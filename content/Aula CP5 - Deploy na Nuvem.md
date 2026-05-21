---
disciplina: Projetos Integrados 2
codigo: "VIA231"
aula: CP5
titulo: "Deploy na Nuvem — Hospedagem, DNS e Segurança"
tipo: pratica
semana: 14
data: 2026-05-21
status: publicado
tags:
  - projetos-integrados
  - deploy
  - docker
  - cloud
  - aws
  - azure
  - dns
  - ssl
  - hardening
  - devops
publicar: true
---

# 🟢 Aula CP5: Deploy na Nuvem — Hospedagem, DNS e Segurança

**Disciplina:** Projetos Integrados 2 (VIA231)
**Curso:** Inteligência Artificial e Ciência de Dados, Uniube
**Semana:** 14 | Quarta-feira, 21/05/2026
**Professor:** Romualdo Mathias Filho
**Tipo:** 🔬 100% Prática (Hands-On)
**Tópicos:** AWS EC2, Docker Compose em Produção, IP Público, DNS Gratuito, SSL/HTTPS, Hardening

---

> [!INFO] 🎯 Visão Geral da Aula & Recursos
> **Hoje seu projeto sai do localhost e vai para o mundo.** Vocês vão colocar a aplicação do grupo online em uma VM na nuvem, com domínio próprio e HTTPS — exatamente como um sistema real em produção.
>
> **O que você vai dominar:**
> - 🚀 Conectar na VM via SSH e subir sua stack completa com `docker compose`
> - 🌐 Acessar sua aplicação por uma URL real (`grupo.duckdns.org`) com HTTPS
> - 🛡️ Aplicar as primeiras camadas de segurança (firewall, SSH seguro, Fail2Ban)
>
> **📂 Recursos para Download:**
> - 📋 Cheatsheet de comandos → Seção "Resumo Estrutural" desta aula
> - 🐳 `docker-compose.yml` de referência → Módulo 2
> - 🔒 Script de Hardening → Módulo 4 (Material Assíncrono)

---

## 🎯 Objetivo da Aula

Ao final desta aula, os alunos serão capazes de:
- Conectar via SSH em uma VM na nuvem e preparar o ambiente Docker
- Realizar o deploy completo de uma aplicação containerizada (DB + Backend + Frontend) em produção
- Registrar um subdomínio DNS gratuito e configurar HTTPS automático
- Aplicar práticas básicas de hardening em um servidor de produção

---

## 🔄 Revisão Rápida (5 min)

| **Conceito (CP-4 — MVP Completo)** | **Conexão com hoje** |
| --- | --- |
| Docker Compose local | Hoje usamos o **mesmo compose** na nuvem — a portabilidade é o superpoder do Docker |
| Frontend + Backend + Banco integrados | A stack inteira sobe na VM com um único comando |
| README com instruções de execução | Agora o README ganha o link da **URL pública** do sistema hospedado |

---

## 📌 1. Acessando a VM na Nuvem [Hands-On ⏳ 25 min]

### 🏗️ Arquitetura da Nuvem

Antes de colocar a mão na massa, vamos entender o que o professor já preparou para vocês:

```mermaid
flowchart TB
    subgraph AWS["☁️ AWS — EC2 Compartilhada"]
        direction TB
        VPC["🔒 VPC + Security Group<br>Portas: 22, 80, 443, 3001-3010"]
        VM["🖥️ EC2 · Ubuntu 22.04<br>t3.medium · Elastic IP"]
        DOCKER["🐳 Docker Engine<br>+ Docker Compose v2"]
        VPC --> VM --> DOCKER
    end

    subgraph GRUPOS["👥 Grupos (cada um na sua porta)"]
        GA["🔵 Alpha → porta 3001"]
        GB["🟢 Beta → porta 3002"]
        GC["🟡 Gamma → porta 3003"]
    end

    INTERNET["🌐 Internet<br>Elastic IP: 54.207.120.35"]
    
    INTERNET -->|"portas 3001-3010"| VPC
    DOCKER --> GA
    DOCKER --> GB
    DOCKER --> GC
```

**Equivalência AWS ↔ Azure (para quem quiser replicar):**

| Conceito | AWS | Azure |
|----------|-----|-------|
| VM | **EC2** (Elastic Compute Cloud) | **Azure VM** |
| Rede privada | **VPC** (Virtual Private Cloud) | **VNet** (Virtual Network) |
| Firewall de rede | **Security Group** | **NSG** (Network Security Group) |
| IP fixo público | **Elastic IP** | **Public IP** (Static) |
| Par de chaves SSH | **Key Pair** (EC2) | **SSH Public Key** |
| Armazenamento disco | **EBS** (Elastic Block Store) | **Managed Disk** |

> [!TIP] 💡 Dica de Produção (Pro-Tip)
> Em empresas como Nubank e iFood, cada aplicação roda em sua **própria instância EC2 ou cluster ECS/EKS**. Aqui estamos compartilhando uma EC2 por questão didática, mas o fluxo de deploy (SSH → clone → compose up) é **idêntico** ao do mundo real. Os mesmos comandos Docker funcionam em qualquer nuvem — AWS, Azure, GCP — essa é a portabilidade dos containers.

### 🔑 Passo 1: Gerar sua chave SSH (no seu PC)

Se você ainda não tem um par de chaves SSH, gere agora:

```bash
# Windows (PowerShell) ou Linux/Mac (Terminal)
ssh-keygen -t ed25519 -C "seu-email@aluno.uniube.br"
```

- Pressione **Enter** para aceitar o caminho padrão (`~/.ssh/id_ed25519`)
- Defina uma senha (passphrase) ou deixe em branco para testes

Depois, **copie sua chave pública** e envie para o professor:

```bash
# Mostrar a chave pública (copie o conteúdo inteiro)
cat ~/.ssh/id_ed25519.pub
```

### 🔌 Passo 2: Conectar na VM via SSH

```bash
# Exemplo real com usuário do grupo 'alpha' e IP da VM:
ssh alpha@54.207.120.35
```

**Primeira conexão?** O terminal vai perguntar se confia no host — digite `yes`.

Se conectou, parabéns! Você está dentro de um servidor na nuvem. 🎉

> [!WARNING] ⚠️ Gotcha de Infraestrutura
> **Nunca compartilhe sua chave privada** (`id_ed25519` sem `.pub`). A chave privada é como a senha da sua casa — só a pública (`id_ed25519.pub`) é segura para compartilhar. Se vazou a privada, gere um novo par imediatamente.

### 🧠 Checkpoint: Teste seu Conhecimento!

<details>
<summary><b>🔍 Exercício Rápido: Por que usamos chave SSH em vez de senha para acessar servidores?</b></summary>
<blockquote>

**Resposta Correta:** Chaves SSH são exponencialmente mais seguras que senhas porque:
1. **Impossível de adivinhar** — uma chave Ed25519 tem 256 bits de entropia (vs. ~40 bits de uma senha forte)
2. **Imune a brute-force** — não existe "tentar todas as combinações" viável
3. **Sem transmissão de segredo** — a chave privada nunca sai do seu PC; o servidor só vê a pública

É por isso que empresas desativam login por senha em servidores de produção (veremos isso no Hardening).

</blockquote>
</details>

---

## 📌 2. Subindo a Aplicação com Docker Compose [Hands-On ⏳ 30 min]

### 📦 Passo 1: Verificar Docker na VM

```bash
# Confirmar que Docker está instalado e rodando
docker --version
docker compose version

# Ver se há containers de outros grupos rodando
docker ps
```

### 📥 Passo 2: Clonar o repositório do grupo

```bash
# Cada grupo trabalha na sua pasta (exemplo para o grupo alpha)
mkdir -p ~/grupo-alpha
cd ~/grupo-alpha
 
# Clonar o repositório do grupo
git clone https://github.com/grupo-alpha/projeto-ia.git .
```
 
> Se o repositório for **privado**, use um Personal Access Token (Token de Acesso Pessoal):
> ```bash
> git clone https://oauth2:ghp_Y1a2b3c4d5e6f7g8h9i0jK@github.com/grupo-alpha/projeto-ia.git .
> ```

### ⚙️ Passo 3: Configurar variáveis de ambiente

```bash
# Copiar o arquivo de exemplo e editar
cp .env.example .env
nano .env
```

**Exemplo de `.env` para produção:**
```env
# Banco de Dados
DB_NAME=meuprojeto_prod
DB_USER=app_user
DB_PASS=SenhaForte123!@#

# Backend
NODE_ENV=production
API_PORT=4000

# Frontend (Apontando para o IP da VM e porta exposta da API do grupo)
REACT_APP_API_URL=http://54.207.120.35:4001
```

> [!WARNING] ⚠️ Gotcha de Infraestrutura
> **O `.env` NUNCA vai para o Git!** Verifiquem agora se o `.gitignore` do projeto contém a linha `.env`. Se não contém, adicionem imediatamente:
> ```bash
> echo ".env" >> .gitignore
> ```
> Credenciais vazadas no GitLab são o erro #1 de segurança de projetos universitários.

### 🐳 Passo 4: Ajustar o docker-compose.yml para produção

Em produção, precisamos separar as duas fases de deploy da nossa aplicação:

#### 🔄 Fase 1: Acesso Direto (Sem Caddy - Para Testes Iniciais)
Nesta fase, para que o navegador no seu computador local consiga conversar com a API e o frontend, **precisamos expor ambas as portas**. Caso contrário, você terá um erro de conexão de rede.

```yaml
# docker-compose.yml — Modelo para Fase 1 (Acesso Direto via IP)
services:
  db:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASS}
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - interno
    # ⚠️ SEM "ports:" — o banco NUNCA é exposto na internet!

  api:
    build: ./backend
    restart: unless-stopped
    ports:
      - "${PORTA_API:-4001}:4000"  # 🟢 Exposta temporariamente para a internet na Fase 1
    environment:
      DATABASE_URL: postgres://${DB_USER}:${DB_PASS}@db:5432/${DB_NAME}
      NODE_ENV: production
    depends_on:
      - db
    networks:
      - interno

  frontend:
    build: ./frontend
    restart: unless-stopped
    ports:
      - "${PORTA_EXTERNA:-3001}:3000"  # 🟢 Exposta para o tráfego do frontend
    depends_on:
      - api
    networks:
      - interno

volumes:
  pgdata:

networks:
  interno:
    driver: bridge
```

#### 🔒 Fase 2: Produção Segura (Com Caddy - Fechando as Portas)
Quando adicionamos o reverse proxy (Caddy), **fechamos a porta externa da API (removendo `ports:` da api)**. Todo o tráfego de fora entra unicamente pela porta `443` do Caddy, que faz o roteamento interno pela rede do [[Docker]]. Isso protege sua API contra ataques diretos e resolve 100% dos erros de CORS!

> [!WARNING] ⚠️ Gotcha Crítico de Rede (CORS e Localhost)
> **O maior erro de iniciantes:** Configurar a variável `REACT_APP_API_URL` como `http://api:4000` ou `http://localhost:4000`.
> - **Por que falha?** O React roda no **navegador do usuário** (client-side), e não dentro do servidor Docker. O navegador do usuário não sabe o que é `api` (DNS do Docker) e, se tentar acessar `localhost`, buscará a API no próprio computador do aluno!
> - **Solução na Fase 1:** A variável deve apontar para o IP público e a porta exposta da API: `REACT_APP_API_URL=http://54.207.120.35:4001`.
> - **Solução na Fase 2:** Apontar para o domínio com HTTPS: `REACT_APP_API_URL=https://grupo-alpha.duckdns.org/api` (ou usar caminhos relativos `/api` se o Caddy estiver configurado corretamente).

**Tabela de portas por grupo (definida pelo professor):**

| Grupo | Porta Frontend (Fase 1 e 2) | Porta API (Apenas Fase 1) | Acesso Inicial (Fase 1) |
|-------|-----------------------------|---------------------------|-------------------------|
| 🔵 Alpha | 3001 | 4001 | Frontend: `http://54.207.120.35:3001` <br> API: `http://54.207.120.35:4001` |
| 🟢 Beta | 3002 | 4002 | Frontend: `http://54.207.120.35:3002` <br> API: `http://54.207.120.35:4002` |
| 🟡 Gamma | 3003 | 4003 | Frontend: `http://54.207.120.35:3003` <br> API: `http://54.207.120.35:4003` |
| 🔴 Delta | 3004 | 4004 | Frontend: `http://54.207.120.35:3004` <br> API: `http://54.207.120.35:4004` |
| 🟣 Epsilon | 3005 | 4005 | Frontend: `http://54.207.120.35:3005` <br> API: `http://54.207.120.35:4005` |

> Editem o arquivo `.env` do grupo com as portas corretas:
> ```env
> PORTA_EXTERNA=3001
> PORTA_API=4001
> ```

### 🚀 Passo 5: Subir a aplicação!

```bash
# Construir imagens e subir todos os containers em segundo plano
docker compose up -d --build

# Verificar se tudo está rodando e quais portas estão expostas
docker compose ps

# Ver logs em tempo real (Ctrl+C para sair)
docker compose logs -f

# Ver logs só do backend (útil para debugar erros de conexão)
docker compose logs -f api
```

**Resultado esperado de `docker compose ps`:**

```
NAME          SERVICE     STATUS    PORTS
grupo-db-1    db          running   5432/tcp
grupo-api-1   api         running   0.0.0.0:4001->4000/tcp
grupo-fe-1    frontend    running   0.0.0.0:3001->3000/tcp
```

> [!NOTE] 💼 Pergunta de Entrevista
> **"Explique a diferença entre `docker compose up` e `docker compose up -d`."**
>
> **Resposta ideal (nível Júnior+):** O flag `-d` significa *detached mode* — os containers rodam em segundo plano, liberando o terminal. Sem `-d`, os logs ficam presos no terminal e se você fechar a sessão [[SSH]], os containers morrem junto. Em produção, **sempre** usamos `-d`. Para ver os logs depois, usamos `docker compose logs -f`.

---

## 📌 3. IP Público + DNS Gratuito + HTTPS [Hands-On ⏳ 25 min]

### 🌐 Testando o acesso via IP
 
Agora que os containers estão rodando, abra o navegador no seu PC e acesse:
 
```
http://54.207.120.35:3001
```

Se a tela do seu frontend apareceu — **sua aplicação está online!** 🎉

Mas... acessar por IP e porta não é profissional. Vamos resolver isso.

### 🦆 Passo 1: Registrar um subdomínio gratuito no DuckDNS

O [[DuckDNS]] é um serviço gratuito de DNS dinâmico que dá subdomínios `.duckdns.org` para qualquer pessoa:

