# RT-006 — Event Bus

**Identificador:** RT-006

**Nome:** Event Bus

**Versão:** 1.0

**Status:** Oficial

**Camada:** Runtime Layer

**Tipo:** Barramento de Eventos

**Documento:** Apêndice E — Catálogo Arquitetural

**Relacionado a:**

- RT-001 — Runtime
- RT-002 — Planner
- RT-003 — Dispatcher
- RT-004 — Scheduler
- RT-005 — Workflow Engine
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica

---

# 1. Objetivo

O Event Bus é responsável pela distribuição de eventos internos do Agent OS.

Seu objetivo é permitir que componentes compartilhem informações sem depender diretamente uns dos outros.

O Event Bus não executa lógica de negócio.

Ele apenas transporta eventos entre produtores e consumidores.

---

# 2. Responsabilidades

O Event Bus é responsável por:

- receber eventos publicados;
- distribuir eventos aos componentes interessados;
- preservar a ordem de publicação quando necessário;
- registrar eventos para observabilidade;
- desacoplar produtores e consumidores de eventos.

---

# 3. Não é Responsável por

O Event Bus nunca deverá:

- executar tarefas;
- interpretar eventos;
- modificar mensagens;
- tomar decisões;
- executar Agents;
- executar Skills;
- executar Tools;
- armazenar conhecimento de domínio.

---

# 4. Entradas

O Event Bus recebe exclusivamente eventos internos publicados pelos componentes da arquitetura.

Exemplos:

- WorkflowCreated
- WorkflowCompleted
- TaskStarted
- TaskCompleted
- TaskFailed
- AgentSelected
- SkillExecuted
- ToolExecuted

Todos os eventos devem ser transportados por uma Message Envelope.

---

# 5. Saídas

O Event Bus entrega eventos para:

- Runtime
- Workflow Engine
- Scheduler
- Monitoramento
- Auditoria
- Componentes inscritos no evento

Nenhum consumidor precisa conhecer quem publicou o evento.

---

# 6. Fluxo de Funcionamento

```text
Evento Publicado

↓

Validar Envelope

↓

Identificar Assinantes

↓

Distribuir Evento

↓

Registrar Observabilidade

↓

Encerrar
```

---

# 7. Dependências

O Event Bus depende de:

- Message Envelope;
- Runtime.

Não depende de:

- modelos de IA;
- banco vetorial;
- banco relacional;
- ferramentas específicas;
- sistema operacional.

---

# 8. Contratos Utilizados

Todos os eventos utilizam a Message Envelope.

Cada evento deve preservar:

- trace_id;
- parent_id;
- timestamp;
- layer_from;
- layer_to;
- payload;
- meta.

O conteúdo do evento nunca é alterado durante sua distribuição.

---

# 9. Invariantes Arquiteturais

O Event Bus deve garantir que:

1. Todo evento possua um identificador de rastreabilidade (`trace_id`).
2. Eventos nunca sejam modificados durante a distribuição.
3. Produtores não conheçam consumidores.
4. Consumidores não dependam dos produtores.
5. Toda publicação seja registrada.
6. Eventos inválidos sejam rejeitados.

---

# 10. Estados

```text
Idle

↓

Receiving Event

↓

Validating

↓

Routing

↓

Publishing

↓

Completed
```

Em caso de erro:

```text
Publishing

↓

Failed

↓

Notify Runtime

↓

Completed
```

---

# 11. Falhas Previstas

O Event Bus deve tratar:

- Envelope inválida;
- evento desconhecido;
- destinatário inexistente;
- erro de distribuição;
- timeout;
- duplicação de evento.

Toda falha deve ser registrada e comunicada ao Runtime.

---

# 12. Observabilidade

O Event Bus registra:

- publicação do evento;
- origem;
- destino;
- horário;
- duração da distribuição;
- falhas;
- conclusão.

Todos os registros preservam o mesmo `trace_id`.

---

# 13. Exemplo de Execução

O Workflow Engine conclui uma etapa.

Publica:

```text
TaskCompleted
```

↓

O Event Bus recebe.

↓

Distribui para:

- Runtime
- Scheduler
- Monitoramento
- Auditoria

Cada componente recebe o evento sem depender diretamente do Workflow Engine.

---

# 14. Relacionamentos

Recebe eventos de:

- Runtime
- Planner
- Dispatcher
- Scheduler
- Workflow Engine
- Agents
- Skills
- Tools

Distribui eventos para:

- Runtime
- Workflow Engine
- Scheduler
- Serviços de monitoramento
- Componentes inscritos

Nunca comunica-se diretamente com:

- Usuário
- Presentation Layer

---

# 15. Critérios de Conformidade

Uma implementação do Event Bus é considerada compatível quando:

- distribui eventos sem modificá-los;
- preserva o `trace_id`;
- utiliza exclusivamente a Message Envelope;
- mantém produtores e consumidores desacoplados;
- registra todas as publicações;
- rejeita eventos inválidos.

---

# 16. Evolução Futura

O Event Bus poderá evoluir para suportar:

- filas distribuídas;
- publicação assíncrona;
- múltiplos canais de eventos;
- persistência temporária;
- repetição automática em caso de falha;
- integração com serviços externos de mensageria.

Essas evoluções não alteram sua responsabilidade arquitetural.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | RT-006 |
| Camada | Runtime Layer |
| Tipo | Barramento de Eventos |
| Executa lógica | Não |
| Distribui eventos | Sim |
| Modifica eventos | Não |
| Mantém `trace_id` | Sim |
| Desacopla componentes | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- RT-001 — Runtime
- RT-002 — Planner
- RT-003 — Dispatcher
- RT-004 — Scheduler
- RT-005 — Workflow Engine
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia