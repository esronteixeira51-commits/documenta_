# TL-001 — Tool

**Identificador:** TL-001

**Nome:** Tool

**Versão:** 1.0

**Status:** Oficial

**Camada:** Tool Layer

**Tipo:** Componente Abstrato

**Documento:** Apêndice E — Catálogo Arquitetural

**Relacionado a:**

- SK-001 — Skill
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia

---

# 1. Objetivo

A Tool representa a menor unidade executável do Agent OS.

Sua função é executar uma operação técnica específica de forma determinística, recebendo parâmetros estruturados e produzindo um resultado estruturado.

A Tool não interpreta objetivos.

A Tool não decide estratégias.

A Tool apenas executa.

---

# 2. Responsabilidades

A Tool é responsável por:

- executar uma única operação técnica;
- validar parâmetros obrigatórios;
- produzir um resultado estruturado;
- retornar erros padronizados;
- registrar informações de observabilidade.

---

# 3. Não é Responsável por

A Tool nunca deverá:

- interpretar linguagem natural;
- decidir qual operação executar;
- chamar Agents;
- chamar outras Tools por iniciativa própria;
- modificar o Workflow;
- acessar a interface do usuário;
- tomar decisões arquiteturais.

Toda coordenação pertence à Skill.

---

# 4. Entradas

A Tool recebe exclusivamente uma Message Envelope contendo:

- comando;
- argumentos estruturados;
- contexto técnico necessário;
- permissões;
- trace_id.

Nunca deve receber instruções em linguagem natural como mecanismo principal de operação.

---

# 5. Saídas

A Tool produz:

- resultado estruturado;
- códigos de erro oficiais;
- métricas de execução;
- eventos de observabilidade.

---

# 6. Fluxo de Funcionamento

```text
Receber Envelope

↓

Validar Parâmetros

↓

Executar Operação

↓

Produzir Resultado

↓

Responder à Skill
```

---

# 7. Dependências

A Tool depende de:

- SK-001 — Skill;
- Message Envelope;
- recursos técnicos necessários para sua função.

Esses recursos podem incluir:

- bibliotecas;
- motores de IA;
- banco vetorial;
- banco relacional;
- sistema operacional;
- APIs externas;
- hardware especializado.

Essas dependências pertencem à implementação da Tool, não ao restante da arquitetura.

---

# 8. Contratos Utilizados

Toda comunicação utiliza exclusivamente a Message Envelope.

A Tool comunica-se apenas com Skills.

Nunca comunica-se diretamente com:

- Runtime;
- Agents;
- Usuário.

---

# 9. Invariantes Arquiteturais

Toda Tool deve garantir que:

1. execute apenas sua responsabilidade técnica;
2. preserve o `trace_id`;
3. nunca altere o contexto recebido;
4. produza resultados estruturados;
5. utilize apenas parâmetros estruturados;
6. nunca interprete objetivos do usuário;
7. nunca execute planejamento.

---

# 10. Estados

```text
Idle

↓

Receiving

↓

Validating

↓

Executing

↓

Completed
```

Em caso de falha:

```text
Executing

↓

Failed

↓

Return Error
```

---

# 11. Falhas Previstas

A Tool deve tratar:

- parâmetros inválidos;
- recurso indisponível;
- timeout;
- erro interno;
- permissão insuficiente;
- falha de comunicação.

Toda falha deve utilizar os códigos oficiais definidos pelo Contrato de Interfaces.

---

# 12. Observabilidade

Toda Tool registra:

- início da execução;
- parâmetros técnicos (quando permitido);
- duração;
- consumo de recursos;
- resultado;
- falhas.

Todos os registros preservam o mesmo `trace_id`.

---

# 13. Exemplo de Execução

## OCR Tool

Entrada:

```text
arquivo.pdf
```

↓

Saída:

```text
texto extraído
```

---

## Python Tool

Entrada:

```text
script.py
```

↓

Saída:

```text
resultado da execução
```

---

## Vector Search Tool

Entrada:

```text
embedding
```

↓

Saída:

```text
documentos encontrados
```

A Tool executa exclusivamente sua função técnica.

---

# 14. Relacionamentos

Recebe chamadas de:

- Skills.

Comunica-se com:

- recursos técnicos necessários à sua implementação.

Nunca comunica-se diretamente com:

- Runtime;
- Agents;
- Usuário.

---

# 15. Critérios de Conformidade

Uma Tool é considerada compatível quando:

- executa uma responsabilidade técnica claramente definida;
- utiliza exclusivamente Message Envelope;
- preserva o `trace_id`;
- retorna resultados estruturados;
- não interpreta linguagem natural como regra de negócio;
- não toma decisões arquiteturais.

---

# 16. Evolução Futura

Novas Tools podem ser adicionadas sem alterar Skills ou Agents.

Exemplos:

- OCR Tool;
- Python Tool;
- ChromaDB Tool;
- SQLite Tool;
- Web Search Tool;
- Embedding Tool;
- Speech-to-Text Tool;
- Image Generation Tool;
- PDF Parser Tool.

Todas devem respeitar esta especificação base.

---

# Resumo Arquitetural

| Item | Valor |
|-------|-------|
| Identificador | TL-001 |
| Camada | Tool Layer |
| Tipo | Componente Abstrato |
| Executa operação técnica | Sim |
| Decide estratégia | Não |
| Interpreta objetivo | Não |
| Mantém `trace_id` | Sim |
| Contrato oficial | Message Envelope |

---

# Referências

- SK-001 — Skill
- Livro II — Especificação Oficial de Comunicação
- Livro III — Arquitetura Lógica
- Manifesto do Agent OS
- Princípios de Engenharia