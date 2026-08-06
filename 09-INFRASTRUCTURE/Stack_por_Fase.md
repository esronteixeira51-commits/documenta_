# Stack Tecnológico por Fase — Agent OS

Versão: 0.1
Status: Draft
Camada: 09-INFRASTRUCTURE / 14-TECHNICAL-SPECS
Depende de: Contrato de Interfaces entre Camadas v0.1

---

# 1. Objetivo

Definir **qual software concreto** roda atrás de cada camada do Agent OS (Core, Runtime, Skills, Tools, Knowledge, Infrastructure) em cada uma das três fases de hardware. A regra de ouro, herdada do Contrato de Interfaces: **a troca de tecnologia nunca deve exigir mudar o Message Envelope, os Agents ou as Skills — só a implementação por trás da Tool ou do transporte.**

Este documento assume o hardware já definido em `Infraestrutura_Projeto.md`:

- **Fase 1**: Ryzen 7 5700G + RTX 5050 8GB + 32GB RAM
- **Fase 2**: Ryzen 9 9950X3D2 + 2× RTX 5090 32GB (64GB total) + 128GB DDR5
- **Fase 3**: Threadripper PRO 9995WX + 4× RTX 6000 Blackwell 96GB (384GB total) + 512GB DDR5

---

# 2. Tabela-resumo por categoria

| Categoria | Fase 1 — Protótipo | Fase 2 — Servidor | Fase 3 — Workstation |
|---|---|---|---|
| Serving de LLM | LM Studio (llama.cpp) | vLLM (tensor parallel 2 GPUs) | vLLM/TensorRT-LLM (tensor parallel 4 GPUs) |
| Roteador de modelos | Manual (você escolhe no LM Studio) | LiteLLM proxy | LiteLLM + política de custo/latência |
| Event Bus / fila | Fila em memória (Python) | Redis Streams | Apache Kafka |
| Orquestração de containers | Docker Compose | Docker Compose (GPU pinning) | k3s (Kubernetes leve) |
| Vector DB | ChromaDB | Qdrant | Qdrant (modo cluster) |
| Banco relacional | SQLite | PostgreSQL | PostgreSQL + réplica |
| Automação low-code | n8n | n8n (via webhook do FastAPI) | n8n (multi-tenant) |
| API interna | FastAPI (básico) | FastAPI (REST + WebSocket completos) | FastAPI + gateway (Kong/Traefik) |
| OCR | Tesseract (CPU) | Tesseract (CPU, worker dedicado) | Tesseract + fallback GPU (docTR) |
| Transcrição de áudio | whisper.cpp (CPU) | faster-whisper (GPU compartilhada) | faster-whisper (GPU dedicada) |
| Busca web self-hosted | SearXNG | SearXNG | SearXNG (cluster) |
| Observabilidade | Logs em SQLite/arquivo | Prometheus + Grafana | Prometheus + Grafana + Loki + Tempo |
| Sandboxing de execução | Docker container simples | Docker + limites de recursos | gVisor / Firecracker microVM |
| Backup | rsync manual → NAS | restic agendado → NAS | restic + réplica offsite |
| Segredos/credenciais | `.env` local | `.env` + Docker secrets | HashiCorp Vault |

---

# 3. Detalhamento por categoria

## 3.1 Serving de LLM

**Por que essa é a decisão mais importante de todas:** é a única peça que muda de *arquitetura interna*, não só de escala, entre as fases — porque o problema técnico muda de natureza.

### Fase 1 — LM Studio
Você já usa e já validou (Qwen3-30B-A3B, DeepSeek-R1-0528-Qwen3-8B, 97% de utilização de GPU na RTX 5050). **Mantenha.** Não há motivo de engenharia pra trocar agora — LM Studio expõe uma API compatível com OpenAI (`http://localhost:1234/v1`), que é exatamente o que o `tool.llm_call` do seu contrato espera. Trocar isso na Fase 1 seria otimização prematura.

**Limitação real que vai te empurrar para a Fase 2:** LM Studio é desenhado para **uma sessão de cada vez**. Se dois Agents tentarem chamar o modelo simultaneamente (ex: Researcher e Math Agent ao mesmo tempo), a segunda chamada fica na fila do processo, não em paralelo de verdade — mesmo que tecnicamente a API aceite a requisição.

### Fase 2 — vLLM
Quando você tem 2 GPUs e múltiplos Agents rodando de verdade em paralelo, o gargalo deixa de ser "cabe na VRAM" e passa a ser "quantas requisições simultâneas o motor de inferência aguenta". É pra isso que existe o **vLLM**: ele usa PagedAttention para servir dezenas de requisições concorrentes sem duplicar o cache de KV por sessão, e suporta paralelismo de tensor nativo entre GPUs.

Exemplo real de como isso muda a config:

```bash
# Fase 1 (LM Studio) — você clica em "Start Server" na GUI, sem tensor parallel

# Fase 2 (vLLM) — serve o mesmo Qwen3, mas agora dividido entre as 2 RTX 5090
python -m vllm.entrypoints.openai.api_server \
  --model Qwen/Qwen3-235B-A22B \
  --tensor-parallel-size 2 \
  --gpu-memory-utilization 0.90 \
  --max-num-seqs 16 \
  --port 8000
```

O `--tensor-parallel-size 2` é a linha que faz o modelo de 235B (que não cabe em uma GPU de 32GB) ser fatiado matematicamente entre as duas RTX 5090. `--max-num-seqs 16` é o que permite até 16 Agents/skills chamando o modelo ao mesmo tempo sem fila — isso é o paralelismo real que a Fase 1 não tinha.

**Importante:** vLLM continua expondo API compatível com OpenAI, então o contrato Skill→Tool que você já desenhou (`tool.llm_call`) **não muda uma linha** — só o endpoint que ele aponta.

### Fase 3 — vLLM (tensor parallel 4) ou TensorRT-LLM
Com 384GB de VRAM, o cenário muda de "1 modelo grande" para "múltiplos modelos grandes simultâneos" (ex: DeepSeek-V3 671B ocupando 3 GPUs + um modelo auxiliar de 70B na quarta GPU, permanentemente residente). Aqui vale considerar migrar as cargas de produção crítica (ex: atendimento de clientes das empresas, se isso acontecer) para **TensorRT-LLM**, que otimiza especificamente para NVIDIA e ganha throughput adicional em troca de mais complexidade de compilação de engine — vale o esforço só quando o volume de requisições justifica.

---

## 3.2 Roteador de modelos — LiteLLM

Você mencionou explorar "multi-model stacks via OpenRouter" — isso se encaixa exatamente aqui. O **LiteLLM** funciona como um proxy único: todas as Skills chamam sempre o mesmo endpoint (`http://litellm:4000/v1`), e é o LiteLLM quem decide, por política, se a requisição vai para:

- vLLM local (modelo de raciocínio principal)
- Um modelo local menor (para tarefas simples, economizando VRAM)
- OpenRouter/Claude API (fallback, só se explicitamente autorizado — o Manifesto diz "serviços externos serão opcionais, nunca obrigatórios")

Exemplo de política real (`litellm_config.yaml`):

```yaml
model_list:
  - model_name: agent-raciocinio
    litellm_params:
      model: openai/Qwen3-235B-A22B
      api_base: http://vllm-server:8000/v1
  - model_name: agent-triagem
    litellm_params:
      model: openai/Qwen3-8B
      api_base: http://vllm-server-small:8001/v1
  - model_name: fallback-nuvem
    litellm_params:
      model: openrouter/anthropic/claude-sonnet-4.5
      api_key: os.environ/OPENROUTER_API_KEY

router_settings:
  routing_strategy: usage-based-routing
  fallbacks: [{"agent-raciocinio": ["fallback-nuvem"]}]
```

Isso introduz o roteador já na **Fase 2**, não na Fase 3, porque é justamente quando você passa a ter mais de um modelo residente (raciocínio + triagem) que faz sentido ter uma camada decidindo qual usar — na Fase 1, com um modelo só, o roteador seria burocracia sem função.

---

## 3.3 Event Bus / Fila de tarefas

Esta é a peça que implementa fisicamente o `trace_id`/`parent_id` do seu Contrato de Interfaces.

### Fase 1 — Fila em memória
Uma lista Python com `asyncio.Queue()` já é suficiente. Não precisa persistência — se o processo cair, você está rodando manualmente mesmo, e reinicia a tarefa.

### Fase 2 — Redis Streams
Quando o Scheduler e os Crons do Core entram em produção de verdade (tarefas automáticas rodando sem você acompanhar ao vivo), você precisa que a fila **sobreviva a um restart** e permita múltiplos "consumers" (vários Agents escutando o mesmo stream de eventos). Redis Streams é a escolha de menor fricção aqui — você provavelmente já vai ter Redis rodando de qualquer forma como cache do LiteLLM.

```python
# Exemplo real: Runtime publicando uma tarefa no stream
import redis
r = redis.Redis(host="localhost", port=6379)

r.xadd("agent_os:tasks", {
    "trace_id": "a1b2c3d4",
    "layer_to": "agent",
    "target_id": "agent.researcher",
    "payload": json.dumps({"objective": "..."})
})
```

### Fase 3 — Apache Kafka
Na Fase 3, o requisito muda: você provavelmente terá múltiplos consumidores independentes do mesmo evento (ex: um Agent processando a tarefa, e simultaneamente um serviço de auditoria/compliance registrando tudo para as empresas), e vai querer **retenção longa e replay** dos eventos — Kafka é desenhado pra isso, Redis Streams não é o ponto forte dele nessa escala. Essa é uma troca real de tecnologia, mas note: o *payload* dentro da mensagem continua sendo exatamente a mesma Message Envelope — só o transporte muda.

---

## 3.4 Vector DB — ChromaDB → Qdrant

Você já usa ChromaDB no Segundo Cérebro. Ele é ótimo para a Fase 1: embutido, sem servidor separado, zero fricção operacional.

**O que quebra em escala:** ChromaDB não foi desenhado para alta concorrência de escrita/leitura simultânea nem para filtragem híbrida complexa (busca vetorial + filtro por metadados em grande volume). Seu próprio blueprint já antecipa isso (`09-INFRASTRUCTURE/Qdrant.md` já existe na estrutura).

**Migração recomendada na Fase 2**, quando o Knowledge_Curator e o Research_Synthesizer Agents passam a rodar em paralelo, gerando escrita concorrente na base vetorial. Qdrant roda como serviço separado (Docker), tem API REST/gRPC, e suporta filtro por payload nativamente — útil pra separar, por exemplo, embeddings da base de matemática dos embeddings do negócio de courier, dentro da mesma instância mas em coleções isoladas.

```python
# Fase 1 (ChromaDB, embutido)
import chromadb
client = chromadb.PersistentClient(path="./chroma_data")
collection = client.get_or_create_collection("historia_matematica")

# Fase 2 (Qdrant, serviço separado)
from qdrant_client import QdrantClient
client = QdrantClient(host="qdrant-server", port=6333)
client.create_collection(
    collection_name="historia_matematica",
    vectors_config={"size": 1024, "distance": "Cosine"}
)
```

Na **Fase 3**, o mesmo Qdrant pode rodar em modo cluster (sharding automático) se o volume total de vetores passar de alguns milhões — improvável no seu caso de uso pessoal, mas relevante se as empresas gerarem grande volume de documentos (notas fiscais, catálogos).

---

## 3.5 Banco relacional — SQLite → PostgreSQL

**Fase 1**: SQLite é a escolha certa — zero setup, um arquivo, perfeito para registrar logs de execução, estado dos Agents, e dados estruturados do courier/loja em baixo volume.

**Fase 2**: o gatilho de migração não é "volume de dados", é **concorrência de escrita**. SQLite trava o arquivo inteiro numa escrita (mesmo em modo WAL, tem limites). Quando você tem múltiplos Agents/Skills gravando estado simultaneamente (ex: Validator atualizando status de uma tarefa enquanto o Documentation Agent grava outra), PostgreSQL resolve isso nativamente com MVCC (controle de concorrência multi-versão).

**Detalhe prático de migração:** se você desenhar o schema desde a Fase 1 usando SQLAlchemy (ORM) em vez de SQL cru, a troca de SQLite→PostgreSQL na Fase 2 vira uma troca de connection string, não uma reescrita de queries.

---

## 3.6 Observabilidade

Esta categoria é onde a Fase 1 mais "engana" — parece opcional, mas é o que vai te dar visibilidade real do gargalo que justifica avançar de fase (lembra do critério de gate que definimos: "gargalo de paralelismo identificado").

**Fase 1**: log estruturado (JSON) em arquivo ou tabela SQLite, incluindo sempre `trace_id`, `latency_ms`, camada. Isso é suficiente pra você, manualmente, identificar que "o Math Agent ficou esperando 4 segundos pela GPU liberar" — já é o sinal de gate pra Fase 2.

