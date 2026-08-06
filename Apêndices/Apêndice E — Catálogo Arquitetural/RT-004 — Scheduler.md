# RT-004 — Scheduler

**Identificador:** RT-004

**Nome:** Scheduler

**Versão:** 1.0

**Status:** Oficial

**Camada:** Runtime Layer

**Tipo:** Componente de Agendamento

**Documento:** Apêndice E — Catálogo Arquitetural

**Relacionado a:**

- RT-001 — Runtime
- RT-002 — Planner
- RT-003 — Dispatcher
- RT-005 — Workflow Engine
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia

---

# 1. Objetivo

O Scheduler é responsável por determinar quando uma etapa do Workflow poderá ser executada.

Seu objetivo é organizar a ordem de execução das tarefas considerando dependências, disponibilidade de recursos e políticas definidas pelo Runtime.

O Scheduler não executa tarefas e não altera o Workflow.

Sua função é exclusivamente controlar o momento da execução.

---

# 2. Responsabilidades

O Scheduler é responsável por:

- controlar a ordem de execução das etapas;
- verificar dependências entre tarefas;
- liberar tarefas aptas para execução;
- controlar filas de execução;
- aplicar prioridades definidas pelo Runtime;
- informar ao Dispatcher quais tarefas podem ser despachadas.

---

# 3. Não é Responsável por

O Scheduler nunca deverá:

- modificar o Workflow;
- decidir estratégias de resolução;
- executar Agents;
- executar Skills;
- executar Tools;
- alterar permissões;
- interpretar resultados;
- produzir respostas ao usuário.

---

# 4. Entradas

O Scheduler recebe:

- Workflow produzido pelo Planner;
- estado atual da execução;
- dependências entre etapas;
- políticas de agendamento;
- Message Envelope válida.

---

# 5. Saídas

O Scheduler produz:

- lista de tarefas liberadas para execução;
- ordem de execução;
- eventos de agendamento;
- atualizações do estado do Workflow.

---

# 6. Fluxo de Funcionamento

```text
Receber Workflow

↓

Analisar Dependências

↓

Verificar Recursos

↓

Aplicar Prioridades

↓

Liberar Próxima Etapa

↓

Notificar Dispatcher

↓

Atualizar Estado
```

---

# 7. Dependências

O Scheduler depende de:

- RT-001 — Runtime
- RT-003 — Dispatcher
- RT-005 — Workflow Engine
- Message Envelope

Não depende de:

- modelos de IA;
- banco de dados;
- infraestrutura física;
- ferramentas específicas.

---

# 8. Contratos Utilizados

Toda comunicação utiliza exclusivamente a Message Envelope.

Cada decisão de agendamento deve preservar:

- `trace_id`
- `parent_id`
- contexto
- permissões
- metadados

---

# 9. Invariantes Arquiteturais

O Scheduler deve preservar as seguintes regras:

1. Nenhuma tarefa é executada antes de suas dependências.
2. O Workflow nunca é alterado.
3. O `trace_id` permanece inalterado.
4. Apenas tarefas liberadas podem ser despachadas.
5. O Scheduler nunca executa lógica de domínio.
6. Toda decisão de agendamento é registrada para auditoria.

---

# 10. Estados

```text
Idle

↓

Receiving

↓

Analyzing

↓

Scheduling

↓

Waiting

↓

Scheduling

↓

Completed
```

Em caso de falha:

```text
Scheduling

↓

Error

↓

Notify Runtime

↓

Completed
```

---

# 11. Falhas Previstas

O Scheduler deve tratar:

- dependências inválidas;
- ciclo entre tarefas;
- fila inconsistente;
- recurso indisponível;
- timeout de espera;
- Workflow inválido.

Toda falha deve ser comunicada ao Runtime.

---

# 12. Observabilidade

O Scheduler registra:

- ordem de execução;
- tempo de espera;
- tarefas bloqueadas;
- tarefas liberadas;
- prioridades aplicadas;
- eventos de fila;
- conclusão do agendamento.

Todos os registros preservam o mesmo `trace_id`.

---

# 13. Exemplo de Execução

Workflow:

```text
1. OCR

↓

2. Indexação

↓

3. Pesquisa

↓

4. Resumo

↓

5. Revisão
```

O Scheduler verifica que a Pesquisa depende da Indexação.

Enquanto a Indexação não for concluída, a Pesquisa permanece bloqueada.

Após a conclusão da Indexação, o Scheduler libera automaticamente a próxima etapa para o Dispatcher.

---

# 14. Relacionamentos

Recebe chamadas de:

- RT-001 — Runtime
- RT-003 — Dispatcher
- RT-005 — Workflow Engine

Interage com:

- Dispatcher

Nunca comunica-se diretamente com:

- Agents
- Skills
- Tools
- Presentation Layer
- Usuário

---

# 15. Critérios de Conformidade

Uma implementação do Scheduler é considerada compatível quando:

- respeita as dependências do Workflow;
- não modifica o Workflow;
- utiliza exclusivamente Message Envelope;
- preserva `trace_id`;
- controla filas de execução;
- registra decisões de agendamento;
- não executa lógica de domínio.

---

# 16. Evolução Futura

O Scheduler poderá evoluir para suportar:

- execução paralela;
- múltiplas filas;
- prioridades dinâmicas;
- escalonamento distribuído;
- balanceamento entre nós;
- políticas de qualidade de serviço (QoS).

Essas evoluções não alteram sua responsabilidade arquitetural.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | RT-004 |
| Camada | Runtime Layer |
| Tipo | Agendamento |
| Planeja | Não |
| Despacha | Não |
| Agenda execução | Sim |
| Mantém `trace_id` | Sim |
| Modifica Workflow | Não |
| Contrato oficial | Message Envelope |

---

# Referências

- RT-001 — Runtime
- RT-002 — Planner
- RT-003 — Dispatcher
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia