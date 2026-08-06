# AG-007 — Critic Agent

**Identificador:** AG-007

**Nome:** Critic Agent

**Versão:** 1.0

**Status:** Oficial

**Camada:** Agent Layer

**Tipo:** Agent Especializado

**Documento:** Apêndice E — Catálogo Arquitetural

**Deriva de:**

- AG-001 — Agent

---

# 1. Objetivo

O Critic Agent é responsável por analisar, revisar e validar a consistência dos resultados produzidos por outros Agents antes que sejam considerados concluídos.

Sua função é identificar inconsistências, ambiguidades, violações arquiteturais ou oportunidades de melhoria, contribuindo para a qualidade contínua do Agent OS.

O Critic Agent não substitui o Agent responsável pela execução da tarefa.

Ele fornece uma avaliação técnica independente.

---

# 2. Responsabilidades

O Critic Agent é responsável por:

- revisar resultados produzidos por outros Agents;
- verificar consistência lógica;
- identificar conflitos entre documentos;
- validar conformidade com os princípios arquiteturais;
- apontar riscos e inconsistências;
- sugerir melhorias;
- emitir parecer técnico sobre a qualidade do resultado.

---

# 3. Não é Responsável por

O Critic Agent nunca deverá:

- reescrever documentos completos;
- executar pesquisas diretamente;
- escrever código;
- realizar cálculos;
- alterar a arquitetura por iniciativa própria;
- executar Tools diretamente.

Seu papel é avaliar, não executar.

---

# 4. Entradas

Recebe exclusivamente uma Message Envelope contendo:

- artefato a ser revisado;
- contexto;
- critérios de avaliação;
- documentação de referência;
- permissões;
- `trace_id`.

---

# 5. Saídas

Produz:

- relatório de revisão;
- inconsistências encontradas;
- recomendações;
- parecer técnico;
- nível de confiança da revisão.

---

# 6. Fluxo de Funcionamento

```text
Receber Artefato

↓

Analisar Contexto

↓

Validar Critérios

↓

Comparar com Referências

↓

Identificar Inconsistências

↓

Emitir Parecer

↓

Responder ao Solicitante
```

---

# 7. Skills Utilizadas

O Critic Agent poderá utilizar:

- Consistency Validation Skill;
- Cross Reference Skill;
- Rule Validation Skill;
- Architecture Compliance Skill;
- Citation Verification Skill;
- Quality Assessment Skill.

---

# 8. Nunca Utiliza Diretamente

O Critic Agent nunca acessa diretamente:

- banco vetorial;
- banco relacional;
- OCR;
- Python;
- APIs externas;
- motores LLM externos.

Toda interação ocorre através de Skills.

---

# 9. Critérios de Avaliação

Toda revisão deve considerar:

## Consistência

O artefato não deve contradizer outros documentos oficiais.

---

## Conformidade

O conteúdo deve respeitar:

- Manifesto;
- Princípios de Engenharia;
- Contrato de Interfaces;
- Arquitetura Lógica;
- demais especificações oficiais.

---

## Clareza

As responsabilidades devem estar claramente definidas.

---

## Rastreabilidade

Sempre que possível, o Critic Agent deve indicar exatamente onde encontrou uma inconsistência e quais documentos sustentam sua análise.

---

## Evolução

As recomendações devem priorizar refinamentos incrementais, preservando a essência da arquitetura.

---

# 10. Observabilidade

O Critic Agent registra:

- artefato analisado;
- critérios utilizados;
- documentos consultados;
- inconsistências encontradas;
- recomendações emitidas;
- tempo de revisão.

Todos os registros preservam o mesmo `trace_id`.

---

# 11. Exemplo de Execução

Solicitação:

> "Audite o Livro III — Arquitetura Lógica."

Fluxo:

```text
Runtime

↓

Critic Agent

↓

Research Agent

↓

RAG Skill

↓

Knowledge Base

↓

Critic Agent

↓

Consistency Validation Skill

↓

Architecture Compliance Skill

↓

Parecer Técnico

↓

Runtime
```

Resultado:

- inconsistências identificadas;
- conflitos documentais destacados;
- recomendações de melhoria;
- parecer de conformidade.

---

# 12. Relacionamentos

Recebe chamadas de:

- Runtime;
- Planner Agent;
- Documentation Agent;
- Programming Agent;
- Coordinator Agent.

Solicita apoio de:

- Research Agent;
- Mathematics Agent (quando necessário).

Comunica-se com:

- Skills de validação.

Nunca comunica-se diretamente com:

- Tools;
- Usuário.

---

# 13. Critérios de Conformidade

Um Critic Agent é considerado compatível quando:

- respeita AG-001;
- utiliza apenas Skills;
- preserva o `trace_id`;
- fundamenta suas conclusões;
- não modifica diretamente o artefato analisado;
- mantém independência em relação ao Agent autor.

---

# 14. Evolução Futura

O Critic Agent poderá evoluir para suportar:

- auditoria arquitetural automática;
- validação entre versões de documentos;
- detecção de regressões arquiteturais;
- verificação automática de aderência ao código;
- análise de impacto de mudanças;
- revisão colaborativa entre múltiplos Critics.

Essas evoluções não alteram sua responsabilidade arquitetural.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | AG-007 |
| Camada | Agent Layer |
| Tipo | Agent Especializado |
| Audita resultados | Sim |
| Coordena Skills | Sim |
| Executa Tools | Não |
| Modifica artefatos | Não |
| Mantém `trace_id` | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- AG-001 — Agent
- AG-003 — Research Agent
- AG-006 — Mathematics Agent
- SK-001 — Skill
- TL-001 — Tool
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia