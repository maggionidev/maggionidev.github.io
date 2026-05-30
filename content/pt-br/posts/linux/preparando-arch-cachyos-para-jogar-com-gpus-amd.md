---
title: Preparando Arch/CachyOS para JOGAR com GPUs AMD
slug: preparing-cachyos-for-gaming
description: Aprenda a como configurar rapidamente o seu CachyOS para jogar tudo!
summary: Aprenda a como configurar rapidamente o seu CachyOS para jogar tudo!
tags:
  - gaming
  - cachyos
  - linux
categories:
  - linux
  - arch
  - gaming
keywords: []
author: Gabriel Maggioni
date: 2026-05-17T10:00:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

## Introdução

Esse post é direcionado à usuários AMD com Arch ou CachyOS. As configurações a seguir vão ajudar seus jogos a extraírem o máximo de desempenho  de suas peças.

***

## 1. Atualiza Tudo Primeiro (Sério, Não Pula Essa Etapa)

Antes de qualquer coisa, garante que o sistema está completamente atualizado:

```bash
sudo pacman -Syu
```

Depois, instala os pacotes essenciais pra AMD:

```bash
# Drivers e ferramentas da GPU
sudo pacman -S mesa lib32-mesa libva-mesa-driver lib32-libva-mesa-driver mesa-utils rocm-smi-lib

# Suporte Vulkan (essencial pra jogos modernos e Proton)
sudo pacman -S vulkan-radeon lib32-vulkan-radeon mesa lib32-mesa

# GameMode (otimizações automáticas durante o jogo)
sudo pacman -S gamemode lib32-gamemode

# MangoHud (overlay de estatísticas em tempo real)
sudo pacman -S mangohud lib32-mangohud
```

O `lib32-*` é necessário pra jogos 32-bit e alguns títulos via Proton. Não economiza aqui.

Quer conferir qual versão do Mesa está rodando?

```bash
pacman -Q mesa
```

***

## 2. Desbloqueando a GPU: O Parâmetro do GRUB

Aqui começa a parte boa. Por padrão, o kernel Linux restringe algumas funcionalidades do driver `amdgpu` - incluindo o controle total de clocks e voltagens. O `amdgpu.ppfeaturemask=0xffffffff` desbloqueia **todas** essas funcionalidades.

Sem isso, o CoreCtrl (que vamos instalar logo mais) não consegue controlar os clocks da GPU corretamente em GPUs RX modernas.

### Editando o GRUB

Abre o arquivo de configuração:

```bash
sudo nano /etc/default/grub
```

Procura a linha `GRUB_CMDLINE_LINUX_DEFAULT` e adiciona o parâmetro **dentro das aspas**, junto com o que já existir:

pra mim ficou assim, só adicionei `amdgpu.ppfeaturemask=0xffffffff` no final:

```plain
GRUB_CMDLINE_LINUX_DEFAULT='nowatchdog nvme_load=YES splash loglevel=3 amdgpu.ppfeaturemask=0xffffffff'
```

> ⚠️ Não apaga os outros parâmetros que já estavam lá! Só **adiciona** o `amdgpu.ppfeaturemask=0xffffffff` no final.

Salva o arquivo (`Ctrl+O`, depois `Enter`, depois `Ctrl+X`) e aplica a mudança:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**Reinicia o PC.** Sem reiniciar, não adianta.

***

## 3. CoreCtrl: O Painel de Controle da Sua GPU

O CoreCtrl é uma ferramenta gráfica que permite controlar clocks, voltagens e perfis de desempenho da GPU de forma visual - sem precisar mexer em arquivos do sistema manualmente.

### Instalação

```bash
sudo pacman -Ss corectrl
```

> Se não encontrar, verifique se os repositórios estão atualizados (`sudo pacman -Sy`) ou procure nos repos do AUR com `yay -S corectrl`.

### Configurando o Perfil de Gaming

Depois de abrir o CoreCtrl:

1. **Cria um novo perfil** chamado `Gaming`
2. Na aba **GPU**, muda o modo para **Avançado** - isso libera os controles de clock e voltagem

> 🚨 **Aviso sério:** Modo Avançado te dá acesso a configurações que podem danificar a GPU se você sair mexendo em tudo sem saber o que está fazendo. Foca apenas no que o guia indica.

### Ajustando a GPU ( RX 6600 )

A minha GPU é a RX6600, então eu vou fazer as seguintes alterações nela:

**Clock mínimo da GPU → 1900 MHz**
**Clock máximo da GPU → 2500 MHz**

