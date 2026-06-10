---
title: Fish Shell em 1 minuto!
slug: 1-minute-fish-shell
description: Aprenda os comandos básicos do seu terminal fish em 1 minuto!
summary: Aprenda os comandos básicos do seu terminal fish em 1 minuto!
tags:
  - fish
  - shell
  - linux
  - cachyos
  - arch
categories:
  - linux
  - 1minute
keywords: []
author: Gabriel Maggioni
date: 2026-05-09T22:45:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: true
draft: false
---

⚡Fish Shell em 1 minuto! Cola prática pra se virar no terminal do CachyOS!

***

🐟 **O que é o Fish?** O CachyOS usa o **Fish** como shell padrão — ele já vem com autocomplete inteligente, histórico e cores sem precisar configurar nada.

***

📁 **Navegação Básica**

| Comando | Função |
| --- | --- |
| `cd pasta/` | entrar na pasta |
| `cd ..` | voltar uma pasta |
| `cd ~` | ir para o home |
| `ls` | listar arquivos |
| `ls -la` | listar com detalhes e ocultos |
| `pwd` | mostrar onde você está |

***

✨ **Superpoderes do Fish**

| Recurso | Como funciona |
| --- | --- |
| `Tab` | autocomplete de comandos e pastas |
| `↑ / ↓` | navegar no histórico |
| `→` | aceitar sugestão automática |
| `Alt + ←` | voltar palavra por palavra |
| `Ctrl + r` | buscar no histórico |
| `Ctrl + c` | cancelar comando atual |

***

📦 **Pacotes com Pacman e Yay (CachyOS)**

| Comando | Função |
| --- | --- |
| `sudo pacman -S pacote` | instalar pacote |
| `sudo pacman -R pacote` | remover pacote |
| `sudo pacman -Syu` | atualizar o sistema |
| `yay -S pacote` | instalar do AUR |
| `yay -Syu` | atualizar tudo (incluindo AUR) |
| `pacman -Ss palavra` | buscar pacote |
| `pacman -Q` | listar instalados |

***

📄 **Arquivos e Texto**

| Comando | Função |
| --- | --- |
| `cat arquivo.txt` | ver conteúdo |
| `nano arquivo.txt` | editar no nano |
| `cp origem destino` | copiar arquivo |
| `mv origem destino` | mover / renomear |
| `rm arquivo` | apagar arquivo |
| `rm -rf pasta/` | apagar pasta inteira |
| `mkdir pasta` | criar pasta |
| `touch arquivo.txt` | criar arquivo vazio |

***

🔍 **Busca**

| Comando | Função |
| --- | --- |
| `grep "texto" arquivo` | buscar texto em arquivo |
| `find . -name "*.txt"` | buscar arquivos por nome |
| `which programa` | saber onde o programa está |

***

⚙️ **Sistema**

| Comando | Função |
| --- | --- |
| `htop` | monitor de processos |
| `df -h` | espaço em disco |
| `free -h` | uso de memória |
| `uname -r` | versão do kernel |
| `neofetch` | info do sistema |
| `reboot` | reiniciar |
| `poweroff` | desligar |

***

🔧 **Configurar o Fish**

| Comando | Função |
| --- | --- |
| `fish_config` | abre config no navegador |
| `nano ~/.config/fish/config.fish` | editar config manualmente |
| `alias ll='ls -la'` | criar atalho temporário |
| `funcsave ll` | salvar o alias pra sempre |

***

🧠 **Fluxo Básico do Dia a Dia**

```plain
sudo pacman -Syu        # atualizar o sistema
cd ~/projetos/          # entrar na pasta
ls                      # ver o que tem
nano arquivo.txt        # editar algo
Ctrl + x → Y → Enter   # salvar no nano
```

***

🎯 **Os 10 Que Vale Decorar Primeiro**

1. `cd` / `ls`
2. `Tab` (autocomplete!)
3. `↑` (histórico)
4. `sudo pacman -Syu`
5. `yay -S pacote`
6. `cp` / `mv` / `rm`
7. `cat arquivo`
8. `grep`
9. `htop`
10. `fish_config`

Com isso sozinho você já sobrevive tranquilamente no terminal do CachyOS 🐟🔥
