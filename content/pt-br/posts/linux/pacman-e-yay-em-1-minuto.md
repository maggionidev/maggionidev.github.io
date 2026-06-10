---
title: Pacman e Yay em 1 minuto!
slug: 1-minute-pacman-yay
description: Aprenda os comandos básicos pacman + yay!
summary: Aprenda os comandos básicos pacman + yay!
tags:
  - pacman
  - yay
  - linux
  - arch
  - cachyos
categories:
  - linux
  - arch
  - 1minute
keywords: []
author: Gabriel Maggioni
date: 2026-05-09T22:47:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: true
draft: false
---

***

> Esse post é resumido. Tenho um bem completo [aqui](/pt-br/posts/linux/pacman-and-yay-cachyos-guide/)

🎭 **Pacman vs Yay — Qual a diferença?**

|  | Pacman | Yay |
| --- | --- | --- |
| O que é | gerenciador oficial | wrapper do AUR |
| Instala de | repositórios oficiais | oficial + AUR |
| Precisa sudo | sim | não (yay pede quando precisa) |
| Uso no dia a dia | sistema base | tudo que o pacman não tem |

> 💡 **AUR** = repositório mantido pela comunidade. Tem quase tudo que não está nos repos oficiais.

***

📦 **Instalar Pacotes**

| Comando | Função |
| --- | --- |
| `sudo pacman -S pacote` | instalar do repositório oficial |
| `yay -S pacote` | instalar do oficial ou AUR |
| `sudo pacman -S p1 p2 p3` | instalar vários de uma vez |
| `yay -S pacote --noconfirm` | instalar sem confirmar |

***

🗑️ **Remover Pacotes**

| Comando | Função |
| --- | --- |
| `sudo pacman -R pacote` | remover pacote |
| `sudo pacman -Rs pacote` | remover + dependências órfãs |
| `sudo pacman -Rns pacote` | remover + deps + arquivos de config |

> 💡 Prefira sempre `-Rs` pra não deixar lixo acumulando.

***

🔄 **Atualizar o Sistema**

| Comando | Função |
| --- | --- |
| `sudo pacman -Syu` | atualizar tudo (repos oficiais) |
| `yay -Syu` | atualizar tudo (oficial + AUR) |
| `yay` | atalho — já faz o `-Syu` |

> 💡 No CachyOS, o hábito é rodar `yay` todo dia antes de trabalhar.

***

🔍 **Buscar Pacotes**

| Comando | Função |
| --- | --- |
| `pacman -Ss palavra` | buscar nos repos oficiais |
| `yay -Ss palavra` | buscar no oficial + AUR |
| `pacman -Si pacote` | ver detalhes do pacote |
| `yay -Si pacote` | ver detalhes (AUR incluso) |

***

📋 **Listar Pacotes Instalados**

| Comando | Função |
| --- | --- |
| `pacman -Q` | listar todos instalados |
| `pacman -Qe` | listar só os que você instalou manualmente |
| `pacman -Qm` | listar pacotes do AUR |
| `pacman -Q pacote` | checar se um pacote está instalado |
| `pacman -Ql pacote` | ver arquivos de um pacote |

***

🧹 **Limpeza do Sistema**

| Comando | Função |
| --- | --- |
| `sudo pacman -Sc` | limpar cache de pacotes antigos |
| `sudo pacman -Scc` | limpar todo o cache |
| `sudo pacman -Rns $(pacman -Qdtq)` | remover órfãos |
| `yay -Sc` | limpar cache do yay/AUR |

***

🔧 **Situações do Dia a Dia**

| Situação | Comando |
| --- | --- |
| Não sei o nome exato | `yay -Ss parte-do-nome` |
| Pacote quebrado | `sudo pacman -S pacote --overwrite '*'` |
| Forçar refresh das listas | `sudo pacman -Syy` |
| Ver de qual repo veio | `pacman -Qi pacote` |
| Qual pacote tem esse arquivo | `pacman -F nome-do-arquivo` |

***

🧠 **Fluxo Básico do Dia a Dia**

```plain
yay                          # atualizar tudo
yay -Ss nome                 # buscar o que quero instalar
yay -S pacote                # instalar
sudo pacman -Rs pacote       # remover quando não precisar mais
sudo pacman -Rns $(pacman -Qdtq)  # limpar órfãos de vez em quando
```

***

🎯 **Os 10 Que Vale Decorar Primeiro**

1. `yay` — atualiza tudo
2. `yay -S pacote` — instala qualquer coisa
3. `yay -Ss palavra` — busca
4. `sudo pacman -Rs pacote` — remove limpo
5. `pacman -Q pacote` — checa se está instalado
6. `pacman -Qe` — o que você instalou manualmente
7. `pacman -Qm` — o que veio do AUR
8. `sudo pacman -Sc` — limpa cache
9. `sudo pacman -Rns $(pacman -Qdtq)` — mata órfãos
10. `pacman -Qi pacote` — info completa do pacote

Com isso você gerencia o sistema como um arch user de verdade 📦🔥
