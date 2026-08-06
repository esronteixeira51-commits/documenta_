# AG-002 — Planner Agent

**Identificador:** AG-002

**Nome:** Planner Agent

**Versão:** 1.0

**Status:** Oficial

**Camada:** Agent Layer

**Tipo:** Agent Especializado

**Documento:** Apêndice E — Catálogo Arquitetural

**Deriva de:**

- AG-001 — Agent

---

# 1. Objetivo

O Planner Agent é responsável por decompor problemas complexos em objetivos menores, independentes e executáveis.

Sua função é produzir um plano intelectual de resolução.

Ele não executa nenhuma etapa do plano.

---

# 2. Responsabilidades

O Planner Agent é responsável por:

- analisar objetivos complexos;
- dividir problemas em subtarefas;
- identificar dependências lógicas;
- organizar prioridades intelectuais;
- produzir planos coerentes;
- encaminhar as subtarefas aos Agents especializados.

---

# 3. Não é Responsável por

O Planner Agent nunca deverá:

- executar OCR;
- pesquisar documentos;
- escrever código;
- realizar cálculos;
- consultar banco vetorial;
- gerar embeddings;
- executar Tools.

---

# 4. Entradas

Recebe:

- objetivo geral;
- contexto;
- restrições;
- critérios de qualidade;
- permissões.

---

# 5. Saídas

Produz:

- plano intelectual;
- lista de subtarefas;
- dependências lógicas;
- prioridades.

---

# 6. Fluxo

```text
Objetivo

↓

Análise

↓

Decomposição

↓

Plano

↓

Subtarefas

↓

Agents Especializados
```

---

# 7. Skills Utilizadas

Exemplos:

- Planning Skill
- Task Decomposition Skill
- Workflow Structuring Skill

---

# 8. Nunca Utiliza Diretamente

- OCR Tool
- Python Tool
- ChromaDB
- SQLite

Sempre através de Skills.

---

# 9. Exemplo

Entrada:

> "Crie toda a documentação arquitetural do Agent OS."

Plano produzido:

```text
Manifesto

↓

Princípios

↓

Interfaces

↓

Arquitetura

↓

Apêndices

↓

Auditoria
```

O Planner Agent apenas organiza.

Outros Agents executam.

---

# 10. Critérios de Conformidade

Um Planner Agent é considerado compatível quando:

- não executa tarefas técnicas;
- produz planos coerentes;
- delega execução;
- utiliza Message Envelope;
- respeita AG-001.