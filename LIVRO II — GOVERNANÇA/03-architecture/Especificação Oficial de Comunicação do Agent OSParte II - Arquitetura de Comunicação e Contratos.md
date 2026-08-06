# Parte II — Arquitetura de Comunicação e Contratos

---

# 7. Arquitetura de Comunicação

A comunicação oficial do Agent OS é baseada em uma arquitetura de mensagens.

Nenhum componente comunica-se diretamente através de implementações internas de outro componente.

Toda interação ocorre exclusivamente através de contratos públicos.

Esta arquitetura garante:

- baixo acoplamento;
- substituição de componentes;
- rastreabilidade;
- auditoria;
- evolução contínua.

A comunicação representa a única fronteira oficialmente reconhecida entre componentes.

---

## 7.1 Fluxo Arquitetural

A comunicação segue sempre a direção estabelecida pela arquitetura.

```
                Runtime
                    │
                    ▼
                Planner
                    │
                    ▼
              Dispatcher
                    │
                    ▼
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

O retorno percorre exatamente o caminho inverso.

Nenhum componente poderá ignorar esta hierarquia.

---

## 7.2 Direção das Dependências

As dependências sempre apontam para baixo na hierarquia.

```
Runtime
      ↓
Planner
      ↓
Dispatcher
      ↓
Agent
      ↓
Skill
      ↓
Tool
      ↓
Infrastructure
```

Dependências inversas não fazem parte da arquitetura oficial.

---

# 8. Relações entre Componentes

A arquitetura define explicitamente quais relações são permitidas.

Toda comunicação não prevista nesta seção será considerada inválida.

---

## 8.1 Relações Permitidas

| Origem | Destino |
|---------|----------|
| Runtime | Planner |
| Planner | Dispatcher |
| Dispatcher | Agent |
| Agent | Skill |
| Skill | Tool |
| Tool | Infrastructure |

---

## 8.2 Relações Condicionais

As relações abaixo somente poderão existir quando previstas por contrato específico.

| Origem | Destino |
|---------|----------|
| Tool | Tool |
| Skill | Skill |
| Agent | Agent |
| Runtime | Runtime |

Essas comunicações deverão possuir justificativa arquitetural.

---

## 8.3 Relações Proibidas

São consideradas violações arquiteturais:

| Origem | Destino |
|---------|----------|
| Tool | Agent |
| Tool | Runtime |
| Tool | Planner |
| Skill | Runtime |
| Skill | Planner |
| Infrastructure | Agent |
| Infrastructure | Skill |
| Infrastructure | Runtime |

Essas comunicações nunca deverão ocorrer diretamente.

---

# 9. Envelope Universal

Toda comunicação oficial utiliza uma única estrutura de transporte denominada Envelope.

Independentemente da tecnologia utilizada, toda mensagem deverá preservar a mesma estrutura lógica.

A Envelope representa o contrato universal de comunicação do Agent OS.

---

## 9.1 Estrutura Canônica

```
Envelope
│
├── trace_id
├── parent_id
├── message_id
├── timestamp
├── layer_from
├── layer_to
├── target
├── type
├── payload
├── context
├── permissions
├── metadata
└── signature (opcional)
```

Esta estrutura representa o contrato lógico.

A serialização poderá variar conforme a tecnologia utilizada.

---

# 10. Campos Obrigatórios

Toda Envelope deverá conter obrigatoriamente os seguintes campos.

---

## trace_id

Identifica a execução completa.

Todas as mensagens pertencentes ao mesmo fluxo compartilham o mesmo trace_id.

---

## parent_id

Identifica a mensagem imediatamente anterior.

Permite reconstruir a árvore de execução.

---

## message_id

Identificador único da própria mensagem.

Cada Envelope possui exatamente um message_id.

---

## timestamp

Instante oficial da criação da mensagem.

Formato definido pela implementação.

---

## layer_from

Componente responsável pela emissão.

---

## layer_to

Componente destinatário.

---

## target

Identificador lógico do componente destinatário.

Exemplos:

```
agent.researcher

skill.rag_search

tool.python_exec
```

---

## type

Categoria da mensagem.

Os tipos oficiais encontram-se na seção seguinte.

---

## payload

Conteúdo principal da comunicação.

Seu formato depende do contrato específico.

---

## context

Estado compartilhado da execução.

Não representa a tarefa.

Representa informações auxiliares.

---

## permissions

Informações de autorização.

Definem os limites operacionais da execução.

---

## metadata

Informações auxiliares.

Nunca alteram o comportamento funcional.

Destinam-se à observabilidade.

---

# 11. Tipos Oficiais de Mensagem

A arquitetura reconhece apenas os seguintes tipos.

---

## request

Solicita a execução de uma operação.

---

## result

Representa uma execução concluída com sucesso.

---

## error

Representa uma falha controlada.

---

## pending_confirmation

Representa uma execução suspensa aguardando decisão humana.

Não caracteriza erro.

---

Eventos futuros deverão ser adicionados através de evolução desta especificação.

---

# 12. Contratos de Comunicação

Todo contrato público deverá definir explicitamente:

- responsabilidade;
- entrada;
- saída;
- erros previstos;
- permissões exigidas;
- compatibilidade;
- versão.

Nenhum contrato poderá depender de comportamento implícito.

---

# 13. Regras Gerais

Toda comunicação deverá obedecer às seguintes regras.

## Regra 1

Toda mensagem possui exatamente um emissor.

---

## Regra 2

Toda mensagem possui exatamente um destinatário lógico.

---

## Regra 3

Toda mensagem pertence a um único fluxo identificado por trace_id.

---

## Regra 4

Toda resposta referencia uma solicitação anterior.

---

## Regra 5

Toda comunicação deve ser validada antes de sua execução.

---

## Regra 6

Nenhum componente poderá alterar uma mensagem recebida.

Caso sejam necessárias modificações, uma nova mensagem deverá ser produzida.

---

## Regra 7

Mensagens representam fatos.

Estados internos pertencem aos componentes.

---

# 14. Violações Arquiteturais

São consideradas violações desta especificação:

• comunicação direta ignorando contratos públicos;

• acesso direto à implementação de outro componente;

• alteração de mensagens durante propagação;

• dependências inversas;

• contratos implícitos;

• quebra de compatibilidade sem versionamento;

• utilização de formatos proprietários não documentados.

Toda violação deverá ser registrada como dívida arquitetural ou corrigida imediatamente.