1. Acesse **[duckdns.org](https://www.duckdns.org)**
2. Faça login com sua conta **GitHub**
3. No campo "sub domain", digite o nome desejado (ex: `grupo-alpha`)
4. Clique em **"add domain"**
5. No campo "current ip", coloque o **IP Público da VM** (ex: `54.207.120.35`)
6. Clique em **"update ip"**

**Pronto!** Agora `grupo-alpha.duckdns.org` aponta para sua VM.

Teste no navegador:
```
http://grupo-alpha.duckdns.org:3001
```

### 🔒 Passo 2: Adicionar HTTPS com Caddy (SSL Automático)

O [[Caddy]] é um servidor web moderno e leve, muito utilizado em práticas de [[SRE]], que obtém certificados SSL do [[Let's Encrypt]] **automaticamente**, sem nenhuma configuração manual.

#### 🛠️ Abordagem A: Teste Sequencial (Caddy no Docker do Grupo)
Para testar o Caddy diretamente no seu projeto, **adicione o serviço ao seu `docker-compose.yml`**:

```yaml
  caddy:
    image: caddy:2-alpine
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"  # HTTP/3
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    depends_on:
      - frontend
    networks:
      - interno
```

> ⚠️ **Atenção:** Como estamos compartilhando uma única VM, apenas **UM** grupo por vez pode rodar o Caddy escutando nas portas `80` e `443` do host. O professor organizará a execução sequencial na hora da aula para cada grupo ver seu SSL ativo.

**Criar o arquivo `Caddyfile`** (na raiz do projeto):

```caddy
# Caddyfile unificado: resolve HTTPS e elimina CORS!
grupo-alpha.duckdns.org {
    # 1. Roteia chamadas /api para a API interna na rede do Docker
    handle_path /api/* {
        reverse_proxy api:4000
    }

    # 2. Roteia todo o restante para o frontend
    handle {
        reverse_proxy frontend:3000
    }
}
```

**Por que este Caddyfile de 9 linhas é genial?**
- **SSL Automático:** O Caddy obtém o certificado TLS da Let's Encrypt para seu subdomínio sem você fazer nada.
- **Adeus CORS:** Como tanto o frontend quanto a API são acessados sob o mesmo domínio (`grupo-alpha.duckdns.org`), o navegador não bloqueia as chamadas e o CORS deixa de ser um problema!
- **Variáveis de Ambiente Limpas:** O seu frontend React pode usar caminhos relativos para a API: `REACT_APP_API_URL=/api`.

**Subir a stack com o Caddy:**

```bash
# Recriar os containers aplicando o Caddy
docker compose up -d --build

# Verificar os logs do Caddy (aqui você vê o Let's Encrypt emitindo o certificado!)
docker compose logs -f caddy
```

---

#### 🌐 Abordagem B: Multi-Tenant Simultâneo (Caddy Central do Professor)
Para que **todos os grupos rodem simultaneamente com HTTPS** sem conflito de portas, o professor configurou um **Caddy Central** no host da VM. 
Nesse modelo, os grupos **não rodam** o container Caddy. Eles apenas expõem suas portas (ex: frontend em `3001` e API em `4001`), e o Caddy do professor faz o roteamento inteligente:

```mermaid
flowchart TD
    USER["👤 Navegador do Usuário"]
    DNS["🦆 DuckDNS (*.duckdns.org)"]
    
    subgraph VM["🖥️ VM EC2 Compartilhada"]
        CADDY["🔒 Caddy Central (Porta 443)<br>Gerencia SSLs automaticamente"]
        
        subgraph G_ALPHA["🔵 Grupo Alpha"]
            FE_A["Frontend (Porta 3001)"]
            API_A["API (Porta 4001)"]
        end
        
        subgraph G_BETA["🟢 Grupo Beta"]
            FE_B["Frontend (Porta 3002)"]
            API_B["API (Porta 4002)"]
        end
    end

    USER -->|"1. Acessa pi2-alpha.duckdns.org"| DNS
    DNS -->|"2. Resolve IP"| CADDY
    
    CADDY -->|"pi2-alpha.duckdns.org"| FE_A
    CADDY -->|"pi2-alpha.duckdns.org/api/*"| API_A
    
    CADDY -->|"pi2-beta.duckdns.org"| FE_B
    CADDY -->|"pi2-beta.duckdns.org/api/*"| API_B
```

> [!TIP] 💡 Dica de Produção (Pro-Tip)
> O **Caddy** substitui ferramentas legadas como o [[NGINX]] + Certbot em startups e MVPs devido à facilidade de configuração (SSL nativo, sem cron jobs de renovação). Em grandes infraestruturas com centenas de subdomínios, soluções como Caddy ou gateways do Kubernetes gerenciam milhares de certificados por segundo com absoluta estabilidade.

---

## 📌 4. Hardening — Segurança do Servidor [Material Assíncrono ⏳ 20 min]

> ⚠️ **Este módulo é para vocês aplicarem DEPOIS da aula.** O conteúdo será cobrado na AMOSTRATEC. Leiam, apliquem na VM, e tragam dúvidas na próxima aula.

### 🛡️ Checklist de Segurança (aplicar na ordem)

#### 1. SSH: Desabilitar login por senha

```bash
# Editar configuração do SSH
sudo nano /etc/ssh/sshd_config

# Encontrar e alterar estas linhas:
PasswordAuthentication no
PubkeyAuthentication yes

# Reiniciar o serviço SSH
sudo systemctl restart sshd
```

> ⚠️ **CUIDADO:** Antes de desabilitar senha, **confirme que sua chave SSH funciona!** Se desabilitar senha sem chave configurada, você perde acesso ao servidor permanentemente.

#### 2. Firewall: Bloquear portas desnecessárias

```bash
# Instalar e configurar UFW (Uncomplicated Firewall)
sudo apt install ufw -y

# Política padrão: bloquear TUDO que entra, liberar TUDO que sai
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Liberar apenas o necessário
sudo ufw allow 22/tcp     # SSH
sudo ufw allow 80/tcp     # HTTP (redireciona para HTTPS)
sudo ufw allow 443/tcp    # HTTPS

# Ativar o firewall
sudo ufw enable

# Verificar regras
sudo ufw status verbose
```

#### 3. Fail2Ban: Proteção contra brute-force

```bash
# Instalar
sudo apt install fail2ban -y

# Ativar e iniciar
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Ver status (IPs bloqueados)
sudo fail2ban-client status sshd
```

O [[Fail2Ban]] monitora os logs do SSH e **bloqueia automaticamente** qualquer IP que errar a senha mais de 5 vezes em 10 minutos.

#### 4. Docker: Não rodar containers como root

No `Dockerfile` do seu backend (exemplo para Node.js):

```dockerfile
FROM node:20-alpine

# Criar usuário não-root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .

# Trocar para o usuário não-root ANTES de executar
USER appuser

EXPOSE 4000
CMD ["node", "server.js"]
```

#### 5. Atualizações automáticas de segurança

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure -plow unattended-upgrades
```

### 📊 Tabela-Cheatsheet de Segurança

| O Que Fazer | Como | Por Quê |
|-------------|------|---------|
| Desativar SSH por senha | `PasswordAuthentication no` | Elimina brute-force |
| Firewall restritivo | UFW: só 22, 80, 443 | Menor superfície de ataque |
| Fail2Ban ativo | `apt install fail2ban` | Bloqueia bots automáticos |
| `.env` no `.gitignore` | `echo .env >> .gitignore` | Evita vazamento de credenciais |
| Container não-root | `USER appuser` no Dockerfile | Previne escalação de privilégios |
| Updates automáticos | `unattended-upgrades` | Patches de segurança sem esquecer |
| Porta do banco fechada | Sem `ports:` no compose | DB inacessível pela internet |
| Imagens com tag fixa | `postgres:16-alpine` | Reprodutibilidade e segurança |

> [!NOTE] 💼 Pergunta de Entrevista
> **"Cite 3 medidas de segurança que você aplicaria ao colocar uma aplicação Docker em produção."**
>
> **Resposta ideal (nível Pleno):**
> 1. **Rede interna Docker** — banco e API sem portas expostas, acessíveis apenas pela rede bridge interna
> 2. **SSH por chave + Fail2Ban** — desabilitar login por senha e bloquear IPs com tentativas falhas
> 3. **Containers como non-root** — usar `USER` no Dockerfile para minimizar impacto de vulnerabilidades

---

## 📋 Resumo Estrutural

| **Conceito/Comando** | **Definição/Aplicação Prática** |
| --- | --- |
| EC2 (AWS) / Azure VM | Máquina virtual na nuvem — seu servidor de produção |
| Security Group / NSG | Firewall de rede na nuvem — controla quais portas estão abertas |
| Elastic IP / Public IP | IP público fixo associado à sua instância |
| `ssh usuario@IP` | Conectar remotamente em um servidor via protocolo SSH |
| `ssh-keygen -t ed25519` | Gerar par de chaves criptográficas para autenticação sem senha |
| `docker compose up -d --build` | Construir imagens e subir todos os containers em segundo plano |
| `docker compose ps` | Listar containers rodando e seus status |
| `docker compose logs -f` | Acompanhar logs em tempo real (debug) |
| `docker compose down` | Parar e remover todos os containers da stack |
| DuckDNS | Serviço gratuito de DNS dinâmico — subdomínios `.duckdns.org` |
| Caddy | Servidor web que obtém certificados SSL automaticamente |
| `Caddyfile` | Arquivo de configuração do Caddy (3 linhas para HTTPS!) |
| `reverse_proxy` | Redirecionar tráfego externo para um container interno |
| UFW | Firewall simplificado do Ubuntu — controla portas abertas (dentro da VM) |
| Fail2Ban | Proteção automática contra tentativas de brute-force SSH |
| `.env` + `.gitignore` | Variáveis sensíveis fora do código-fonte |
| `USER appuser` | Diretiva no Dockerfile para rodar como não-root |
| Rede Docker interna | Containers se comunicam sem expor portas para a internet |

---

%%
## ❓ Banco de Questões

> 🔒 Esta seção é visível apenas no Obsidian do professor. Não publicada.

### Questão 1: Prática (Múltipla Escolha — Nível: Intermediário)

**Enunciado:** Um grupo de alunos subiu sua aplicação com Docker Compose em uma VM na nuvem. O `docker-compose.yml` contém três serviços: `db` (PostgreSQL), `api` (Node.js) e `frontend` (React). Ao acessar `http://IP:3001`, o frontend carrega mas não consegue se comunicar com a API. O aluno verifica que `docker compose ps` mostra todos os containers como "running". Qual é a causa mais provável do problema?

- [ ] A) O PostgreSQL não está aceitando conexões externas
- [ ] B) O container da API não está na mesma rede Docker que o frontend
- [x] C) A variável de ambiente `REACT_APP_API_URL` no frontend está apontando para `localhost` em vez do IP/hostname correto ✅
- [ ] D) O Caddy não está configurado como reverse proxy

