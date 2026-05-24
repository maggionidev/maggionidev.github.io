---
title: Tudo oque você precisa saber sobre Git e Github
slug: ''
description: Aprenda os comandos necessários de git e github
summary: Aprenda os comandos necessários de git e github
tags:
  - git
  - github
  - dev
categories:
  - dev
keywords:
  - git
  - github
  - controle de versão
  - ssh
  - programação
author: Gabriel Maggioni
date: 2026-04-15T20:23:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

# Git e Github — Guia Prático para Iniciantes

Nesse post, iremos abordar os seguintes temas:

- Como conectar via SSH no Github
- Como enviar um repositório local para o remoto
- Comandos úteis
- Workflow com Git e Github

***

## O que é Git e por que usar?

**Git** é um sistema de controle de versão distribuído criado por Linus Torvalds em 2005. Ele permite que você rastreie as alterações no seu código ao longo do tempo, volte para versões anteriores e colabore com outras pessoas sem sobrescrever o trabalho umas das outras.

**Github** é uma plataforma online que hospeda repositórios Git, facilitando a colaboração, o backup e o compartilhamento de projetos.

> 💡 Pense no Git como uma "máquina do tempo" para o seu código, e no Github como a "nuvem" onde você guarda essas versões.

***

## Instalação

### Linux (Debian/Ubuntu)

```bash
sudo apt update && sudo apt install git -y
```

### macOS

```bash
brew install git
```

### Windows

