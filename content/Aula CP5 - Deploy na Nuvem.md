---
disciplina: Projetos Integrados 2
codigo: "VIA231"
aula: CP5
titulo: "Deploy na Nuvem — Hospedagem, DNS e Segurança"
tipo: pratica
semana: 12
data: 2026-10-29
status: publicado
tags:
  - projetos-integrados
  - deploy
  - docker
  - cloud
  - aws
  - aws-academy
  - dns
  - ssl
  - hardening
  - devops
publicar: true
---

# 🟢 Aula CP5: Deploy na Nuvem — Hospedagem, DNS e Segurança

**Disciplina:** Projetos Integrados 2 (VIA231)
**Curso:** Inteligência Artificial e Ciência de Dados, Uniube
**Semana:** 12 | Quinta-feira, 29/10/2026
**Professor:** Romualdo Mathias Filho
**Tipo:** 🔬 100% Prática (Hands-On)
**Tópicos:** AWS Academy Learner Lab, EC2, Docker Compose em Produção, DNS Gratuito, SSL/HTTPS, Hardening

---

> [!INFO] 🎯 Visão Geral da Aula & Recursos
> **Hoje seu projeto sai do localhost e vai para o mundo.** Cada grupo vai criar seu próprio servidor na AWS, colocar a aplicação online com domínio próprio e HTTPS — exatamente como um sistema real em produção.
>
> **O que você vai dominar:**
> - 🏗️ Criar uma instância EC2 na AWS e instalar Docker na nuvem
> - 🚀 Subir sua stack completa (DB + Backend + Frontend) com `docker compose`
> - 🌐 Acessar sua aplicação por uma URL real (`meu-grupo.duckdns.org`) com HTTPS
> - 🛡️ Aplicar as primeiras camadas de segurança (firewall, SSH seguro, Fail2Ban)
>
> **📂 Recursos para Download:**
> - 📋 Cheatsheet de comandos → Seção "Resumo Estrutural" desta aula
> - 🐳 `docker-compose.yml` de referência → Módulo 2
> - 🔒 Script de Hardening → Módulo 4 (Material Assíncrono)

---

## 🎯 Objetivo da Aula

Ao final desta aula, os alunos serão capazes de:
- Criar e configurar uma instância EC2 na AWS usando o Learner Lab
- Realizar o deploy completo de uma aplicação containerizada (DB + Backend + Frontend) em produção
- Registrar um subdomínio DNS gratuito e configurar HTTPS automático com Caddy
- Aplicar práticas básicas de hardening em um servidor de produção

---

## 🔄 Revisão Rápida (5 min)

| **Conceito (CP-4 — MVP Completo)** | **Conexão com hoje** |
| --- | --- |
| Docker Compose local | Hoje usamos o **mesmo compose** na nuvem — a portabilidade é o superpoder do Docker |
| Frontend + Backend + Banco integrados | A stack inteira sobe na VM com um único comando |
| README com instruções de execução | Agora o README ganha o link da **URL pública** do sistema hospedado |

---

## 📌 1. Criando sua VM na Nuvem (AWS Academy) [Hands-On ⏳ 25 min]

### 🏗️ Arquitetura — Cada Grupo com sua VM

Diferente de um ambiente corporativo onde o DevOps prepara a infra, hoje **vocês vão criar o servidor do zero**. Cada grupo terá sua própria máquina virtual (EC2) na AWS:

```mermaid
flowchart TB
    subgraph AWS["☁️ AWS Academy — Learner Lab"]
        direction TB
        subgraph GA["🔵 Grupo Alpha"]
            EC2A["🖥️ EC2 · Ubuntu 22.04<br>t3.small · IP Público"]
            DA["🐳 Docker: DB + API + Frontend + Caddy"]
            EC2A --> DA
        end

        subgraph GB["🟢 Grupo Beta"]
            EC2B["🖥️ EC2 · Ubuntu 22.04<br>t3.small · IP Público"]
            DB["🐳 Docker: DB + API + Frontend + Caddy"]
            EC2B --> DB
        end
    end

    DNSA["🦆 alpha.duckdns.org"]
    DNSB["🦆 beta.duckdns.org"]
    
    DNSA --> EC2A
    DNSB --> EC2B
    
    INTERNET["🌐 Internet (HTTPS)"] --> DNSA
    INTERNET --> DNSB
```

> [!TIP] 💡 Dica de Produção (Pro-Tip)
> Em empresas como Nubank e iFood, cada aplicação roda em sua **própria instância EC2 ou cluster ECS/EKS** — exatamente como vocês estão fazendo agora! O fluxo de deploy (criar VM → instalar Docker → clone → compose up) é **idêntico** ao do mundo real.

### 🖥️ Passo 1: Acessar o AWS Academy Learner Lab

