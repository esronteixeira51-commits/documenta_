# SK-001 — Skill

**Identificador:** SK-001

**Nome:** Skill

**Versão:** 1.0

**Status:** Oficial

**Camada:** Skill Layer

**Tipo:** Componente Base

**Documento:** Apêndice E — Catálogo Arquitetural

**Deriva de:**

- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- AG-001 — Agent

---

# 1. Objetivo

A Skill é o componente responsável por implementar uma capacidade específica do Agent OS.

Enquanto o Agent decide **o que deve ser feito**, a Skill define **como aquela capacidade será executada**, coordenando uma ou mais Tools para produzir um resultado.

A Skill representa a camada de lógica operacional do sistema.

---

# 2. Papel na Arquitetura

A Skill ocupa a camada intermediária entre Agents e Tools.

```text
Runtime

↓

Planner

↓

Agent

↓

Skill

↓

Tool
```

Essa posição garante o desacoplamento entre tomada de decisão e execução.

---

# 3. Responsabilidades

Uma Skill é responsável por:

- implementar uma única capacidade;
- transformar objetivos em operações;
- validar parâmetros recebidos;
- selecionar as Tools adequadas;
- controlar a sequência de execução;
- consolidar resultados;
- tratar falhas operacionais;
- retornar resultados padronizados.

---

# 4. Não é Responsável por

Uma Skill nunca deverá:

- interpretar objetivos do usuário;
- decidir estratégias globais;
- executar planejamento;
- executar operações diretamente sem utilizar Tools;
- acessar interfaces de usuário;
- modificar a arquitetura do sistema.

Essas responsabilidades pertencem a outras camadas.

---

# 5. Princípio Fundamental

Toda Skill implementa exatamente uma capacidade.

Exemplos:

- pesquisar documentos;
- gerar embeddings;
- executar OCR;
- validar expressões matemáticas;
- interpretar diagramas;
- executar código Python.

Nunca múltiplas capacidades ao mesmo tempo.

---

# 6. Estrutura Geral

Toda Skill possui:

- identificador único;
- descrição;
- capacidade implementada;
- entradas;
- saídas;
- dependências;
- Tools suportadas;
- políticas de erro;
- critérios de sucesso.

---

# 7. Fluxo de Funcionamento

```text
Receber Request

↓

Validar Entrada

↓

Selecionar Ferramentas

↓

Executar Ferramentas

↓

Consolidar Resultados

↓

Validar Resultado

↓

Retornar Response
```

---

# 8. Comunicação

Toda Skill comunica-se exclusivamente através da Message Envelope oficial.

Recebe:

- request

Retorna:

- result
- error
- pending_confirmation

Nunca utiliza formatos próprios de comunicação.

---

# 9. Relação com Agents

Uma Skill nunca toma decisões arquiteturais.

O fluxo correto é sempre:

```text
Agent

↓

Skill

↓

Tool
```

Jamais:

```text
Agent

↓

Tool
```

Nem:

```text
Tool

↓

Skill
```

A hierarquia é obrigatória.

---

# 10. Relação com Tools

Uma Skill pode utilizar:

- uma Tool;
- várias Tools;
- nenhuma Tool (em casos excepcionais de lógica puramente estrutural).

As Tools nunca conhecem a Skill que as chamou.

O acoplamento é unidirecional.

---

# 11. Princípios de Engenharia

Toda Skill deve obedecer aos seguintes princípios.

## Responsabilidade Única

Uma Skill implementa apenas uma capacidade.

---

## Determinismo

Mesmas entradas devem produzir resultados equivalentes, salvo dependências externas explicitadas.

---

## Reutilização

Uma Skill pode ser utilizada por diversos Agents.

---

## Baixo Acoplamento

A Skill não depende da implementação interna das Tools.

---

## Alta Coesão

Toda lógica relacionada à capacidade deve permanecer concentrada na própria Skill.

---

## Observabilidade

Toda execução deve produzir registros rastreáveis.

---

# 12. Tratamento de Erros

Toda Skill deve:

- validar entradas;
- tratar exceções;
- produzir Error Envelope;
- utilizar ErrorCode oficial;
- nunca lançar exceções não tratadas para a camada superior.

---

# 13. Observabilidade

Toda execução registra:

- trace_id;
- skill_id;
- tool utilizada;
- duração;
- resultado;
- falhas;
- retries;
- versão.

---

# 14. Critérios de Conformidade

Uma Skill é considerada compatível quando:

- respeita o Message Envelope;
- implementa apenas uma capacidade;
- utiliza Tools quando necessário;
- preserva trace_id;
- produz Result Envelope;
- utiliza ErrorCode oficial;
- mantém baixo acoplamento.

---

# 15. Evolução

Novas Skills poderão ser adicionadas sem alterar:

- Runtime;
- Planner;
- Agents;
- demais Skills.

A arquitetura deve crescer por composição, nunca por substituição de responsabilidades existentes.

---

# 16. Exemplo Arquitetural

```text
Research Agent

↓

RAG Search Skill

↓

Embedding Tool

↓

Vector Database Tool

↓

Resultado

↓

Research Agent
```

Outro exemplo:

```text
Programming Agent

↓

Python Execution Skill

↓

Python Sandbox Tool

↓

Resultado

↓

Programming Agent
```

---

# 17. Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | SK-001 |
| Camada | Skill Layer |
| Tipo | Componente Base |
| Implementa capacidades | Sim |
| Coordena Tools | Sim |
| Decide arquitetura | Não |
| Executa planejamento | Não |
| Mantém trace_id | Sim |
| Contrato oficial | Message Envelope |

---

# 18. Filosofia da Camada Skill

A camada Skill representa o conhecimento operacional do Agent OS.

Os Agents sabem **o que fazer**.

As Skills sabem **como fazer**.

As Tools sabem **executar**.

Essa separação reduz o acoplamento, facilita testes, permite reutilização de capacidades e garante que novas funcionalidades possam ser incorporadas sem alterar a arquitetura existente.

---

# Referências

- AG-001 — Agent
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia




------------------------


---

# Apêndice E — Parte II

# Catálogo Oficial de Skills

Versão: 1.0

Status: Oficial

---

# Introdução

A camada **Skill** representa a camada operacional do Agent OS.

Enquanto os Agents definem **o que deve ser feito**, as Skills implementam **como cada capacidade é executada**, coordenando uma ou mais Tools para produzir um resultado padronizado.

Cada Skill possui responsabilidade única e pode ser reutilizada por diferentes Agents.

As Skills são organizadas por domínio funcional para facilitar evolução, manutenção e auditoria arquitetural.

---

# SK-100 — Pesquisa e Conhecimento

## Objetivo

Implementar todas as capacidades relacionadas à aquisição, organização, validação e recuperação de conhecimento.

## Responsabilidades

* pesquisa na internet;
* pesquisa semântica (RAG);
* análise documental;
* consolidação de múltiplas fontes;
* geração de embeddings;
* indexação;
* validação de fontes;
* geração de citações;
* montagem de contexto.

## Utilizada principalmente por

* Research Agent;
* Documentation Agent;
* Memory Agent;
* Architecture Evolution Agent.

## Principais Tools

* Web Search Tool;
* ChromaDB Tool;
* Embedding Tool;
* Parser Tool.

---

# SK-200 — Engenharia e Desenvolvimento

## Objetivo

Implementar capacidades relacionadas ao desenvolvimento de software.

## Responsabilidades

* geração de código;
* análise de código;
* refatoração;
* geração de testes;
* documentação técnica;
* análise de dependências;
* execução de builds;
* depuração.

## Utilizada principalmente por

* Programming Agent;
* Critic Agent.

## Principais Tools

* Python Tool;
* Git Tool;
* Docker Tool;
* Build Tool.

---

# SK-300 — Percepção

## Objetivo

Implementar capacidades de interpretação de informações visuais e sonoras.

## Responsabilidades

* OCR;
* análise de imagens;
* interpretação de diagramas;
* reconhecimento de objetos;
* análise de layouts;
* transcrição de áudio;
* identificação de falantes;
* classificação de áudio.

## Utilizada principalmente por

* Vision Agent;
* Audio Agent;
* Research Agent.

## Principais Tools

* OCR Tool;
* Vision Model Tool;
* Whisper Tool;
* Audio Processing Tool.

---

# SK-400 — Memória e Persistência

## Objetivo

Gerenciar armazenamento, recuperação e organização do conhecimento.

## Responsabilidades

