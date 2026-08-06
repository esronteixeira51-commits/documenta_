# Parte III — Componentes Arquiteturais

---

# 21. Visão Geral

A Arquitetura Lógica organiza o Agent OS como um conjunto de Componentes especializados.

Um componente representa uma unidade arquitetural responsável por desempenhar uma função específica dentro do sistema.

Todo componente pertence exatamente a uma camada arquitetural.

Todo componente comunica-se exclusivamente através dos contratos públicos definidos pela Especificação Oficial de Comunicação.

---

# 22. Modelo Geral

Todos os componentes compartilham as seguintes propriedades arquiteturais.

• possuem responsabilidade única;

• possuem limites bem definidos;

• conhecem apenas contratos públicos;

• podem ser substituídos;

• evoluem independentemente.

Essas propriedades caracterizam um componente arquitetural.

---

# 23. Runtime Component

## Finalidade

Coordenar a execução global do sistema.

Um Runtime Component organiza.

Nunca executa lógica de domínio.

Nunca realiza operações especializadas.

---

## Responsabilidades

- iniciar execuções;

- controlar workflows;

- gerenciar sessões;

- coordenar eventos;

- despachar tarefas;

- controlar o ciclo de vida da execução.

---

## Exemplos

- Runtime Engine

- Planner

- Dispatcher

- Scheduler

- Workflow Engine

- Event Bus

---

# 24. Agent

## Finalidade

Resolver problemas através da tomada de decisão.

O Agent interpreta objetivos.

Seleciona estratégias.

Escolhe Skills.

Coordena a resolução.

---

## Responsabilidades

- interpretar objetivos;

- decompor problemas;

- selecionar Skills;

- validar resultados;

- coordenar capacidades.

---

## Um Agent nunca deve

- executar cálculos determinísticos;

- acessar infraestrutura diretamente;

- executar ferramentas;

- implementar regras específicas de uma Skill.

---

## Relações

Um Agent pode utilizar:

- Skills

Um Agent nunca utiliza diretamente:

- Infrastructure

Um Agent nunca executa:

- Tools

---

# 25. Skill

## Finalidade

Representar uma capacidade reutilizável.

Uma Skill transforma uma decisão em operações concretas.

Ela conhece procedimentos.

Não conhece objetivos globais.

---

## Responsabilidades

- orquestrar Tools;

- validar parâmetros;

- compor operações;

- reutilizar capacidades.

---

## Uma Skill nunca deve

- tomar decisões estratégicas;

- interpretar objetivos do usuário;

- coordenar outros Agents.

---

## Relações

Uma Skill utiliza:

- Tools

Uma Skill responde para:

- Agent

---

# 26. Tool

## Finalidade

Executar operações determinísticas.

Uma Tool transforma entrada em saída.

Nada mais.

---

## Responsabilidades

- executar código;

- realizar cálculos;

- acessar arquivos;

- acessar bancos;

- consumir APIs;

- manipular recursos externos.

---

## Uma Tool nunca deve

- interpretar linguagem natural;

- tomar decisões;

- selecionar estratégias;

- coordenar workflows.

---

## Relações

Uma Tool depende apenas da infraestrutura necessária para sua execução.

---

# 27. Infrastructure Component

## Finalidade

Disponibilizar recursos computacionais.

A infraestrutura não participa da lógica arquitetural.

Ela fornece recursos.

---

## Responsabilidades

- armazenamento;

- processamento;

- rede;

- bancos de dados;

- modelos;

- containers;

- sistema operacional.

---

## A infraestrutura nunca deve

- decidir;

- interpretar;

- coordenar;

- modificar contratos.

---

# 28. Componentes de Suporte

Alguns componentes não pertencem diretamente ao fluxo principal de resolução de tarefas.

Eles fornecem serviços transversais à arquitetura.

Exemplos incluem:

- Memory Manager

- Logging

- Observability

- Configuration

- Cache

- Policy Engine

Esses componentes deverão respeitar os mesmos contratos públicos utilizados pelos demais componentes.

---

# 29. Relações Arquiteturais

A arquitetura estabelece as seguintes relações fundamentais.

```

Agent
│
▼
Skill
│
▼
Tool
│
▼
Infrastructure

```

Runtime Components coordenam esse fluxo.

Nenhum componente poderá inverter essa direção.

---

# 30. Ciclo de Vida dos Componentes

Todo componente participa do seguinte ciclo arquitetural.

```

Inicialização

↓

Disponível

↓

Recebe Contrato

↓

Processa

↓

Produz Resultado

↓

Retorna ao Estado Disponível

```

A arquitetura não impõe como cada componente implementa internamente esse ciclo.

Impõe apenas sua existência.

---

# 31. Extensibilidade

Novos componentes poderão ser adicionados.

Entretanto deverão:

- possuir responsabilidade única;

- pertencer a uma camada existente;

- respeitar os contratos públicos;

- preservar a direção arquitetural;

- manter compatibilidade.

Caso um novo componente não se encaixe em nenhuma camada existente, deverá ser criada uma ADR justificando sua introdução.

---

# Encerramento

Os Componentes Arquiteturais representam os blocos fundamentais do Agent OS.

As próximas partes deste livro descreverão como esses componentes colaboram durante a execução de um fluxo completo e como suas dependências são organizadas para preservar a estabilidade da arquitetura.