1. Acesse o **Canvas** da disciplina
2. Clique em **"AWS Academy Learner Lab"**
3. Clique em **"Start Lab"** (o indicador fica 🟢 verde quando pronto — aguarde ~2 min)
4. Clique em **"AWS"** (abre o Console AWS em uma nova aba)

### 🔑 Passo 2: Baixar a chave SSH do laboratório

Antes de criar a VM, baixe a chave de acesso:

1. Na página do Learner Lab, clique em **"AWS Details"**
2. Clique em **"Download PEM"** (ou **Download PPK** se usar PuTTY no Windows)
3. Salve o arquivo `labsuser.pem` em uma pasta conhecida (ex: `~/Downloads/`)

**No Linux/Mac**, defina as permissões corretas:
```bash
chmod 400 ~/Downloads/labsuser.pem
```

> [!WARNING] ⚠️ Gotcha de Infraestrutura
> **Nunca compartilhe o arquivo `labsuser.pem`!** Ele é sua chave privada — qualquer pessoa com este arquivo pode acessar seus servidores. Se vazou, encerre o lab e inicie um novo.

### 🏗️ Passo 3: Criar a instância EC2

No Console AWS (a aba que abriu no passo 1):

1. Pesquise **"EC2"** na barra de busca → clique em **EC2**
2. Clique em **"Launch instance"** (botão laranja)
3. Configure:

| Campo | Valor |
|-------|-------|
| **Name** | `deploy-NOME-DO-GRUPO` (ex: `deploy-alpha`) |
| **AMI** | Ubuntu Server 22.04 LTS (Free tier eligible) |
| **Instance type** | `t3.small` (2 vCPU, 2 GB RAM) |
| **Key pair** | Selecione **`vockey`** |
| **Security Group** | Clique em **"Create security group"** → marque ✅ **Allow SSH**, ✅ **Allow HTTPS**, ✅ **Allow HTTP** |

4. Clique em **"Launch instance"** 🚀

**Aguarde ~1 minuto** e sua VM estará rodando!

### 🌐 Passo 4: Descobrir o IP Público da VM

1. No painel EC2, clique em **"Instances"**
2. Clique no nome da sua instância (`deploy-alpha`)
3. Copie o **"Public IPv4 address"** (ex: `54.89.123.45`)

> ⚠️ **Atenção:** Este IP é temporário — ele muda se você parar e reiniciar a instância. Para a aula de hoje, isso não é problema.

### 🔌 Passo 5: Conectar na VM via SSH

```bash
# Substituir pelo IP que você copiou no passo anterior
ssh -i ~/Downloads/labsuser.pem ubuntu@54.89.123.45
```

**Primeira conexão?** O terminal vai perguntar se confia no host — digite `yes`.

Se conectou, parabéns! Você está dentro de um servidor na nuvem. 🎉

### ⚙️ Passo 6: Instalar Docker na VM

A VM vem "limpa" — precisamos instalar o Docker e o Git:

```bash
# Atualizar o sistema
sudo apt update && sudo apt upgrade -y

# Instalar Docker (script oficial)
curl -fsSL https://get.docker.com | sudo sh

# Adicionar seu usuário ao grupo docker (evita precisar de sudo)
sudo usermod -aG docker ubuntu
newgrp docker

# Instalar Git
sudo apt install -y git

# Verificar se tudo está instalado
docker --version
docker compose version
git --version
```

**Resultado esperado:**
```
Docker version 27.x.x
Docker Compose version v2.x.x
git version 2.x.x
```

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

## 📌 2. Subindo a Aplicação com Docker Compose [Hands-On ⏳ 25 min]

### 📥 Passo 1: Clonar o repositório do grupo

```bash
# Criar pasta e clonar o projeto
mkdir -p ~/meu-projeto
cd ~/meu-projeto

# Clonar o repositório do grupo (exemplo)
git clone https://github.com/grupo-alpha/projeto-ia.git .
```

> Se o repositório for **privado**, use um Personal Access Token (Token de Acesso Pessoal):
> ```bash
> git clone https://oauth2:ghp_Y1a2b3c4d5e6f7g8h9i0jK@github.com/grupo-alpha/projeto-ia.git .
> ```

### ⚙️ Passo 2: Configurar variáveis de ambiente

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

# Frontend — Na Fase 1 (teste), aponte para o IP público:
REACT_APP_API_URL=http://SEU_IP_PUBLICO:4000
# Na Fase 2 (com Caddy/HTTPS), mude para caminho relativo:
# REACT_APP_API_URL=/api
```

> [!WARNING] ⚠️ Gotcha de Infraestrutura
> **O `.env` NUNCA vai para o Git!** Verifiquem agora se o `.gitignore` do projeto contém a linha `.env`. Se não contém, adicionem imediatamente:
> ```bash
> echo ".env" >> .gitignore
> ```
> Credenciais vazadas no GitHub são o erro #1 de segurança de projetos universitários.

### 🐳 Passo 3: Preparar o docker-compose.yml para produção

Como cada grupo tem sua **própria VM**, não precisamos mapear portas por grupo. Começamos com acesso direto para testar:

```yaml
# docker-compose.yml — Fase 1 (Teste via IP Público)
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
      - "4000:4000"  # 🟡 Exposta temporariamente para testes (remover na Fase 2)
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
      - "80:3000"  # 🟢 Acesso direto via IP na porta 80
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