* armazenamento;
* recuperação;
* atualização;
* consolidação;
* busca vetorial;
* cache de contexto;
* versionamento.

## Utilizada principalmente por

* Memory Agent;
* Research Agent;
* Documentation Agent.

## Principais Tools

* ChromaDB Tool;
* SQLite Tool;
* PostgreSQL Tool;
* Redis Tool.

---

# SK-500 — Coordenação e Workflow

## Objetivo

Implementar capacidades relacionadas à execução coordenada de tarefas.

## Responsabilidades

* coordenação de workflows;
* agendamento;
* resolução de dependências;
* agregação de resultados;
* monitoramento;
* roteamento;
* gerenciamento de retries.

## Utilizada principalmente por

* Coordinator Agent;
* Planner Agent.

## Principais Tools

* Scheduler Tool;
* Queue Tool;
* EventBus Tool.

---

# SK-600 — Segurança e Permissões

## Objetivo

Garantir que todas as operações respeitem as políticas de segurança do Agent OS.

## Responsabilidades

* validação de permissões;
* aplicação de políticas;
* confirmação humana;
* validação de sandbox;
* sanitização de entradas e saídas;
* gerenciamento de credenciais.

## Utilizada principalmente por

Todos os Agents.

## Principais Tools

* Permission Tool;
* Sandbox Tool;
* Secret Manager Tool.

---

# SK-700 — Integração

## Objetivo

Permitir comunicação do Agent OS com sistemas externos.

## Responsabilidades

* APIs REST;
* GraphQL;
* bancos de dados;
* sistema de arquivos;
* Git;
* Docker;
* MCP;
* Plugins.

## Utilizada principalmente por

* Programming Agent;
* Research Agent;
* Memory Agent.

## Principais Tools

* REST Tool;
* Git Tool;
* Docker Tool;
* File System Tool.

---

# SK-800 — Skills Especializadas

## Objetivo

Reservar espaço para capacidades específicas de determinados domínios.

## Exemplos

* engenharia;
* medicina;
* direito;
* CAD;
* robótica;
* finanças;
* GIS;
* ciência.

Essas Skills não fazem parte do núcleo do Agent OS.

---

# SK-900 — Skills Internas

## Objetivo

Implementar funcionalidades de suporte interno da plataforma.

## Responsabilidades

* logging;
* métricas;
* health checks;
* configuração;
* feature flags;
* validação de schemas;
* despacho de eventos.

## Utilizada principalmente por

Infraestrutura do Runtime.

---

# Organização da Camada Skill

| Faixa      | Domínio                      | Objetivo                                         |
| ---------- | ---------------------------- | ------------------------------------------------ |
| SK-001–099 | Base                         | Definições fundamentais da camada Skill          |
| SK-100–199 | Pesquisa e Conhecimento      | Aquisição, análise e organização de conhecimento |
| SK-200–299 | Engenharia e Desenvolvimento | Desenvolvimento de software                      |
| SK-300–399 | Percepção                    | Visão computacional e processamento de áudio     |
| SK-400–499 | Memória e Persistência       | Armazenamento e recuperação de conhecimento      |
| SK-500–599 | Coordenação e Workflow       | Orquestração de tarefas                          |
| SK-600–699 | Segurança e Permissões       | Políticas e proteção do sistema                  |
| SK-700–799 | Integração                   | Comunicação com sistemas externos                |
| SK-800–899 | Especializadas               | Capacidades específicas de domínio               |
| SK-900–999 | Internas                     | Infraestrutura da plataforma                     |

---

# Princípios Arquiteturais

Toda Skill deve obedecer aos seguintes princípios:

* implementar **uma única capacidade**;
* comunicar-se exclusivamente através da **Message Envelope**;
* utilizar Tools para executar operações determinísticas;
* preservar o `trace_id` durante toda a execução;
* manter baixo acoplamento e alta coesão;
* ser reutilizável por múltiplos Agents;
* produzir resultados padronizados.

---

# Relação Arquitetural

```text
Runtime
   │
   ▼
Planner Agent
   │
   ▼
Agent
   │
   ▼
Skill
   │
   ▼
Tool
```

As Skills representam a camada operacional do Agent OS, fazendo a ponte entre as decisões dos Agents e a execução realizada pelas Tools.

---

