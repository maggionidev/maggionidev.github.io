---
title: 'Guia completo: CachyOS + Jogos + AMD'
slug: guia-amd-cachyos-gaming
description: Aprenda a otimizar sua GPU AMD no Arch Linux e CachyOS usando CoreCtrl, MangoHud, GameMode e ajustes no amdgpu. Mais FPS, menos stutter e melhor desempenho nos jogos.
summary: Aprenda a otimizar sua GPU AMD no Arch Linux e CachyOS usando CoreCtrl, MangoHud, GameMode e ajustes no amdgpu. Mais FPS, menos stutter e melhor desempenho nos jogos.
tags:
  - cachyos
  - games
  - amd
  - mangohud
  - corectrl
categories:
  - games
  - cachyos
keywords: []
author: Gabriel Maggioni
date: 2026-06-11T14:20:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

# Otimização de Gaming AMD no CachyOS

> **Hardware de referência:** Ryzen 7 5600x + RX 6600 8GB

***

## 1. Atualizando o Sistema

Garante que o sistema está completamente atualizado antes de qualquer coisa:

```plain
sudo pacman -Syu
# sudo reboot obrigatório se atualizou!

```

Instala os pacotes essenciais pra AMD:

```plain
# Pacote meta-gaming: identifica seu hardware e instala o necessário
sudo pacman -S cachyos-gaming-meta

# Drivers e ferramentas da GPU
sudo pacman -S --needed mesa lib32-mesa libva-mesa-driver lib32-libva-mesa-driver mesa-utils

# Suporte Vulkan (essencial pra jogos modernos e Proton)
sudo pacman -S --needed vulkan-radeon lib32-vulkan-radeon

# MangoHud
sudo pacman -S mangohud lib32-mangohud

```

> `lib32-*` é necessário pra jogos 32-bit e títulos via Proton. Não economize aqui.

Para verificar a versão do Mesa instalada:

```plain
pacman -Q mesa

```

***

## 2. Desbloqueando a GPU (`amdgpu.ppfeaturemask`)

Por padrão, o kernel restringe o controle de clocks e voltagens do driver `amdgpu`. O parâmetro `amdgpu.ppfeaturemask=0xffffffff` desbloqueia todas essas funcionalidades — sem ele, o CoreCtrl não consegue controlar os clocks corretamente em GPUs RX modernas.

### Editando o GRUB

```plain
sudo nano /etc/default/grub

```

Localize a linha `GRUB_CMDLINE_LINUX_DEFAULT` e adicione `amdgpu.ppfeaturemask=0xffffffff` ao final, **sem remover os outros parâmetros**:

```plain
GRUB_CMDLINE_LINUX_DEFAULT='nowatchdog nvme_load=YES splash loglevel=3 amdgpu.ppfeaturemask=0xffffffff'

```

Salva (`Ctrl+O` → `Enter` → `Ctrl+X`) e aplica:

```plain
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot

```

***

## 3. CoreCtrl: Controle de Clocks da GPU

O CoreCtrl é uma ferramenta gráfica para controlar clocks, voltagens e perfis de desempenho da GPU.

### Instalação

```plain
sudo pacman -Ss corectrl
# Se não encontrar:
yay -S corectrl

```

### Configurando o Perfil de Gaming

1. Crie um perfil chamado `Gaming`
2. Na aba **GPU**, mude o modo para **Avançado**

> 🚨 O modo Avançado dá acesso a configurações que podem danificar a GPU. Mexa apenas no que este guia indica.

### Ajustes para RX 6600

| Configuração | Valor |
| --- | --- |
| Clock mínimo da GPU | 1900 MHz |
| Clock máximo da GPU | 2500 MHz |

**Por que fixar o clock mínimo?** Por padrão, a GPU cai para \~500 MHz em momentos de baixa carga (hitches, carregamentos). A transição de 500 → 2750 MHz leva tempo e causa stutters visíveis. Fixar em 1900 MHz elimina isso sem aumentar significativamente o consumo.

Na aba **CPU**, confirme que o governador está em **Performance**. Depois, ative o perfil Gaming.

### Verificando se Funcionou

```plain
watch -n 0.5 cat /sys/class/drm/card0/device/pp_dpm_sclk
# Se não funcionar, tenta com card1

```

O `*` indica o estado de clock atual. Se aparecer em `2100` ou `2750` (e não ficar preso em `500`), está funcionando.

***

## 4. Cache de Shaders

Aumentar o tamanho máximo do cache de shaders evita stuttering e longos tempos de carregamento — jogos maiores frequentemente ultrapassam 1 GB de shaders.

```plain
mkdir -p ~/.config/environment.d
nano ~/.config/environment.d/shader-cache.conf

```

Adicione a linha correspondente à sua GPU:

```plain
# AMD / Intel
MESA_SHADER_CACHE_MAX_SIZE=20G

# NVIDIA
__GL_SHADER_DISK_CACHE_SIZE=20480

```

```plain
sudo reboot

```

***

## 5. Proton: Qual Versão Usar?

| Versão | Use quando... |
| --- | --- |
| **Proton Experimental** | Jogo recém-lançado, correções mais recentes, não funciona em versões antigas |
| **Proton Stable** (9, 10…) | Jogo já funciona, você quer estabilidade — padrão pra maioria |
| **Proton Hotfix** | Um jogo específico quebrou após atualização (ferramenta de emergência) |
| **Proton GE** | Jogo não abre no oficial, vídeos problemáticos, codecs extras |
| **Proton CachyOS** | Quer espremer alguns FPS extras com otimizações da distro |

### Instalando o Proton GE via ProtonUp-Qt

```plain
# CachyOS
sudo pacman -S protonup-qt
# ou via AUR
yay -S protonup-qt

```

1. Abra o **ProtonUp-Qt**
2. Clique em **Add Version**
3. Selecione `GE-Proton` → versão mais recente → **Install**
4. Reinicie a Steam:steam -shutdown

5. No jogo: **Propriedades → Compatibilidade → Force the use of a specific Steam Play compatibility tool → GE-Proton**

***

## 6. MangoHud: Overlay de Estatísticas

Similar ao MSI Afterburner, mas nativo. Mostra FPS, temperaturas, uso de GPU/CPU, VRAM e mais.

### Configuração

```plain
nano ~/.config/MangoHud/MangoHud.conf

```

Substitua o conteúdo por:

```plain
# ╔══════════════════════════════════════╗
# ║         MangoHud Configuration       ║
# ║  Salve em: ~/.config/MangoHud/       ║
# ╚══════════════════════════════════════╝

legacy_layout=false

# ── Visual & Posição ──────────────────
position=top-left
width=240
font_size=18
background_alpha=0.4
alpha=0.8
round_corners=4
table_columns=3

# Cores dos textos (hex RGB)
gpu_color=2e9762
cpu_color=2e97cb
ram_color=c2a13b
fps_color=e06464
text_color=ffffff

# ── FPS ───────────────────────────────
fps
fps_metrics=avg,0.01     # avg = FPS médio | 0.01 = 1% Low

# ── GPU ───────────────────────────────
gpu_stats
gpu_temp
gpu_core_clock
gpu_power
gpu_fan
vram

# ── CPU ───────────────────────────────
cpu_stats       # Uso em %
cpu_temp        # Temperatura
cpu_mhz         # Clock (MHz)

# ── RAM ───────────────────────────────
ram

# ── Atalhos ───────────────────────────
toggle_hud=M+F12
reset_fps_metrics=M+F1

```

### Testando

```plain
mangohud glxgears   # OpenGL
mangohud vkcube     # Vulkan

```

Se o overlay aparecer no canto superior esquerdo, está funcionando.

***

## 7. Opções de Inicialização na Steam

Nas **Propriedades do jogo → Opções de Inicialização**:

```plain
game-performance mangohud %command%

```

| Opção | O que faz |
| --- | --- |
| `game-performance` | Script nativo do CachyOS. Ativa o perfil de energia "performance" via `power-profiles-daemon` durante o jogo e restaura o anterior ao fechar |
| `mangohud` | Injeta o overlay de estatísticas |
| `%command%` | Placeholder do Steam para o executável do jogo |

### Variáveis de Ambiente Úteis (AMD)

Adicione conforme necessário antes do `%command%`:

| Variável | Descrição |
| --- | --- |
| `ENABLE_LAYER_MESA_ANTI_LAG=1` | AMD Anti-Lag nativo do driver Mesa — reduz input lag |
| `AMD_VULKAN_ICD=RADV` | Garante o uso do driver RADV (padrão otimizado pra jogos) |
| `RADV_TEX_ANISO=16` | Força filtragem anisotrópica em jogos Vulkan |
| `PROTON_FSR4_UPGRADE=1` | Atualiza FSR para a versão mais recente do CachyOS |
| `PROTON_LOCAL_SHADER_CACHE=1` | Cache de shaders isolada por jogo |
| `PROTON_ENABLE_WAYLAND=1` | Suporte nativo Wayland — melhora latência e frame pacing; necessário para HDR |
| `PROTON_USE_NTSYNC=1` | Usa NTSync (Linux 6.14+) — alternativas: `WINEFSYNC=1` ou `WINEESYNC=1` |
| `PROTON_MLFG_UPGRADE=1` | AMD Fluid Motion Frames via ML |
| `PROTON_FORCE_LARGE_ADDRESS_AWARE=1` | Permite apps 32-bit usarem mais de 2 GB de RAM |
| `DXVK_FRAME_RATE=144` | Limita FPS em jogos D3D9/10/11 |
| `DXVK_CONFIG=fpsLimit=144` | Alternativa ao acima |
| `MESA_SHADER_CACHE_MAX_SIZE=12G` | Cache de shaders inline (alternativa ao método da seção 4) |

> Teste as variáveis uma a uma — algumas podem ter efeitos colaterais ou não ser suportadas em versões mais antigas.

***

## Resumo Rápido

| O quê | Por quê |
| --- | --- |
| `mesa` atualizado | Melhorias de desempenho e correção de bugs no driver |
| `amdgpu.ppfeaturemask=0xffffffff` | Desbloqueia controle total de clocks para o CoreCtrl |
| CoreCtrl clock mín. 1900 MHz | Elimina stutters por transição de clock |
| Cache de shaders 20G | Evita recompilação e stutters em jogos maiores |
| MangoHud | Visibilidade total do que acontece na GPU em tempo real |

***

## Troubleshooting

**CoreCtrl não está aplicando os clocks?** Confirma:

- `amdgpu.ppfeaturemask=0xffffffff` foi adicionado ao GRUB
- `sudo grub-mkconfig -o /boot/grub/grub.cfg` foi rodado
- O PC foi reiniciado
- O perfil Gaming está ativo no CoreCtrl

***

Bons jogos! 🎮
