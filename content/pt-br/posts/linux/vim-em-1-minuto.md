---
title: NeoVim em 1 minuto!
slug: 1-minute-nvim
description: Resumo dos principais comandos e casos de uso do nvim!
summary: Resumo dos principais comandos e casos de uso do nvim!
tags:
  - vim
  - nvim
  - neovim
  - linux
  - cachyos
  - linux
categories:
  - linux
  - 1minute
author: Gabriel Maggioni
date: 2026-05-09T22:40:00
lastmod: 2026-05-10T18:06:00
showToc: true
TocOpen: false
hiddenInHomeList: true
draft: false
---

# 🚀 Resumão NeoVim

| 📦 Instalação | Comando |
| --- | --- |
| Arch / CachyOS | `sudo pacman -S neovim` |
| Fedora | `sudo dnf install neovim` |
| Ubuntu / Debian | `sudo apt install neovim` |
| Mac (brew) | `brew install neovim` |

| 💾 Salvar e sair | Comando |
| --- | --- |
| salvar | `:w` |
| sair | `:q` |
| salvar e sair | `:wq` |
| sair sem salvar | `:q!` |
| salvar tudo e sair | `:wqa` |

| ✍️ Inserção | Comando |
| --- | --- |
| antes do cursor | `i` |
| depois do cursor | `a` |
| fim da linha | `A` |
| início da linha | `I` |
| nova linha abaixo | `o` |
| nova linha acima | `O` |

| 🧭 Movimento | Comando |
| --- | --- |
| esquerda | `h` |
| baixo | `j` |
| cima | `k` |
| direita | `l` |
| início da linha | `0` |
| fim da linha | `$` |
| próxima palavra | `w` |
| palavra anterior | `b` |
| início do arquivo | `gg` |
| fim do arquivo | `G` |
| ir para linha | `:10` |
| descer página | `Ctrl+d` |
| subir página | `Ctrl+u` |

| ✂️ Edição | Comando |
| --- | --- |
| apagar linha | `dd` |
| copiar linha | `yy` |
| colar abaixo | `p` |
| colar acima | `P` |
| desfazer | `u` |
| refazer | `Ctrl+r` |
| apagar caractere | `x` |
| apagar até fim da linha | `D` |
| editar palavra | `ciw` |
| editar linha | `cc` |

| 📋 Copiar e mover | Comando |
| --- | --- |
| copiar palavra | `yw` |
| copiar até fim da linha | `y$` |
| cortar linha | `dd` |
| colar | `p` |
| colar antes | `P` |

| 🧠 Seleção | Comando |
| --- | --- |
| seleção livre | `v` |
| linha inteira | `V` |
| bloco visual | `Ctrl+v` |
| sair seleção | `Esc` |
| selecionar tudo | `ggVG` |
| copiar tudo | `ggVG"+y` |
| cortar tudo | `ggVG"+d` |

| 🔍 Busca | Comando |
| --- | --- |
| buscar texto | `/texto` |
| próximo resultado | `n` |
| anterior | `N` |
| palavra atual | `*` |
| substituir tudo | `:%s/velho/novo/g` |
| substituir com confirmação | `:%s/velho/novo/gc` |

| 📁 Arquivos | Comando |
| --- | --- |
| abrir arquivo | `:e nome` |
| abrir diretório | `:Ex` |
| salvar como | `:w nome` |

| 🪟 Janelas e buffers | Comando |
| --- | --- |
| split horizontal | `:split` |
| split vertical | `:vsplit` |
| trocar janela | `Ctrl+w w` |
| mover entre janelas | `Ctrl+w h/j/k/l` |
| próximo buffer | `:bn` |
| buffer anterior | `:bp` |
| fechar buffer | `:bd` |

***

## ⚙️ Configuração básica

Arquivo principal:

```bash
~/.config/nvim/init.lua
```

Criar:

```bash
mkdir -p ~/.config/nvim
nvim ~/.config/nvim/init.lua
```

Config mínima:

```lua
vim.opt.number = true
vim.opt.relativenumber = true

vim.opt.tabstop = 2
vim.opt.shiftwidth = 2
vim.opt.expandtab = true

vim.opt.ignorecase = true
vim.opt.smartcase = true

vim.opt.termguicolors = true
vim.opt.wrap = false
```

***
