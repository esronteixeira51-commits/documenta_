# Apêndice B — Convenções Arquiteturais e Documentais

Versão: 1.0

Status: Oficial

Tipo: Documento Normativo de Referência

Aplica-se a:

- Toda documentação oficial
- ADRs
- Especificações Técnicas
- Arquitetura
- Implementações

---

# Finalidade

Este documento estabelece as convenções oficiais utilizadas na documentação do Agent OS.

Seu objetivo é garantir consistência, clareza e previsibilidade em toda a documentação do projeto.

Estas convenções complementam o Glossário Arquitetural.

Enquanto o Glossário define o significado dos termos, este documento define como esses termos devem ser utilizados.

---

# 1. Princípios Gerais

Toda documentação deverá obedecer aos seguintes princípios.

## Clareza

Escrever para ser compreendido.

Nunca para impressionar.

---

## Precisão

Cada termo possui um único significado.

Evitar sinônimos para conceitos arquiteturais.

---

## Consistência

Um conceito deverá ser descrito sempre da mesma maneira em todos os documentos.

---

## Simplicidade

Preferir linguagem objetiva.

Frases curtas.

Pouca redundância.

---

## Independência Tecnológica

A documentação arquitetural nunca deverá depender de tecnologias específicas.

---

# 2. Estrutura dos Documentos

Todo documento oficial deverá possuir:

- título;

- versão;

- status;

- tipo;

- dependências (quando aplicável);

- finalidade;

- corpo principal;

- encerramento.

---

# 3. Status Oficiais

Os documentos poderão possuir apenas os seguintes estados.

| Status | Significado |
|----------|------------|
| Rascunho | Documento inicial |
| Em Auditoria | Em revisão arquitetural |
| Oficial | Documento aprovado |
| Obsoleto | Documento substituído |
| Arquivado | Mantido apenas por referência histórica |

---

# 4. Convenções de Escrita

Preferir:

✔ "deverá"

✔ "poderá"

✔ "nunca"

✔ "sempre"

Evitar:

✘ "talvez"

✘ "normalmente"

✘ "provavelmente"

✘ "em alguns casos"

Documentos normativos devem utilizar linguagem normativa.

---

# 5. Terminologia

Somente utilizar termos definidos no Glossário Arquitetural.

Caso um novo termo seja necessário:

1. atualizar o Glossário;

2. revisar os documentos impactados;

3. somente então utilizar o novo termo.

---

# 6. Convenções de Diagramas

Todo diagrama deverá representar conceitos arquiteturais.

Nunca tecnologias.

Exemplo correto:

```
Runtime
    │
    ▼
Agent
    │
    ▼
Skill
```

Exemplo incorreto:

```
FastAPI
    │
Docker
    │
LM Studio
```

Esses pertencem à Arquitetura Física.

---

# 7. Convenções de Nomenclatura

Camadas:

Presentation

Runtime

Intelligence

Capabilities

Execution

Infrastructure

---

Componentes:

Agent

Skill

Tool

Planner

Dispatcher

Workflow Engine

Scheduler

Event Bus

---

Documentos:

Boot Filosófico

Capítulo Zero

Constituição

Manifesto

Princípios de Engenharia

Arquitetura Lógica

Arquitetura Física

---

# 8. Convenções para ADRs

Toda ADR deverá possuir:

- contexto;

- problema;

- decisão;

- justificativa;

- consequências;

- status.

---

# 9. Convenções de Versionamento

Versionamento semântico.

```
MAJOR.MINOR
```

Exemplos:

1.0

2.0

2.1

Mudanças editoriais não alteram versão principal.

Mudanças arquiteturais alteram a versão correspondente.

---

# 10. Convenções de Referência

Sempre referenciar documentos oficiais por seu nome completo.

Exemplo:

"Manifesto do Agent OS"

Evitar:

"Manifesto"

ou

"Documento anterior"

---

# 11. Convenções de Citações

Sempre que um documento depender de outro deverá indicar explicitamente essa dependência.

Exemplo:

Depende de:

- Manifesto do Agent OS

- Princípios de Engenharia

---

# 12. Convenções para Exemplos

Todo exemplo deverá ser claramente identificado.

Exemplos nunca possuem autoridade normativa.

Quando houver conflito:

A regra normativa prevalece sobre o exemplo.

---

# 13. Convenções para Código

Código apresentado na documentação possui finalidade ilustrativa.

A implementação oficial deverá possuir documentação própria.

---

# 14. Convenções para Diagramas ASCII

Sempre utilizar direção vertical.

```
Usuário
    │
    ▼
Presentation
    │
    ▼
Runtime
```

Evitar diagramas horizontais extensos.

---

# 15. Convenções para Evolução

Nenhum documento poderá alterar outro documento implicitamente.

Toda alteração deverá ser explícita.

Toda alteração deverá ser rastreável.

Toda alteração deverá preservar a hierarquia normativa.

---

# Encerramento

As Convenções Arquiteturais e Documentais estabelecem um padrão único para toda a documentação do Agent OS.

Seu objetivo é garantir que todos os documentos mantenham a mesma linguagem, estrutura e organização ao longo da evolução do projeto.

Estas convenções complementam os documentos normativos e deverão ser observadas em toda produção documental oficial.