Baixe o instalador em [git-scm.com](https://git-scm.com/downloads).

Após instalar, **pode fazer toda a configuração pelo terminal do git bash**, comece configurando seu nome e e-mail:

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
```

***

## Como conectar via SSH no Github

A autenticação via SSH é mais segura e prática do que usar usuário e senha. Com ela, você não precisa digitar credenciais a cada `push`.

### 1. Gerar a chave SSH

```bash
ssh-keygen -t ed25519 -C "seu@email.com"
```

Pressione `Enter` para aceitar o caminho padrão (`~/.ssh/id_ed25519`). Opcionalmente, defina uma senha para a chave.

### 2. Adicionar a chave ao ssh-agent

```bash
eval "$(ssh-agent -s)" # ssh-agent -c | source
ssh-add ~/.ssh/id_ed25519
```

### 3. Copiar a chave pública

```bash
cat ~/.ssh/id_ed25519.pub
```

Copie o conteúdo exibido no terminal.

### 4. Adicionar a chave no Github

1. Acesse **Github → Settings → SSH and GPG keys**
2. Clique em **New SSH key**
3. Cole a chave pública e salve

### 5. Testar a conexão

```bash
ssh -T git@github.com
```

Se tudo estiver correto, você verá:

```plain
Hi seu-usuario! You've successfully authenticated, but GitHub does not provide shell access.
```

***

## Como enviar um repositório local para o remoto

### Partindo do zero (novo projeto)

```bash
# 1. Crie a pasta e entre nela
mkdir meu-projeto && cd meu-projeto

# 2. Inicie o repositório Git
git init

# 3. Crie um arquivo inicial
echo "# Meu Projeto" > README.md

# 4. Adicione os arquivos ao stage
git add .

# 5. Faça o primeiro commit
git commit -m "feat: primeiro commit"

# 6. Renomeie a branch principal (boa prática)
git branch -M main

# 7. Conecte ao repositório remoto (crie o repo no Github antes)
git remote add origin git@github.com:seu-usuario/meu-projeto.git
# se já tem uma origin não ssh, pode substituir com:
# git remote set-url origin git@github.com:seu-usuario/meu-projeto.git

# 8. Envie para o Github
git push -u origin main
```

> ⚠️ Lembre-se de criar o repositório vazio no Github antes do passo 7. Não marque a opção de adicionar README para evitar conflitos.

### Clonando um repositório existente

```bash
git clone git@github.com:usuario/repositorio.git
cd repositorio
```

***

## Comandos Úteis

### Informação e status

| Comando | O que faz |
| --- | --- |
| `git status` | Mostra o estado atual dos arquivos |
| `git log` | Exibe o histórico de commits |
| `git log --oneline` | Histórico resumido em uma linha por commit |
| `git diff` | Mostra as diferenças ainda não staged |
| `git diff --staged` | Mostra as diferenças já no stage |

### Stage e commits

```bash
git add arquivo.txt        # Adiciona um arquivo específico
git add .                  # Adiciona todos os arquivos modificados
git commit -m "mensagem"   # Cria um commit
git commit --amend         # Edita o último commit (mensagem ou arquivos)
```

### Branches

```bash
git branch                      # Lista as branches locais
git branch minha-feature        # Cria uma nova branch
git checkout minha-feature      # Muda para a branch
git checkout -b minha-feature   # Cria e já muda para a branch
git switch -c minha-feature     # Alternativa moderna ao checkout -b
git merge minha-feature         # Faz merge da branch na atual
git branch -d minha-feature     # Deleta a branch local
```

### Remoto

```bash
git push origin main            # Envia commits para o remoto
git pull                        # Baixa e integra mudanças do remoto
git fetch                       # Baixa mudanças sem integrar
git remote -v                   # Lista os remotos configurados
```

### Desfazendo mudanças

```bash
git restore arquivo.txt         # Descarta mudanças não staged
git restore --staged arquivo.txt # Remove do stage sem perder mudanças
git revert HEAD                 # Cria um commit que desfaz o último
git reset --soft HEAD~1         # Desfaz o último commit, mantém stage
git reset --hard HEAD~1         # ⚠️ Desfaz tudo — use com cuidado!
```

### Stash (guardar mudanças temporariamente)

```bash
git stash           # Salva mudanças temporariamente
git stash pop       # Recupera a última mudança salva
git stash list      # Lista todos os stashes
git stash drop      # Remove o stash mais recente
```

***

## Workflow com Git e Github

Um bom workflow evita conflitos e mantém o histórico organizado. O mais comum para times é o **Feature Branch Workflow**:

### Passo a passo

```plain
main ──────────────────────────────────●─────────
                                      /
feature/login ──●──●──●──────────────●
```

**1. Sempre parta da main atualizada**

```bash
git checkout main
git pull
```

**2. Crie uma branch para sua feature ou correção**

```bash
git checkout -b feature/nome-da-feature
```

**3. Desenvolva e faça commits pequenos e descritivos**

```bash
git add .
git commit -m "feat: adiciona tela de login"
```

**4. Envie sua branch para o remoto**

```bash
git push origin feature/nome-da-feature
```

**5. Abra um Pull Request no Github**

No Github, clique em **"Compare & pull request"**, descreva as mudanças e solicite revisão.

**6. Após aprovação, faça o merge e delete a branch**

```bash
git checkout main
git merge feature/nome-da-feature
git branch -d feature/nome-da-feature
git push origin --delete feature/nome-da-feature
```

***

### Boas práticas para mensagens de commit

Use o padrão **Conventional Commits**:

```plain
feat: adiciona funcionalidade de login
fix: corrige bug no formulário de cadastro
docs: atualiza README com instruções de instalação
style: formata código sem alterar lógica
refactor: reorganiza estrutura de pastas
test: adiciona testes para o módulo de auth
chore: atualiza dependências
```

***

## Resumo rápido

```bash
# Fluxo básico do dia a dia
git pull                          # Atualiza o repositório local
git checkout -b minha-feature     # Cria e entra na branch
# ... faz as alterações ...
git add .                         # Stage das mudanças
git commit -m "feat: minha feature"  # Commit
git push origin minha-feature     # Envia para o Github
# Abre Pull Request no Github
```

***

Com esses conceitos e comandos você já consegue trabalhar de forma profissional com Git e Github no dia a dia. Bons commits! 🚀
