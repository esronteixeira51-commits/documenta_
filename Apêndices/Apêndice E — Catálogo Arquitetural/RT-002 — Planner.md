# RT-002 — Planner

**Identificador:** RT-002

**Nome:** Planner

**Versão:** 1.0

**Status:** Oficial

**Camada:** Runtime Layer

**Tipo:** Componente de Planejamento

**Documento:** Apêndice E — Catálogo Arquitetural

**Relacionado a:**

- RT-001 — Runtime
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia

---

# 1. Objetivo

O Planner é responsável por transformar um objetivo recebido pelo Runtime em um plano estruturado de execução.

Sua função é decompor uma tarefa complexa em etapas menores, preservando as restrições, permissões e contexto da requisição.

O Planner não executa tarefas.

Ele apenas define como elas deverão ser organizadas.

---

# 2. Responsabilidades

O Planner é responsável por:

- analisar o objetivo da tarefa;
- identificar subtarefas;
- organizar a sequência de execução;
- determinar dependências entre etapas;
- produzir um Workflow inicial;
- encaminhar o plano ao Dispatcher.

---

# 3. Não é Responsável por

O Planner nunca deverá:

- executar Skills;
- executar Tools;
- consultar bancos de dados;
- chamar modelos diretamente;
- produzir respostas ao usuário;
- decidir qual Tool será utilizada.

Essas responsabilidades pertencem a outros componentes.

---

# 4. Entradas

Recebe exclusivamente uma Message Envelope válida contendo:

- objetivo;
- contexto;
- restrições;
- permissões;
- trace_id.

---

# 5. Saídas

O Planner produz:

- Workflow inicial;
- lista ordenada de subtarefas;
- dependências entre etapas;
- Envelope destinada ao Dispatcher.

---

# 6. Fluxo de Funcionamento

```text
Receber Objetivo

↓

Analisar Objetivo

↓

Identificar Etapas

↓

Organizar Sequência

↓

Gerar Workflow

↓

Enviar ao Dispatcher
```

---

# 7. Dependências

O Planner depende:

- Runtime;
- Contrato da Message Envelope.

Não depende de:

- modelos específicos;
- infraestrutura;
- bancos de dados;
- Tools.

---

# 8. Contratos Utilizados

Toda comunicação utiliza exclusivamente a Message Envelope.

Nenhum contrato alternativo é permitido.

---

# 9. Invariantes Arquiteturais

O Planner deve preservar:

- toda tarefa gera um Workflow;
- nenhuma etapa é executada diretamente;
- o trace_id permanece inalterado;
- o planejamento é determinístico para um mesmo conjunto de entradas e políticas;
- o Planner nunca chama Tools.

---

# 10. Estados

```text
Idle

↓

Receiving

↓

Analyzing

↓

Planning

↓

Generating Workflow

↓

Completed
```

---

# 11. Falhas Previstas

O Planner deve tratar:

- objetivo inválido;
- contexto insuficiente;
- restrições incompatíveis;
- Workflow impossível de construir.

Essas situações devem retornar erro padronizado ao Runtime.

---

# 12. Observabilidade

O Planner registra:

- início do planejamento;
- duração;
- quantidade de subtarefas geradas;
- dependências identificadas;
- falhas de planejamento;
- Workflow produzido.

Todos os eventos preservam o mesmo trace_id.

---

# 13. Exemplo de Execução

Solicitação:

> "Analise este PDF e produza um resumo."

Workflow produzido:

```text
1. Extrair texto

↓

2. Recuperar contexto

↓

3. Organizar conteúdo

↓

4. Revisar

↓

5. Entregar resposta
```

O Planner apenas produz o plano.

Nenhuma dessas etapas é executada por ele.

---

# 14. Relacionamentos

Entrada:

- RT-001 — Runtime

Saída:

- RT-003 — Dispatcher

Nunca comunica-se diretamente com:

- Agents;
- Skills;
- Tools;
- Banco Vetorial;
- LLMs.

---

# 15. Critérios de Conformidade

Uma implementação do Planner é considerada compatível quando:

- transforma objetivos em Workflows;
- não executa tarefas;
- preserva o trace_id;
- utiliza Message Envelope;
- respeita as restrições recebidas;
- mantém separação entre planejamento e execução.

---

# 16. Evolução Futura

O Planner poderá evoluir para suportar:

- planejamento paralelo;
- otimização baseada em custo;
- planejamento adaptativo;
- planejamento distribuído;
- reutilização de planos recorrentes;
- cache de Workflows.

Essas evoluções não alteram sua responsabilidade arquitetural.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | RT-002 |
| Camada | Runtime Layer |
| Tipo | Planejador |
| Executa tarefas | Não |
| Gera Workflow | Sim |
| Coordena execução | Não |
| Mantém trace_id | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- RT-001 — Runtime
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia