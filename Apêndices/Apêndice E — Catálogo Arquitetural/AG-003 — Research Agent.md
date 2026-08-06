# AG-003 — Research Agent

**Identificador:** AG-003

**Nome:** Research Agent

**Versão:** 1.0

**Status:** Oficial

**Camada:** Agent Layer

**Tipo:** Agent Especializado

**Documento:** Apêndice E — Catálogo Arquitetural

**Deriva de:**

- AG-001 — Agent

---

# 1. Objetivo

O Research Agent é responsável por localizar, recuperar, organizar e consolidar informações necessárias para responder a uma solicitação.

Sua função é transformar uma necessidade de conhecimento em um conjunto estruturado de evidências.

O Research Agent não cria conhecimento novo.

Ele trabalha sobre informações existentes.

---

# 2. Responsabilidades

O Research Agent é responsável por:

- interpretar necessidades de pesquisa;
- selecionar as fontes mais adequadas;
- recuperar informações relevantes;
- consolidar múltiplas fontes;
- eliminar informações redundantes;
- organizar evidências;
- devolver conhecimento estruturado aos demais Agents.

---

# 3. Não é Responsável por

O Research Agent nunca deverá:

- gerar código;
- executar OCR;
- realizar cálculos matemáticos;
- produzir documentos finais;
- revisar textos;
- tomar decisões arquiteturais;
- executar Tools diretamente.

---

# 4. Entradas

Recebe exclusivamente uma Message Envelope contendo:

- objetivo da pesquisa;
- contexto;
- restrições;
- domínio;
- permissões;
- trace_id.

---

# 5. Saídas

Produz:

- conjunto de evidências;
- documentos relevantes;
- referências utilizadas;
- resumo estruturado da pesquisa;
- grau de confiança da resposta.

---

# 6. Fluxo de Funcionamento

```text
Receber Objetivo

↓

Analisar Contexto

↓

Selecionar Fontes

↓

Executar Skills de Pesquisa

↓

Consolidar Evidências

↓

Responder ao Agent Solicitante
```

---

# 7. Skills Utilizadas

O Research Agent poderá utilizar, entre outras:

- RAG Skill;
- Vector Search Skill;
- Web Search Skill;
- Document Retrieval Skill;
- Citation Skill;
- Source Ranking Skill.

A seleção depende do objetivo da pesquisa.

---

# 8. Nunca Utiliza Diretamente

O Research Agent nunca acessa diretamente:

- ChromaDB;
- SQLite;
- APIs Web;
- Motores de busca;
- Ferramentas OCR;
- LLMs externos.

Toda interação ocorre por intermédio das Skills.

---

# 9. Fontes de Conhecimento

O Research Agent pode trabalhar com diferentes origens de informação:

## Conhecimento Local

- Base vetorial;
- Banco documental;
- Arquivos Markdown;
- PDFs indexados;
- Memória estruturada.

## Conhecimento Externo

- Pesquisa Web;
- APIs oficiais;
- Documentação pública;
- Repositórios técnicos.

A política de uso de fontes é definida pelo Runtime e pelas permissões da requisição.

---

# 10. Critérios de Qualidade

Toda pesquisa deve priorizar:

- relevância;
- rastreabilidade;
- atualidade (quando aplicável);
- consistência entre fontes;
- transparência sobre a origem das informações.

Quando houver conflito entre fontes, o conflito deve ser explicitado, e não ocultado.

---

# 11. Observabilidade

O Research Agent registra:

- objetivo da pesquisa;
- fontes consultadas;
- Skills utilizadas;
- tempo de execução;
- quantidade de documentos analisados;
- nível de confiança;
- resultado consolidado.

Todos os registros preservam o mesmo `trace_id`.

---

# 12. Exemplo de Execução

Solicitação:

> "Quais documentos da arquitetura descrevem o sistema de comunicação?"

Fluxo:

```text
Runtime

↓

Research Agent

↓

RAG Skill

↓

Vector Search Tool

↓

Document Retrieval Skill

↓

Markdown Parser Tool

↓

Research Agent

↓

Runtime
```

Resultado:

- Contrato de Interfaces;
- Especificação Oficial de Comunicação;
- Livro III — Arquitetura Lógica;
- Apêndice C — Funcionamento.

---

# 13. Relacionamentos

Recebe chamadas de:

- Runtime;
- Planner Agent;
- Documentation Agent;
- Programming Agent;
- Critic Agent.

Comunica-se com:

- Skills de pesquisa.

Nunca comunica-se diretamente com:

- Tools;
- Usuário;
- Banco Vetorial;
- APIs externas.

---

# 14. Critérios de Conformidade

Um Research Agent é considerado compatível quando:

- respeita AG-001;
- utiliza apenas Skills;
- preserva o `trace_id`;
- produz resultados rastreáveis;
- informa as fontes utilizadas;
- não executa Tools diretamente.

---

# 15. Evolução Futura

O Research Agent poderá evoluir para suportar:

- pesquisa híbrida (local + web);
- múltiplos índices vetoriais;
- pesquisa federada;
- reclassificação automática de resultados;
- pesquisa multimodal;
- pesquisa distribuída entre nós do Agent OS.

Essas evoluções não alteram sua responsabilidade arquitetural.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | AG-003 |
| Camada | Agent Layer |
| Tipo | Agent Especializado |
| Decide estratégia de pesquisa | Sim |
| Executa Skills | Coordena |
| Executa Tools | Não |
| Consolida evidências | Sim |
| Mantém `trace_id` | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- AG-001 — Agent
- SK-001 — Skill
- TL-001 — Tool
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia