# SK-001 — Skill

**Identificador:** SK-001

**Nome:** Skill

**Versão:** 1.0

**Status:** Oficial

**Camada:** Skill Layer

**Tipo:** Componente Abstrato

**Documento:** Apêndice E — Catálogo Arquitetural

**Relacionado a:**

- AG-001 — Agent
- TL-001 — Tool
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia

---

# 1. Objetivo

A Skill representa uma capacidade operacional especializada do Agent OS.

Sua função é executar uma operação bem definida utilizando uma ou mais Tools, conforme solicitado por um Agent.

A Skill conhece o processo necessário para resolver um tipo específico de tarefa.

Ela não toma decisões estratégicas e não executa lógica de domínio além da sua especialidade.

---

# 2. Responsabilidades

A Skill é responsável por:

- interpretar a solicitação recebida do Agent;
- validar os parâmetros necessários para a operação;
- selecionar as Tools adequadas;
- organizar a sequência de chamadas às Tools;
- consolidar os resultados produzidos pelas Tools;
- retornar um resultado estruturado ao Agent.

---

# 3. Não é Responsável por

A Skill nunca deverá:

- decidir qual estratégia geral resolverá o problema;
- substituir o Agent na tomada de decisão;
- modificar o Workflow;
- executar planejamento;
- acessar diretamente a interface do usuário;
- alterar permissões da requisição.

---

# 4. Entradas

A Skill recebe exclusivamente uma Message Envelope contendo:

- objetivo operacional;
- parâmetros estruturados;
- contexto;
- permissões;
- trace_id.

---

# 5. Saídas

A Skill produz:

- chamadas para Tools;
- resultados estruturados;
- mensagens de erro padronizadas;
- eventos de observabilidade.

---

# 6. Fluxo de Funcionamento

```text
Receber Solicitação

↓

Validar Entrada

↓

Selecionar Tool(s)

↓

Executar Operações

↓

Consolidar Resultado

↓

Responder ao Agent
```

---

# 7. Dependências

A Skill depende de:

- AG-001 — Agent;
- TL-001 — Tool;
- Message Envelope.

Não depende diretamente de:

- Runtime;
- Scheduler;
- Workflow Engine;
- Interface do usuário.

---

# 8. Contratos Utilizados

Toda comunicação utiliza exclusivamente a Message Envelope.

A Skill comunica-se com:

- Agents;
- Tools.

Nunca comunica-se diretamente com:

- Runtime;
- Usuário;
- Presentation Layer.

---

# 9. Invariantes Arquiteturais

A Skill deve garantir que:

1. Toda operação utilize Message Envelope.
2. Apenas Tools sejam executadas.
3. O `trace_id` seja preservado.
4. O contexto recebido seja mantido durante toda a execução.
5. Toda resposta seja estruturada.
6. Nenhuma decisão estratégica seja tomada pela Skill.

---

# 10. Estados

```text
Idle

↓

Receiving

↓

Validating

↓

Executing Tools

↓

Consolidating

↓

Completed
```

Em caso de falha:

```text
Executing

↓

Failed

↓

Return Error
```

---

# 11. Falhas Previstas

A Skill deve tratar:

- parâmetros inválidos;
- Tool inexistente;
- Tool indisponível;
- timeout;
- erro retornado pela Tool;
- resultado inconsistente.

Toda falha deve utilizar o formato oficial definido na Message Envelope.

---

# 12. Observabilidade

A Skill registra:

- início da execução;
- Tools utilizadas;
- duração de cada operação;
- erros;
- resultado final.

Todos os registros preservam o mesmo `trace_id`.

---

# 13. Exemplo de Execução

Solicitação recebida:

> "Extrair texto deste PDF."

Fluxo:

```text
Documentation Agent

↓

OCR Skill

↓

OCR Tool

↓

Texto Extraído

↓

Documentation Agent
```

Outro exemplo:

```text
Research Agent

↓

RAG Skill

↓

Embedding Tool

↓

Vector Search Tool

↓

Research Agent
```

A Skill coordena as Tools necessárias, mas não toma decisões de domínio.

---

# 14. Relacionamentos

Recebe chamadas de:

- Agents.

Comunica-se com:

- Tools.

Nunca comunica-se diretamente com:

- Runtime;
- Scheduler;
- Workflow Engine;
- Usuário.

---

# 15. Critérios de Conformidade

Uma Skill é considerada compatível quando:

- utiliza apenas Message Envelope;
- comunica-se exclusivamente com Tools;
- preserva o `trace_id`;
- retorna respostas estruturadas;
- não executa planejamento;
- não toma decisões estratégicas.

---

# 16. Evolução Futura

Novas Skills podem ser adicionadas sem alterar Agents ou Tools.

Exemplos:

- OCR Skill;
- RAG Skill;
- Summarization Skill;
- Translation Skill;
- Coding Skill;
- Mathematical Verification Skill;
- Image Analysis Skill;
- Speech Processing Skill.

Todas devem respeitar esta especificação base.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | SK-001 |
| Camada | Skill Layer |
| Tipo | Componente Abstrato |
| Coordena Tools | Sim |
| Executa Tools | Sim (por orquestração) |
| Decide estratégia | Não |
| Mantém `trace_id` | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- AG-001 — Agent
- TL-001 — Tool
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia