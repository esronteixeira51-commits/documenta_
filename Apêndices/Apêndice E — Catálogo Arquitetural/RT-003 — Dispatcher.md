# RT-003 — Dispatcher

**Identificador:** RT-003

**Nome:** Dispatcher

**Versão:** 1.0

**Status:** Oficial

**Camada:** Runtime Layer

**Tipo:** Componente de Despacho

**Documento:** Apêndice E — Catálogo Arquitetural

**Relacionado a:**

- RT-001 — Runtime
- RT-002 — Planner
- RT-004 — Scheduler
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia

---

# 1. Objetivo

O Dispatcher é responsável por transformar um Workflow planejado em chamadas reais aos componentes da arquitetura.

Sua função é encaminhar cada etapa do Workflow ao componente apropriado, preservando os contratos, permissões e contexto definidos pelo Runtime.

O Dispatcher não toma decisões estratégicas.

Ele apenas executa o plano produzido pelo Planner.

---

# 2. Responsabilidades

O Dispatcher é responsável por:

- Receber o Workflow produzido pelo Planner.
- Identificar o componente responsável por cada etapa.
- Encaminhar cada tarefa ao destino correto.
- Preservar o `trace_id` e o `parent_id`.
- Garantir que todas as chamadas utilizem a Message Envelope.
- Registrar os despachos realizados.
- Informar o Runtime sobre o andamento da execução.

---

# 3. Não é Responsável por

O Dispatcher nunca deverá:

- Planejar tarefas.
- Alterar um Workflow.
- Executar lógica de domínio.
- Escolher estratégias de resolução.
- Executar Skills.
- Executar Tools.
- Produzir respostas ao usuário.
- Modificar permissões da requisição.

---

# 4. Entradas

O Dispatcher recebe exclusivamente uma **Message Envelope** contendo:

- Workflow produzido pelo Planner.
- Objetivo da tarefa.
- Contexto.
- Permissões.
- `trace_id`.
- `parent_id`.

---

# 5. Saídas

O Dispatcher produz:

- Envelopes destinadas aos componentes responsáveis.
- Eventos de despacho.
- Atualizações do estado do Workflow.
- Relatórios de execução para o Runtime.

---

# 6. Fluxo de Funcionamento

```text
Receber Workflow

↓

Selecionar Próxima Etapa

↓

Identificar Destino

↓

Criar Envelope

↓

Despachar

↓

Registrar Evento

↓

Aguardar Resultado

↓

Próxima Etapa
```

O ciclo continua até que todas as etapas do Workflow tenham sido executadas ou o Runtime determine sua interrupção.

---

# 7. Dependências

O Dispatcher depende de:

- RT-001 — Runtime
- RT-002 — Planner
- Message Envelope

Não depende de:

- Modelos de IA.
- Banco de dados.
- Frameworks.
- Ferramentas específicas.

---

# 8. Contratos Utilizados

Toda comunicação é realizada por meio da **Message Envelope**.

Cada despacho deve conter:

- `trace_id`
- `parent_id`
- `layer_from`
- `layer_to`
- `target_id`
- `payload`
- `context`
- `permissions`
- `meta`

O Dispatcher nunca envia mensagens fora desse contrato.

---

# 9. Invariantes Arquiteturais

O Dispatcher deve preservar as seguintes regras:

1. Toda etapa do Workflow é despachada utilizando Message Envelope.
2. Nenhuma etapa é alterada durante o despacho.
3. O `trace_id` permanece inalterado.
4. O `parent_id` identifica corretamente a chamada imediatamente anterior.
5. Apenas componentes autorizados recebem tarefas.
6. O Dispatcher nunca executa lógica de domínio.

Essas regras não podem ser violadas.

---

# 10. Estados

Durante sua operação o Dispatcher pode assumir os seguintes estados:

```text
Idle

↓

Receiving

↓

Dispatching

↓

Waiting

↓

Dispatching

↓

Completed
```

Em caso de falha:

```text
Dispatching

↓

Error

↓

Notify Runtime

↓

Completed
```

---

# 11. Falhas Previstas

O Dispatcher deve tratar, no mínimo:

- Workflow inválido.
- Destino inexistente.
- Componente indisponível.
- Permissão insuficiente.
- Timeout de despacho.
- Envelope inválida.

Toda falha deve ser registrada e comunicada ao Runtime.

---

# 12. Observabilidade

O Dispatcher registra:

- início do despacho;
- componente de destino;
- quantidade de etapas executadas;
- tempo entre despachos;
- falhas de roteamento;
- conclusão do Workflow.

Todos os eventos preservam o mesmo `trace_id`.

---

# 13. Exemplo de Execução

Workflow recebido:

```text
1. Pesquisa

2. OCR

3. Organização

4. Revisão

5. Resposta
```

Execução:

```text
Dispatcher

↓

Research Agent

↓

OCR Skill

↓

Documentation Agent

↓

Critic

↓

Runtime
```

O Dispatcher apenas encaminha cada etapa ao componente correspondente.

---

# 14. Relacionamentos

Recebe chamadas de:

- RT-001 — Runtime
- RT-002 — Planner

Encaminha chamadas para:

- Scheduler
- Agents
- Serviços internos definidos pelo Workflow

Nunca comunica-se diretamente com:

- Usuário
- Presentation Layer
- Banco Vetorial
- Banco Relacional
- Ferramentas físicas

As Tools são sempre acessadas por intermédio de Skills.

---

# 15. Critérios de Conformidade

Uma implementação do Dispatcher é considerada compatível quando:

- preserva o Workflow recebido;
- utiliza exclusivamente Message Envelope;
- mantém `trace_id` e `parent_id`;
- encaminha tarefas ao destino correto;
- não altera permissões;
- não executa lógica de domínio;
- registra eventos de despacho.

---

# 16. Evolução Futura

O Dispatcher poderá evoluir para suportar:

- despacho paralelo;
- balanceamento entre múltiplos Agents;
- roteamento baseado em capacidade;
- priorização dinâmica;
- filas distribuídas;
- execução em múltiplos nós.

Essas evoluções não alteram sua responsabilidade arquitetural.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | RT-003 |
| Camada | Runtime Layer |
| Tipo | Despacho |
| Planeja | Não |
| Executa lógica | Não |
| Encaminha tarefas | Sim |
| Preserva Workflow | Sim |
| Mantém `trace_id` | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- RT-001 — Runtime
- RT-002 — Planner
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia