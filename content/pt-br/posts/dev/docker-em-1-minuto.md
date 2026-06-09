---
title: Docker em 1 minuto!
slug: 1-minute-docker
description: 'Comandos básicos : Docker (de forma resumida)'
summary: 'Comandos básicos : Docker (de forma resumida)'
tags:
  - docker
  - lazydocker
  - linux
  - cachyos
categories:
  - dev
  - 1minute
keywords: []
author: Gabriel Maggioni
date: 2026-05-10T16:45:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

Guia direto ao ponto pra usar Docker no dia a dia sem ficar perdido.

> obs: Esse post é um RESUMO desse [aqui](/pt-br/posts/dev/all-about-docker-in-linux/)

***

## 🧠 Docker na prática

Docker roda sistemas isolados chamados containers.

Você usa pra:

* rodar coisas sem instalar nada
* subir APIs rápido
* replicar ambientes iguais em qualquer máquina

***

# 📦 INSTALAÇÃO

| Sistema | Comando |
| --- | --- |
| Arch / CachyOS | `sudo pacman -S docker` |
| Fedora | `sudo dnf install docker` |
| Ubuntu / Debian | `sudo apt install docker.io` |
| Mac / Windows | Docker Desktop |

> Importante: Talvez seja necessário reiniciar o pc;

***

## 🔎 Verificação

```bash
docker --version
docker info
```

***

## ▶ INICIAR SERVIÇO

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

***

# 🚀 CONTAINERS (BÁSICO)

## ▶ Rodar container

```bash
docker run nginx
```

```bash
docker run -d nginx
```

```bash
docker run -d -p 8080:80 nginx
```

***

## 📋 Listar containers

```bash
docker ps
docker ps -a
```

***

## ⛔ Parar containers

```bash
docker stop <id>
docker kill <id>
```

***

## 🧹 Remover containers

```bash
docker rm <id>
docker rm -f <id>
```

***

# 🖼 IMAGENS

## 📥 Baixar imagem

```bash
docker pull nginx
```

***

## 📋 Listar imagens

```bash
docker images
```

***

## 🗑 Remover imagem

```bash
docker rmi nginx
```

***

## 🧼 Limpeza geral

```bash
docker system prune
docker system prune -a
```

***

# 🧩 DOCKER COMPOSE (IMPORTANTE)

Docker Compose

## 📄 Exemplo real (Postgres + Adminer)

```yaml
version: "3.9"

services:
 db:
   image: postgres:16
   container_name: postgres_db
   environment:
     POSTGRES_USER: user
     POSTGRES_PASSWORD: pass
     POSTGRES_DB: app
   ports:

- "5432:5432"

 adminer:
   image: adminer
   ports:

- "8081:8080"
```

***

## ▶ Rodar stack

```bash
docker compose up
docker compose up -d
```

***

## ⛔ Parar stack

```bash
docker compose down
```

***

## 🔍 Logs

```bash
docker compose logs
docker compose logs -f
```

***

## 🔄 rebuild

```bash
docker compose up --build
```

***

# 🧱 DOCKERFILE (CRIAR SUA IMAGEM)

## 📄 Exemplo Node.js

```dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

CMD ["node", "index.js"]
```

***

## 🏗 Build

```bash
docker build -t minha-app .
```

***

## ▶ Rodar

```bash
docker run -p 3000:3000 minha-app
```

***

# 🧭 FLUXO REAL DE TRABALHO

```bash
docker compose up -d
docker ps
docker logs -f <container>
docker compose down
```

***

# 🔥 COMANDOS ESSENCIAIS (RESUMO ABSOLUTO)

## Containers

```bash
docker ps
docker run nginx
docker stop <id>
docker rm <id>
```

## Imagens

```bash
docker images
docker pull nginx
docker rmi nginx
```

## Compose

```bash
docker compose up -d
docker compose down
docker compose logs -f
```

***

# 🖥 BONUS: LAZYDOCKER

LazyDocker

```bash
lazydocker
```

Te dá:

* visão de containers
* logs em tempo real
* controle sem decorar ID

***

# ⚡ RESUMO REAL

\*`docker run` → roda container rápido
\*`docker ps` → vê o que está vivo
\*`docker compose up -d` → sobe sistema inteiro
\*`docker build` → cria sua imagem
\*`lazydocker` → não se perde no caos

***
