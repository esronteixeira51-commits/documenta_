# Parte V — Modelo de Dependências

---

# 43. Finalidade

Esta seção define como os componentes do Agent OS podem estabelecer relações de dependência.

Seu objetivo é preservar a estabilidade arquitetural, reduzindo o acoplamento entre componentes e permitindo evolução contínua.

As dependências descritas neste documento são arquiteturais.

Não representam importações de código, bibliotecas ou dependências de linguagem.

---

# 44. Princípios Gerais

O modelo de dependências do Agent OS é orientado pelos seguintes princípios.

## Dependência Unidirecional

Toda dependência possui direção única.

Componentes inferiores nunca controlam componentes superiores.

---

## Baixo Acoplamento

Componentes conhecem apenas o necessário para cumprir sua responsabilidade.

Conhecimento desnecessário aumenta o acoplamento e reduz a capacidade de evolução.

---

## Dependência por Contrato

Os componentes dependem exclusivamente de contratos públicos.

Nunca de detalhes internos de implementação.

Essa é a principal garantia de substituibilidade da arquitetura.

---

## Estabilidade

Quanto mais alto um componente estiver na hierarquia arquitetural, maior deverá ser sua estabilidade.

Mudanças frequentes devem ocorrer preferencialmente nos níveis inferiores.

---

# 45. Direção das Dependências

As dependências seguem sempre a direção da arquitetura.

```
Presentation
        │
        ▼
Runtime
        │
        ▼
Intelligence
        │
        ▼
Capabilities
        │
        ▼
Execution
        │
        ▼
Infrastructure
```

Nenhuma camada poderá estabelecer dependências diretas em sentido contrário.

---

# 46. Matriz de Dependências

A arquitetura define explicitamente quais relações são permitidas.

| Camada | Pode depender de | Nunca depende de |
|---------|------------------|------------------|
| Presentation | Runtime | Intelligence, Capabilities, Execution, Infrastructure |
| Runtime | Intelligence | Presentation |
| Intelligence | Capabilities | Runtime, Presentation |
| Capabilities | Execution | Intelligence, Runtime, Presentation |
| Execution | Infrastructure | Capabilities, Intelligence, Runtime, Presentation |
| Infrastructure | Nenhuma camada superior | Todas as camadas superiores |

A comunicação inversa ocorre exclusivamente por meio dos contratos definidos na Especificação Oficial de Comunicação.

Ela não representa uma dependência arquitetural.

---

# 47. Dependências entre Componentes

Os componentes seguem as regras da camada à qual pertencem.

Exemplos:

- um Agent pode depender de uma Skill;

- uma Skill pode depender de uma Tool;

- uma Tool pode depender da infraestrutura necessária para executar sua operação.

Essas relações não autorizam dependências em sentido inverso.

---

# 48. Dependências Proibidas

São consideradas violações arquiteturais:

- Tool depender de Agent;

- Skill depender diretamente de Runtime;

- Infrastructure conhecer lógica de domínio;

- Presentation acessar Tools diretamente;

- Runtime executar operações próprias de Skills ou Tools.

Toda violação deverá ser tratada como dívida arquitetural.

---

# 49. Dependências Transversais

Alguns componentes fornecem serviços compartilhados para toda a arquitetura.

Exemplos:

- Logging;

- Observabilidade;

- Configuração;

- Telemetria;

- Cache;

- Policy Engine.

Esses componentes não participam da lógica de domínio.

Seu acesso deverá ocorrer por contratos públicos e interfaces estáveis.

---

# 50. Inversão de Dependências

Sempre que possível, componentes deverão depender de abstrações arquiteturais.

Nunca de implementações concretas.

Isso permite:

- substituição de componentes;

- testes independentes;

- evolução incremental;

- redução de acoplamento.

---

# 51. Evolução das Dependências

Novas dependências somente poderão ser introduzidas quando:

- respeitarem a direção arquitetural;

- preservarem o baixo acoplamento;

- utilizarem contratos públicos;

- não criarem ciclos;

- forem justificadas por necessidade arquitetural.

Dependências que contrariem esses princípios deverão ser formalizadas por ADR antes de sua adoção.

---

# 52. Ciclos de Dependência

A arquitetura do Agent OS proíbe ciclos de dependência.

Nenhum componente poderá depender, direta ou indiretamente, de si próprio.

A ausência de ciclos é condição necessária para:

- evolução independente;

- testes isolados;

- manutenção previsível;

- substituição segura de componentes.

---

# Encerramento

O Modelo de Dependências preserva a organização lógica do Agent OS ao definir como os componentes podem relacionar-se entre si.

Ele garante que a evolução do sistema ocorra por extensão, preservando os princípios estabelecidos pelos documentos fundacionais e evitando que decisões locais comprometam a arquitetura global.