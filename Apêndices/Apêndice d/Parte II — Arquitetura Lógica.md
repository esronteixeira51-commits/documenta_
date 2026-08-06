###############################################################################
# PARTE II
# ARQUITETURA LÓGICA
###############################################################################

# Objetivo

Esta parte apresenta a organização lógica interna do Agent OS.

Diferentemente da Parte I, que descreve apenas conceitos de alto nível, esta seção apresenta os componentes oficiais da arquitetura e suas relações.

Os diagramas desta seção representam responsabilidades arquiteturais.

Eles não representam arquivos, módulos ou diretórios da implementação.

---

# Visão Geral

```

                    Presentation
                          │
                          ▼
                     Runtime Layer
                          │
      ┌───────────────────┼───────────────────┐
      ▼                   ▼                   ▼
  Planner            Dispatcher         Scheduler
                          │
                          ▼
                Intelligence Layer
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
 Research Agent      Math Agent      Documentation
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
                 Capabilities Layer
                          │
      ┌───────────────────┼───────────────────┐
      ▼                   ▼                   ▼
   RAG Skill        OCR Skill          Python Skill
      │                   │                   │
      └───────────────────┼───────────────────┘
                          ▼
                  Execution Layer
                          │
      ┌───────────────────┼─────────────────────┐
      ▼                   ▼                     ▼
 ChromaDB Tool      OCR Worker           Python Sandbox
                          │
                          ▼
                  Infrastructure
```

---

# Organização Geral

A arquitetura lógica do Agent OS é composta por seis camadas.

Cada camada possui responsabilidades exclusivas.

Nenhuma camada executa responsabilidades pertencentes a outra.

---

###############################################################################
# D-008
###############################################################################

## Runtime Layer

Objetivo

Representar o núcleo de coordenação do sistema.

```
                Runtime
                    │
     ┌──────────────┼──────────────┐
     ▼              ▼              ▼
 Planner      Dispatcher      Scheduler
                    │
             Workflow Engine
                    │
               Event Bus
```

### Responsabilidade

Coordenar toda a execução do sistema.

### Nunca faz

- cálculos;

- OCR;

- RAG;

- SQL;

- Python;

- acesso direto ao banco.

### Componentes

Planner

Dispatcher

Scheduler

Workflow Engine

Event Bus

---

###############################################################################
# D-009
###############################################################################

## Planner

```
Entrada

↓

Análise

↓

Plano

↓

Subtarefas

↓

Dispatcher
```

### Responsabilidade

Transformar objetivos em planos executáveis.

### Entrada

Task

### Saída

Workflow.

---

###############################################################################
# D-010
###############################################################################

## Dispatcher

```
Workflow

↓

Seleciona Agent

↓

Entrega Envelope

↓

Monitora

↓

Recebe Resultado
```

### Responsabilidade

Encaminhar tarefas.

Jamais decide como resolvê-las.

---

###############################################################################
# D-011
###############################################################################

## Scheduler

```
Fila

↓

Prioridade

↓

Recursos

↓

Execução
```

Responsável por decidir quando cada tarefa será executada.

---

###############################################################################
# D-012
###############################################################################

## Workflow Engine

```
Workflow

↓

Estado Atual

↓

Próximo Passo

↓

Atualização
```

Controla o estado da execução.

---

###############################################################################
# D-013
###############################################################################

## Event Bus

```
Evento

↓

Distribuição

↓

Consumidores

↓

Logs
```

Responsável pela comunicação baseada em eventos.

---

###############################################################################
# D-014
###############################################################################

## Intelligence Layer

```
Runtime

↓

Agent

↓

Decisão

↓

Skill
```

A camada Intelligence nunca executa operações.

Ela apenas decide.

---

###############################################################################
# D-015
###############################################################################

## Agent

```
Objetivo

↓

Planejamento

↓

Escolha

↓

Skill
```

Responsabilidades

Interpretar.

Planejar.

Coordenar.

Nunca executar.

---

###############################################################################
# D-016
###############################################################################

## Exemplo de Especialização

```
                    Intelligence

      ┌──────────────┼──────────────┐

      ▼              ▼              ▼

Research        Mathematics    Documentation

      │              │              │

      └──────────────┼──────────────┘

                     ▼

                  Runtime
```

Novos Agents poderão ser adicionados sem alterar a arquitetura.

---

###############################################################################
# D-017
###############################################################################

## Capabilities Layer

```
Agent

↓

Skill

↓

Tool
```

Esta camada representa capacidades reutilizáveis.

---

###############################################################################
# D-018
###############################################################################

## Skill

```
Pedido

↓

Planejamento Técnico

↓

Ferramentas

↓

Resultado
```

Responsável por transformar intenção em operações técnicas.

---

###############################################################################
# D-019
###############################################################################

## Execution Layer

```
Skill

↓

Tool

↓

Resultado
```

Executa operações determinísticas.

---

###############################################################################
# D-020
###############################################################################

## Tool

```
Comando

↓

Execução

↓

Resultado
```

A Tool nunca interpreta linguagem natural.

Recebe apenas parâmetros estruturados.

---

###############################################################################
# Encerramento da Parte II
###############################################################################

A Parte II estabelece os componentes oficiais da Arquitetura Lógica do Agent OS.

Ela define a organização interna do Runtime, da camada de Inteligência, das Capacidades e da Execução, demonstrando como cada responsabilidade é distribuída entre componentes especializados.

Todos os componentes apresentados nesta seção comunicam-se exclusivamente através dos contratos definidos no Livro II e obedecem aos princípios estabelecidos pelo Manifesto do Agent OS e pelos Princípios de Engenharia.

A implementação poderá evoluir ao longo do tempo, mas deverá preservar esta organização lógica, garantindo que a arquitetura permaneça estável, compreensível e auditável.