**Justificativa:** Em ambiente Docker, quando o frontend roda no navegador do usuário (client-side rendering), as requisições à API partem do **navegador do cliente**, não do container. Portanto, `localhost` no frontend aponta para a máquina do usuário, não para o container da API. A URL deve apontar para o IP público da VM ou para o domínio DuckDNS. A opção B seria válida se o erro fosse de comunicação entre containers, mas containers na mesma rede Docker se resolvem por nome de serviço.

---

### Questão 2: Prática (Múltipla Escolha — Nível: Intermediário)

**Enunciado:** Ao configurar o `docker-compose.yml` para produção, um aluno expôs a porta do PostgreSQL assim: `ports: - "5432:5432"`. O professor apontou isso como uma falha grave de segurança. Por quê?

- [ ] A) Porque o PostgreSQL não suporta conexões externas por padrão
- [ ] B) Porque a porta 5432 já está sendo usada pelo sistema operacional
- [x] C) Porque qualquer pessoa na internet pode tentar acessar o banco de dados diretamente, explorando senhas fracas ou vulnerabilidades conhecidas ✅
- [ ] D) Porque o Docker não permite mapear portas de bancos de dados

**Justificativa:** Expor a porta do banco de dados para o mundo (`0.0.0.0:5432`) permite que qualquer atacante tente conexão direta. Bots automatizados varrem a internet constantemente buscando PostgreSQL e MySQL expostos, tentando senhas padrão. A prática correta é manter o banco **apenas na rede Docker interna** (`networks: interno`) sem `ports:` mapeadas, acessível somente pelos containers da mesma stack.