**Fase 2**: Prometheus (coleta métricas) + Grafana (dashboards) + `dcgm-exporter` ou `nvidia_gpu_exporter` (utilização/temperatura/VRAM por GPU em tempo real). Isso é essencial porque agora você tem 2 GPUs físicas e precisa saber qual está saturada.

**Fase 3**: adiciona Loki (agregação de logs) e Tempo (tracing distribuído). Aqui o `trace_id` que você já desenhou no Contrato de Interfaces se converte **diretamente** em spans do OpenTelemetry — ou seja, o trabalho de instrumentação que você faz agora na Fase 1 (sempre propagando `trace_id`/`parent_id`) é o que te poupa de reescrever tudo depois. Isso é provavelmente o melhor exemplo concreto de "a arquitetura vale mais que a implementação" no seu próprio projeto.

---

## 3.7 Sandboxing de execução (Tools)

Segurança de execução de código (Princípio 10 do Manifesto: "nunca executar comandos perigosos automaticamente") também escala:

- **Fase 1**: container Docker simples por execução (`docker run --rm --network none python-sandbox`), suficiente para uso pessoal.
- **Fase 2**: mesma abordagem, mas com limites explícitos de CPU/memória/tempo por container (`--memory=2g --cpus=1 --pids-limit=50`), porque agora há múltiplas execuções concorrentes competindo por recursos do servidor.
- **Fase 3**: se o sistema passar a executar código vindo de múltiplos usuários (funcionários) ou processar dados sensíveis das empresas, Docker sozinho não é isolamento suficiente (compartilha kernel com o host). **gVisor** ou **Firecracker** (microVMs) dão isolamento de nível de VM com overhead baixo — é o padrão que a AWS Lambda usa internamente, por exemplo.

---

## 3.8 O que fica igual em todas as fases

Vale nomear explicitamente, porque é a prova de que o contrato está funcionando:

- **Docker/Docker Compose como unidade de empacotamento** — mesmo migrando pra k3s na Fase 3, os containers em si (imagens) não mudam, só como são orquestrados.
- **FastAPI como camada de API** — cresce em escopo (mais rotas, WebSocket), mas o framework não troca.
- **n8n para automação de negócio** (courier/loja) — permanece em todas as fases, só passa a se comunicar com o Agent OS via webhook/API em vez de ficar isolado.
- **Git** como controle de versão de todo o código e da base de conhecimento em Markdown — não escala, mas também nunca deveria mudar.
- **SearXNG** para busca — mesmo componente, só ganha mais réplicas na Fase 3 se o volume de pesquisas justificar.

---

# 4. Ordem de implementação sugerida para a Fase 1 (próximas semanas)

Já que você está no protótipo agora, esta é a ordem que minimiza retrabalho quando migrar:

1. **FastAPI mínimo** envolvendo o LM Studio — não chame o LM Studio direto das Skills; passe por um wrapper FastAPI seu desde já. Isso garante que trocar LM Studio→vLLM na Fase 2 seja transparente pras camadas de cima.
2. **SQLAlchemy sobre SQLite** para todo o estado do Runtime/Core — facilita a troca futura pra PostgreSQL.
3. **Fila em memória, mas já respeitando o formato de mensagem do Redis Streams** (mesmos campos: `trace_id`, `target_id`, `payload` serializado em JSON) — quando trocar pra Redis na Fase 2, é só trocar o transporte, não o formato.
4. **ChromaDB com a mesma estrutura de coleções que Qdrant usaria** (uma coleção por domínio de conhecimento: matemática, courier, eletrônica) — facilita migração 1:1 depois.

---

# 5. Próximos passos

1. Detalhar o `docker-compose.yml` completo da Fase 1, já com todos os serviços (FastAPI, ChromaDB, SearXNG, Tesseract worker) nomeados exatamente como vão se chamar na Fase 2 — isso evita retrabalho de renomear referências.
2. Formalizar o `error_code` de `RESOURCE_EXHAUSTED` (já previsto no Contrato de Interfaces) para dialogar diretamente com o `nvidia_gpu_exporter` da Fase 2.
3. Escrever o `13-STANDARDS/Naming.md` definindo a convenção `categoria.nome` usada em `target_id` (ex: `agent.researcher`, `skill.rag_search`, `tool.sympy`) antes que o número de Skills cresça e a nomenclatura fique inconsistente.