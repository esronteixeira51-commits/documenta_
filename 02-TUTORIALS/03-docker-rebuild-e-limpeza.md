# Apostila Docker para Desenvolvimento Continuo

> **Para:** Desenvolvedores que precisam rebuildar containers frequentemente sem encher o HD.
> **Versao:** 1.0 | 2026-07-10

---

## O Problema

Durante o desenvolvimento voce:
- Rebuilda containers a cada alteracao no codigo
- Acumula imagens antigas, layers de build, volumes e logs
- O HD enche rapidamente (especialmente no Windows com WSL2)
- Precisa de um fluxo **rapido** para testar e **seguro** para limpar

---

## PARTE 1 - REBUILD RAPIDO (Durante Desenvolvimento)

Use quando alterou codigo e quer testar as mudancas.

### 1.1 Rebuild de um servico especifico
```bash
# Rebuilda apenas o servico que voce alterou
docker compose up -d --build nome-do-servico

# Exemplo: se alterou o backend
docker compose up -d --build backend
```

### 1.2 Rebuild de tudo (quando alterou dependencias, Dockerfile, etc.)
```bash
# Rebuilda TODOS os servicos
docker compose up -d --build

# Forca recriacao dos containers (remove e cria do zero)
docker compose up -d --build --force-recreate
```

### 1.3 Rebuild SEM cache (quando o cache esta causando problemas)
```bash
docker compose build --no-cache
docker compose up -d

# Ou em um comando:
docker compose up -d --build --force-recreate --no-deps
```

### 1.4 Ver logs em tempo real apos rebuild
```bash
# Todos os servicos
docker compose logs -f

# Apenas um servico
docker compose logs -f nome-do-servico

# Ultimas 50 linhas
docker compose logs --tail=50 nome-do-servico
```

### 1.5 Hot-reload (evita rebuild constante)

Configure seu docker-compose.yml para montar o codigo como volume:

```yaml
services:
  backend:
    build: ./backend
    volumes:
      - ./backend:/app        # Monta codigo local no container
      - /app/node_modules     # Preserva node_modules do container
    environment:
      - NODE_ENV=development
    command: npm run dev       # Usa nodemon ou similar
```

> **Dica:** Com hot-reload, voce edita o codigo local e o container atualiza automaticamente - **sem rebuild**.

---

## PARTE 2 - LIMPEZA QUANDO O HD ENCHE

Use quando o disco esta cheio ou o Docker esta lento.

### 2.1 Diagnostico rapido
```bash
# Ver o que esta ocupando espaco
docker system df

# Ver detalhado (containers, imagens, volumes, cache)
docker system df -v

# Ver build cache especifico
docker builder du
```

### 2.2 Limpeza Leve (mantem dados importantes)
```bash
# Remove containers parados
docker container prune -f

# Remove imagens dangling (sem tag)
docker image prune -f

# Remove networks nao utilizadas
docker network prune -f

# Remove build cache nao utilizado
docker builder prune -f
```

### 2.3 Limpeza Completa (remove tudo nao utilizado)
```bash
# Remove: containers parados, imagens nao utilizadas,
# networks nao utilizadas, build cache
docker system prune -f

# Remove TUDO incluindo volumes (CUIDADO!)
docker system prune -a --volumes -f
```

> **Atencao:** --volumes apaga volumes nao utilizados. Se tiver banco de dados persistente, faca backup antes.

### 2.4 Limpeza Nuclear no Windows (WSL2) - O GRANDE VILAO DO HD

No Windows, o Docker Desktop roda dentro de uma VM WSL2. O arquivo ext4.vhdx cresce indefinidamente e **nao encolhe sozinho**.

#### Passo a passo:

```powershell
# 1. PARAR TUDO
wsl --shutdown
Stop-Process -Name "Docker Desktop" -Force -ErrorAction SilentlyContinue

# 2. COMPACTAR O DISCO (libera espaco sem perder dados)
wsl --manage docker-desktop-data --compact
wsl --manage docker-desktop --compact

# 3. REINICIAR DOCKER DESKTOP
Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe"
```

> Se os comandos wsl --manage nao funcionarem (WSL antigo), use o diskpart conforme a Secao 3.3.