---

### Questão 3: Dissertativa Conceitual (Nível: Intermediário)

**Enunciado:** Explique, com suas palavras, o que é um "reverse proxy" e por que utilizamos o Caddy nessa função ao invés de expor o frontend diretamente na porta 443.

**Resposta esperada:**

Um reverse proxy é um servidor intermediário que recebe todas as requisições da internet e as encaminha para o serviço interno correto. O Caddy atua como reverse proxy recebendo o tráfego HTTPS na porta 443 e redirecionando para o container do frontend (porta 3000 interna).

Os benefícios são:
1. **SSL/TLS centralizado** — apenas o Caddy precisa do certificado, não cada serviço individualmente
2. **Segurança** — os containers internos não são expostos diretamente à internet
3. **Flexibilidade** — podemos rotear diferentes domínios para diferentes containers (ex: `api.grupo.duckdns.org` → API, `grupo.duckdns.org` → Frontend)
4. **HTTP/2 e HTTP/3** — o Caddy habilita protocolos modernos automaticamente

Sem o reverse proxy, seria necessário configurar SSL em cada serviço separadamente e expor múltiplas portas, aumentando a superfície de ataque.

---
%%

## 📄 Artigo de Aprofundamento

- [Use Compose in Production — Docker Official Documentation](https://docs.docker.com/compose/how-tos/production/)
> *Resumo prático: A documentação oficial do Docker detalha as diferenças cruciais entre um `docker-compose.yml` de desenvolvimento e um de produção: remover bind mounts, usar volumes nomeados, definir `restart: always`, não expor portas desnecessárias e separar configurações com múltiplos arquivos Compose. É leitura obrigatória para quem acabou de sair do `localhost`.*

- [Getting Started — Caddy Documentation](https://caddyserver.com/docs/getting-started)
> *Resumo prático: O guia oficial do Caddy explica como o servidor obtém certificados HTTPS automaticamente via ACME/Let's Encrypt e como configurar reverse proxy com apenas 3 linhas. Ideal para entender por que o Caddy simplifica drasticamente o deploy comparado ao NGINX + Certbot.*

---

## 📚 Referências Bibliográficas e Citações

- **DOCKER, Inc.**, *Docker Documentation — Compose in Production*. Docker, 2025. Disponível em: https://docs.docker.com/compose/how-tos/production/
- **CADDY**, *Caddy Documentation — Getting Started*. Caddy, 2025. Disponível em: https://caddyserver.com/docs/getting-started
- **DUCKDNS**, *DuckDNS — Free Dynamic DNS*. DuckDNS, 2025. Disponível em: https://www.duckdns.org/
- **TANENBAUM, Andrew S.; WETHERALL, David J.**, *Redes de Computadores*. 5ª ed. Pearson, 2011. **Capítulo 8: Segurança de Redes, pp. 513–590** — Fundamentos de criptografia, SSL/TLS e autenticação por chaves.
- **STALLINGS, William**, *Criptografia e Segurança de Redes*. 6ª ed. Pearson, 2014. **Capítulo 17: Segurança em Nível de Transporte, pp. 497–522** — Protocolo TLS e certificados digitais.
- **KANE, Sean P.; MATTHIAS, Karl**, *Docker: Up & Running*. 3rd ed. O'Reilly Media, 2023. **Capítulo 11: Production Containers, pp. 245–278** — Boas práticas de segurança e deploy Docker em produção.

---
*Última atualização: 2026-05-21 | Status: publicado*
