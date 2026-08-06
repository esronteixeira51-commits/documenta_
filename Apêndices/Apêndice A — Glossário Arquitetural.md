# Apêndice A — Glossário Arquitetural

Versão: 1.0

Status: Oficial

Tipo: Documento Normativo de Referência

Aplica-se a:

- Livro I
- Livro II
- Livro III
- Arquitetura Física
- Especificações Técnicas
- ADRs

---

# Finalidade

Este documento estabelece o vocabulário oficial do Agent OS.

Todo termo definido neste glossário possui significado normativo.

Documentos oficiais deverão utilizar essas definições.

Caso um termo necessite de novo significado, este glossário deverá ser atualizado antes da documentação correspondente.

---

# Organização

Os termos estão organizados alfabeticamente.

Cada definição descreve exclusivamente seu significado arquitetural.

Não descreve implementações.

---

# A

## Agent

Componente da camada Intelligence responsável por interpretar objetivos, tomar decisões e coordenar capacidades especializadas.

Um Agent nunca executa operações determinísticas diretamente.

---

## Arquitetura

Organização estrutural do Agent OS composta por responsabilidades, camadas, componentes, contratos e princípios.

A arquitetura é independente de tecnologias específicas.

---

## Auditoria Arquitetural

Processo sistemático de verificação da conformidade da arquitetura com os documentos normativos do projeto.

---

# C

## Camada

Nível de abstração responsável por agrupar componentes com responsabilidades semelhantes.

Camadas representam organização arquitetural.

Não representam diretórios, processos ou servidores.

---

## Capacidade (Capability)

Competência reutilizável disponibilizada ao sistema por meio de uma ou mais Skills.

Representa o que o sistema é capaz de realizar, independentemente da implementação.

---

## Componente

Unidade arquitetural fundamental do Agent OS.

Todo componente possui responsabilidade única, limites explícitos e comunicação baseada em contratos públicos.

---

## Contrato

Definição pública que estabelece como componentes podem comunicar-se.

Contratos preservam compatibilidade entre implementações.

---

# D

## Dependência

Relação arquitetural na qual um componente necessita conhecer outro componente ou contrato para desempenhar sua responsabilidade.

Comunicação não implica dependência.

---

## Dispatcher

Componente do Runtime responsável por encaminhar tarefas aos componentes adequados.

Não toma decisões de domínio.

---

## Domínio

Contexto lógico que delimita o escopo de conhecimento ou operação de uma execução.

Utilizado para preservar isolamento entre conjuntos distintos de informações.

---

# E

## Envelope

Estrutura universal utilizada para transportar mensagens entre componentes do Agent OS.

Constitui a unidade oficial de comunicação da arquitetura.

---

## Execução

Etapa na qual operações determinísticas são efetivamente realizadas pelas Tools.

---

# F

## Fluxo Arquitetural

Sequência lógica de responsabilidades percorridas por uma execução.

Independe da implementação utilizada.

---

# G

## Governança Arquitetural

Conjunto de mecanismos responsáveis por preservar a integridade da arquitetura durante sua evolução.

---

# I

## Implementação

Realização concreta da arquitetura utilizando tecnologias específicas.

Implementações não redefinem a arquitetura.

---

## Infrastructure

Camada responsável por disponibilizar recursos computacionais necessários ao funcionamento do sistema.

Não participa da lógica arquitetural.

---

# M

## Manifesto

Documento normativo que estabelece os princípios fundamentais de engenharia do Agent OS.

---

## Mensagem

Instância de comunicação entre componentes.

Toda mensagem utiliza uma Envelope.

---

# P

## Planner

Componente pertencente ao Runtime responsável por decompor objetivos em tarefas executáveis.

---

## Presentation

Camada responsável pelos pontos de entrada e saída do sistema.

Não implementa lógica de domínio.

---

# R

## Responsabilidade

Função arquitetural atribuída a um componente.

Cada componente possui uma única responsabilidade principal.

---

## Runtime

Camada responsável por coordenar a execução do sistema.

Não executa lógica especializada.

---

# S

## Skill

Componente responsável por transformar decisões em operações reutilizáveis.

Orquestra Tools.

Não toma decisões estratégicas.

---

## Sistema

Conjunto organizado de componentes arquiteturais que cooperam segundo contratos públicos para atingir objetivos definidos.

---

# T

## Task

Unidade lógica de trabalho processada pelo Agent OS.

Uma Task percorre o fluxo arquitetural completo.

---

## Tool

Componente responsável por executar operações determinísticas.

Não interpreta objetivos nem toma decisões.

---

## Trace

Identificador utilizado para rastrear uma execução ponta a ponta.

---

# V

## Validação

Processo de verificação da conformidade estrutural, contratual ou funcional antes da continuidade de uma execução.

---

## Workflow

Sequência organizada de tarefas coordenadas pelo Runtime para atingir um objetivo específico.

---

# Encerramento

Este Glossário constitui a referência oficial de terminologia do Agent OS.

Sempre que houver divergência entre definições utilizadas em documentos distintos, prevalecerá a definição estabelecida neste Glossário.

Novos termos arquiteturais deverão ser incorporados a este documento antes de serem utilizados como conceitos oficiais.