> [!WARNING] ⚠️ Gotcha Crítico de Rede (CORS e Localhost)
> **O maior erro de iniciantes:** Configurar a variável `REACT_APP_API_URL` como `http://api:4000` ou `http://localhost:4000`.
> - **Por que falha?** O React roda no **navegador do usuário** (client-side), e não dentro do servidor Docker. O navegador do usuário não sabe o que é `api` (DNS do Docker) e, se tentar acessar `localhost`, buscará a API no próprio computador do aluno!
> - **Solução na Fase 1:** A variável deve apontar para o IP público: `REACT_APP_API_URL=http://54.89.123.45:4000`.
> - **Solução na Fase 2:** Usar caminho relativo: `REACT_APP_API_URL=/api` (o Caddy roteia automaticamente).

### 🚀 Passo 4: Subir a aplicação!

```bash
# Construir imagens e subir todos os containers em segundo plano
docker compose up -d --build

# Verificar se tudo está rodando
docker compose ps

# Ver logs em tempo real (Ctrl+C para sair)
docker compose logs -f

# Ver logs só do backend (útil para debugar erros de conexão)
docker compose logs -f api
```

**Resultado esperado de `docker compose ps`:**

```
NAME              SERVICE     STATUS    PORTS
meu-projeto-db-1       db          running   5432/tcp
meu-projeto-api-1      api         running   0.0.0.0:4000->4000/tcp
meu-projeto-frontend-1 frontend    running   0.0.0.0:80->3000/tcp
```

### 🌐 Passo 5: Testar o acesso via IP

Abra o navegador **no seu PC** e acesse:

```
http://SEU_IP_PUBLICO
```

Se a tela do seu frontend apareceu — **sua aplicação está online!** 🎉

> [!NOTE] 💼 Pergunta de Entrevista
> **"Explique a diferença entre `docker compose up` e `docker compose up -d`."**
>
> **Resposta ideal (nível Júnior+):** O flag `-d` significa *detached mode* — os containers rodam em segundo plano, liberando o terminal. Sem `-d`, os logs ficam presos no terminal e se você fechar a sessão SSH, os containers morrem junto. Em produção, **sempre** usamos `-d`. Para ver os logs depois, usamos `docker compose logs -f`.

---

## 📌 3. DNS Gratuito + HTTPS Automático [Hands-On ⏳ 25 min]

Acessar por IP não é profissional. Vamos resolver isso com um domínio gratuito e HTTPS.

### 🦆 Passo 1: Registrar um subdomínio gratuito no DuckDNS

O DuckDNS é um serviço gratuito de DNS dinâmico que dá subdomínios `.duckdns.org` para qualquer pessoa:

1. Acesse **[duckdns.org](https://www.duckdns.org)**
2. Faça login com sua conta **GitHub**
3. No campo "sub domain", digite o nome do seu grupo (ex: `pi2-alpha`)
4. Clique em **"add domain"**
5. No campo "current ip", coloque o **IP Público da sua EC2** (ex: `54.89.123.45`)
6. Clique em **"update ip"**

**Pronto!** Agora `pi2-alpha.duckdns.org` aponta para sua VM.

Teste no navegador:
```
http://pi2-alpha.duckdns.org
```

### 🔒 Passo 2: Adicionar HTTPS com Caddy (SSL Automático)

O Caddy é um servidor web moderno e leve, muito utilizado em práticas de SRE, que obtém certificados SSL do Let's Encrypt **automaticamente**, sem nenhuma configuração manual.

**Criar o arquivo `Caddyfile`** na raiz do projeto:

```caddy
# Caddyfile — HTTPS automático + elimina CORS!
pi2-alpha.duckdns.org {
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
- **Adeus CORS:** Como tanto o frontend quanto a API são acessados sob o mesmo domínio (`pi2-alpha.duckdns.org`), o navegador não bloqueia as chamadas e o CORS deixa de ser um problema!
- **Variáveis de Ambiente Limpas:** O seu frontend pode usar caminhos relativos para a API: `REACT_APP_API_URL=/api`.

### 🐳 Passo 3: Atualizar o docker-compose.yml (Fase 2 — Produção)

Agora adicionamos o Caddy e **fechamos as portas expostas** do frontend e da API:

```yaml
# docker-compose.yml — Fase 2 (Produção com HTTPS)
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

  api:
    build: ./backend
    restart: unless-stopped
    # 🔒 SEM "ports:" — a API só é acessível via Caddy internamente
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
    # 🔒 SEM "ports:" — o frontend só é acessível via Caddy internamente
    depends_on:
      - api
    networks:
      - interno

  caddy:
    image: caddy:2-alpine
    restart: unless-stopped
    ports:
      - "80:80"       # HTTP (redireciona automaticamente para HTTPS)
      - "443:443"     # HTTPS
      - "443:443/udp" # HTTP/3
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    depends_on:
      - frontend
    networks:
      - interno

volumes:
  pgdata:
  caddy_data:
  caddy_config:

networks:
  interno:
    driver: bridge
```

### 🔄 Passo 4: Atualizar o `.env` e resubir

```bash
# ⚠️ IMPORTANTE: Parar os containers da Fase 1 ANTES de subir a Fase 2
# (o Caddy precisa da porta 80 que o frontend estava usando)
docker compose down

# Editar o .env — mudar a URL da API para caminho relativo
nano .env
# Alterar: REACT_APP_API_URL=/api

# Recriar os containers com Caddy (agora com o novo compose)
docker compose up -d --build

# Verificar os logs do Caddy (aqui você vê o Let's Encrypt emitindo o certificado!)
docker compose logs -f caddy
```

### 🎉 Resultado Final

Acesse no navegador:
```
https://pi2-alpha.duckdns.org
```

🔒 **Cadeado verde!** Sua aplicação está rodando com HTTPS, domínio próprio, e sem erros de CORS.

> [!TIP] 💡 Dica de Produção (Pro-Tip)
> O **Caddy** substitui ferramentas legadas como o NGINX + Certbot em startups e MVPs devido à facilidade de configuração (SSL nativo, sem cron jobs de renovação). Em grandes infraestruturas com centenas de subdomínios, soluções como Caddy ou gateways do Kubernetes gerenciam milhares de certificados por segundo com absoluta estabilidade.

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

O Fail2Ban monitora os logs do SSH e **bloqueia automaticamente** qualquer IP que errar a senha mais de 5 vezes em 10 minutos.

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
| AWS Academy Learner Lab | Ambiente de laboratório AWS com créditos gratuitos para estudantes |
| EC2 (Elastic Compute Cloud) | Máquina virtual na nuvem — seu servidor de produção |
| Security Group | Firewall de rede na AWS — controla quais portas estão abertas |
| `vockey` / `labsuser.pem` | Par de chaves SSH pré-configurado no Learner Lab |
| `ssh -i chave.pem ubuntu@IP` | Conectar remotamente em um servidor via protocolo SSH |
| `curl -fsSL https://get.docker.com \| sudo sh` | Instalar Docker via script oficial |
| `docker compose up -d --build` | Construir imagens e subir todos os containers em segundo plano |
| `docker compose ps` | Listar containers rodando e seus status |
| `docker compose logs -f` | Acompanhar logs em tempo real (debug) |
| `docker compose down` | Parar e remover todos os containers da stack |
| DuckDNS | Serviço gratuito de DNS dinâmico — subdomínios `.duckdns.org` |
| Caddy | Servidor web que obtém certificados SSL automaticamente |
| `Caddyfile` | Arquivo de configuração do Caddy (9 linhas para HTTPS + reverse proxy!) |
| `reverse_proxy` | Redirecionar tráfego externo para um container interno |
| UFW | Firewall simplificado do Ubuntu — controla portas abertas (dentro da VM) |
| Fail2Ban | Proteção automática contra tentativas de brute-force SSH |
| `.env` + `.gitignore` | Variáveis sensíveis fora do código-fonte |
| `USER appuser` | Diretiva no Dockerfile para rodar como não-root |
| Rede Docker interna | Containers se comunicam sem expor portas para a internet |

---
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
- **AWS**, *AWS Academy — Learner Lab*. Amazon Web Services, 2025. Disponível em: https://aws.amazon.com/training/awsacademy/
- **TANENBAUM, Andrew S.; WETHERALL, David J.**, *Redes de Computadores*. 5ª ed. Pearson, 2011. **Capítulo 8: Segurança de Redes, pp. 513–590** — Fundamentos de criptografia, SSL/TLS e autenticação por chaves.
- **STALLINGS, William**, *Criptografia e Segurança de Redes*. 6ª ed. Pearson, 2014. **Capítulo 17: Segurança em Nível de Transporte, pp. 497–522** — Protocolo TLS e certificados digitais.
- **KANE, Sean P.; MATTHIAS, Karl**, *Docker: Up & Running*. 3rd ed. O'Reilly Media, 2023. **Capítulo 11: Production Containers, pp. 245–278** — Boas práticas de segurança e deploy Docker em produção.

---
*Última atualização: 2026-05-21 | Status: publicado*