Por padrão, a GPU "dorme" em clocks baixíssimos (tipo 500 MHz) quando percebe que o jogo não está exigindo 100% do tempo - o que acontece constantemente em jogos com hitches, carregamentos ou momentos menos intensos. O problema é que a transição de 500 MHz → 2750 MHz leva tempo, causando **stutters** perceptíveis.

Fixar o mínimo em 1900 MHz garante que a GPU nunca cai pra clocks baixos demais, eliminando esses solavancos sem aumentar significativamente o consumo médio.

3. Na aba **CPU**, verifica se o **governador de frequência** está em **Performance**
4. **Ativa o perfil Gaming**

### Verificando se Funcionou

Depois de aplicar, testa com:

```bash
watch -n 0.5 cat /sys/class/drm/card0/device/pp_dpm_sclk
```

_(Se não funcionar, tenta com `card1`)_

A saída vai mostrar os estados de clock disponíveis. O `*` indica o estado atual. Se o asterisco aparecer em `2100` ou `2750` - e **não** ficar preso em `500` - então o clock mínimo está funcionando corretamente. 

***

## 4. MangoHud: Seu Overlay de Estatísticas

O MangoHud é um overlay que aparece no canto da tela durante o jogo mostrando FPS, temperatura, uso de GPU/CPU, VRAM e muito mais - similar ao MSI Afterburner do Windows, mas nativo e sem frescura.

### Configuração

Edita o arquivo de config:

```bash
nano ~/.config/MangoHud/MangoHud.conf
```

Apaga o que tiver e cola isso:

```ini
# === Métricas de GPU ===
fps
fps_metrics=avg
gpu_stats
gpu_usage
vram
gpu_core_clock
gpu_power
gpu_temp

# === Métricas de CPU ===
cpu_stats
cpu_temp
cpu_mhz
ram

# === Posição e Visual ===
position=top-left
offset_x=15
offset_y=15
font_size=20
background_alpha=0.35
alpha=0.95
round_corners=10
hud_compact
table_columns=3
legacy_layout=false

# === Desabilitado (menos ruído) ===
frametime=0
media_player=0
time=0
battery=0
device_battery=0
```

### Testando

Antes de abrir qualquer jogo, testa se o overlay aparece:

```bash
# Teste com OpenGL
mangohud glxgears

# Teste com Vulkan
mangohud vkcube
```

Se aparecer o overlay no canto superior esquerdo com as estatísticas, está funcionando.

***

## 5. Opções de Inicialização na Steam

Com tudo configurado, vai nas **propriedades** do jogo na Steam → **Opções de Inicialização** e coloca:

```plain
gamemoderun mangohud %command%
# gamemoderun RADV_PERFTEST=sam RADV_DEBUG=syncshaders mangohud %command%
```

### O Que Cada Coisa Faz

- **`gamemoderun`** - Ativa o GameMode enquanto o jogo está rodando. O GameMode é um daemon do Feral Interactive que faz uma série de otimizações automáticas no sistema: muda o governador de CPU pra performance, reduz processos em background, aplica otimizações de scheduler, e pode interagir com jogos compatíveis via API. Basicamente, diz pro Linux: "estou jogando, prioriza isso".
- **`mangohud`** - Injeta o overlay do MangoHud no jogo para mostrar as estatísticas configuradas.
- **`%command%`** - É o placeholder do Steam pro executável do jogo. Tudo que vem antes dele são variáveis de ambiente e wrappers que serão aplicados.

***

## Resumo Rápido

| O Que | Por Que |
| --- | --- |
| `mesa` atualizado | Melhorias de desempenho e correção de bugs no driver |
| `amdgpu.ppfeaturemask=0xffffffff` | Desbloqueia controle total de clocks pra o CoreCtrl |
| CoreCtrl clock min 2100 MHz | Elimina stutters por transição de clock |
| CoreCtrl -25 mV offset | Menos calor e consumo sem perder desempenho |
| MangoHud | Visibilidade total do que está acontecendo na GPU |
| GameMode | Otimizações automáticas do sistema durante o jogo |

***

## Dúvidas? Problemas?

Se o CoreCtrl não estiver aplicando os clocks, confirma que:

1. O `amdgpu.ppfeaturemask=0xffffffff` foi adicionado **e** o `grub-mkconfig` foi rodado
2. O PC foi reiniciado depois
3. O perfil Gaming está **ativo** no CoreCtrl

***

Bons jogos! 🎮

***