### 2.5 Reset Total (quando nada mais funciona)

```powershell
# 1. Fecha tudo
wsl --shutdown
Stop-Process -Name "Docker Desktop" -Force -ErrorAction SilentlyContinue

# 2. Remove os dados do WSL (forma correta de deletar)
wsl --unregister docker-desktop-data
wsl --unregister docker-desktop

# 3. Se as distribuicoes nao existirem mais, remove a pasta lixeiro
Remove-Item -Path "C:\Users\$env:USERNAME\AppData\Local\Docker\wsl" -Recurse -Force

# 4. Reinicia o Docker Desktop (ele recria tudo limpo)
Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe"
```

> **Atencao:** Isso apaga **TODOS** os containers, imagens e volumes. Use como ultimo recurso.

---

## PREVENCAO - Evitar que o HD Encha

### 3.1 Limitar logs no docker-compose.yml
```yaml
services:
  seu-servico:
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### 3.2 Limitar logs globalmente (Docker Desktop)

Va em: **Docker Desktop** -> Configuracoes -> **Docker Engine** -> edite o JSON:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

### 3.3 Script de manutencao automatica (agende no Windows)

Salve como `limpar-docker.ps1` e execute semanalmente:

```powershell
# limpar-docker.ps1
Write-Host "=== Limpando Docker ===" -ForegroundColor Cyan

# Containers parados
docker container prune -f

# Imagens dangling
docker image prune -f

# Build cache
docker builder prune -f

# Compactar disco WSL
wsl --shutdown
Start-Sleep -Seconds 3
wsl --manage docker-desktop-data --compact
wsl --manage docker-desktop --compact

Write-Host "=== Limpo! ===" -ForegroundColor Green
```

Para agendar no Windows:
1. Pressione `Win + R` -> digite `taskschd.msc`
2. Crie tarefa basica -> semanal -> execute o script `.ps1`

### 3.4 Usar .dockerignore

Crie um arquivo `.dockerignore` na raiz do projeto para evitar copiar lixo para o build:

```
node_modules
.git
.env
*.log
dist
build
.vscode
.idea
```

> Menos lixo no build = menos layers = menos espaco no cache.

---

## FLUXO DE TRABALHO RECOMENDADO

```
+-----------------------------------------+
|  1. ALTEROU O CODIGO?                   |
|     -> docker compose up -d --build     |
|     (ou hot-reload se configurado)      |
+-----------------------------------------+
|  2. ALTEROU DOCKERFILE/DEPENDENCIAS?    |
|     -> docker compose up -d --build      |
|       --force-recreate --no-cache       |
+-----------------------------------------+
|  3. HD ESTA CHEIO?                      |
|     -> docker system df                 |
|     -> docker system prune -a -f        |
|     -> wsl --manage docker-desktop-data |
|       --compact                         |
+-----------------------------------------+
|  4. DOCKER TRAVOU/LOUCURA?              |
|     -> docker compose down              |
|     -> wsl --unregister docker-desktop-*|
|     -> Reinicia Docker Desktop          |
|     -> docker compose up -d --build     |
+-----------------------------------------+
```

---

## COMANDOS DE EMERGENCIA

| Situacao | Comando |
|----------|---------|
| Container travou | `docker compose restart` |
| Container nao sobe | `docker compose logs --tail=50` |
| Porta em uso | `docker compose down` + `docker container prune -f` |
| Imagem corrompida | `docker compose build --no-cache` |
| Tudo parou de funcionar | `docker system prune -a --volumes -f` |
| HD cheio no Windows | `wsl --manage docker-desktop-data --compact` |

---

## DICAS FINAIS

1. **Use `.env` para variaveis** - evita rebuildar so pra mudar config
2. **Multi-stage builds** no Dockerfile - imagens finais menores
3. **Volume para dados persistentes** - nao perde DB ao rebuildar
4. **Monitore o HD** - `docker system df` semanalmente
5. **Nunca delete arquivos `.vhdx` manualmente** - sempre use `wsl --unregister`

---

*Gerado em 2026-07-10 para o projeto AgentOS Community Edition*