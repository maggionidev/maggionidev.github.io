---
title: Guia Completo de Snapshots Btrfs no Arch Linux com Snapper
slug: btrfs-snapshots-arch-linux
description: Aprenda como funcionam snapshots Btrfs no Arch Linux e CachyOS usando Snapper. Veja como criar snapshots automáticos, rollback, recuperar arquivos, limitar espaço usado e configurar subvolumes corretamente.
summary: Aprenda como funcionam snapshots Btrfs no Arch Linux e CachyOS usando Snapper. Veja como criar snapshots automáticos, rollback, recuperar arquivos, limitar espaço usado e configurar subvolumes corretamente.
tags:
  - btrfs
  - snapper
  - arch
  - cachyos
  - linux
  - snapshots
  - rollback
categories:
  - linux
keywords:
  - btrfs
  - snapper
  - arch linux
  - cachyos
  - linux
  - snapshots
  - rollback
  - filesystem
author: Gabriel Maggioni
date: 2026-05-08T19:24:00
lastmod: 2026-05-18T19:47:00
showToc: true
TocOpen: false
draft: false
---

# O que é Btrfs?

Btrfs é um sistema de arquivos moderno do Linux. Ele funciona como o "organizador" do SSD: decide como os arquivos são salvos, movidos e recuperados.

O diferencial dele é o **Copy-on-Write (CoW)**:

> em vez de sobrescrever arquivos diretamente, ele cria uma nova versão antes de alterar a antiga.

É isso que permite snapshots rápidos e rollback do sistema.

***

## Subvolumes

O Btrfs divide o sistema em subvolumes, que são tipo "mini partições" dentro do mesmo disco.

Normalmente no Arch/CachyOS:

```bash
@       → /
@home   → /home
```

Isso é importante porque:

* snapshot de `/` NÃO inclui `/home`
* sistema e arquivos pessoais ficam separados

Ver subvolumes:

```bash
sudo btrfs subvolume list /
```

Ver montagem:

```bash
findmnt -t btrfs
```

***

## Snapshots

Snapshot é uma "foto" do sistema naquele momento.

Eles são:

* quase instantâneos
* leves no começo
* ótimos pra desfazer updates quebrados

Criar snapshot da raiz:

```bash
sudo btrfs subvolume snapshot -r / /.snapshots/meu-snap-$(date +%Y-%m-%d_%H-%M)
```

Do home:

```bash
sudo btrfs subvolume snapshot -r /home /home/.snapshots/home-snap-$(date +%Y-%m-%d_%H-%M)
```

***

## Snapshot NÃO é backup

Snapshot fica no mesmo SSD.

| Problema | Snapshot resolve? |
| --- | --- |
| Update quebrou o sistema | ✓ |
| Arquivo apagado | ✓ |
| SSD morreu | ✗ |

Se o disco morrer, os snapshots morrem junto.

***

## Snapper

O Snapper automatiza snapshots.

Instalar:

```bash
sudo pacman -S snapper snap-pac grub-btrfs
```

Configurar:

```bash
sudo snapper -c root create-config /
sudo snapper -c home create-config /home
```

Ativar:

```bash
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

Depois disso:

* snapshots automáticos são criados
* antes de cada `pacman -Syu` ele salva um PRE
* depois cria um POST

***

## Snapshot manual

```bash
# Criar snapshot manual antes de uma atualização arriscada
sudo snapper -c root create --description "antes-de-atualizar"
```

***

## Rollback

Listar snapshots:

```bash
sudo snapper list
```

Voltar para um snapshot antigo:

```bash
sudo snapper rollback N
sudo reboot
```

Isso literalmente volta o sistema para o estado anterior.

***

## Recuperar arquivo apagado

Copiar arquivo de dentro do snapshot:

```bash
sudo cp /.snapshots/2/snapshot/caminho/arquivo .
```

Ver diferenças:

```bash
sudo snapper diff 2..0
```

***

## Ver uso real do disco

`df -h` não mostra corretamente no Btrfs.

Use:

```bash
sudo btrfs filesystem usage /
```

Ver espaço dos snapshots:

```bash
sudo btrfs qgroup show -reF /
```

A coluna `excl` mostra quanto espaço seria liberado ao deletar cada snapshot.

***

## Diagnóstico: descobrindo o que está pesando

Antes de sair apagando, vale entender o tamanho real do problema:

```bash
# Uso geral do Btrfs (mais preciso que df -h)
sudo btrfs filesystem usage /

# Peso total dos snapshots do sistema
sudo du -sh /.snapshots

# Peso dos snapshots do home (se existir)
sudo du -sh /home/.snapshots 2>/dev/null
```

O sinal de alerta é quando você vê algo assim na saída do `btrfs filesystem usage`:

```plain
Data,single: Size:437.01GiB, Used:433.74GiB (99.25%)
```

Isso significa que o Btrfs alocou quase tudo e está realmente cheio — não é ilusão do `df`.

***

## Apagando snapshots antigos

### Deletar snapshot corretamente

NÃO use `rm -rf`.

Correto:

```bash
sudo btrfs subvolume delete /.snapshots/meu-snap
```

### Apagando via Snapper (forma recomendada)

Listar todos os snapshots do sistema:

```bash
sudo snapper list
```

Apagar um snapshot específico:

```bash
sudo snapper delete 20
```

Apagar um intervalo de uma vez:

```bash
sudo snapper delete 14-94
```

> **Atenção:** se algum número não existir no intervalo, o comando vai falhar. Verifique a lista antes e ajuste o intervalo para conter apenas números existentes.

Fazer o mesmo para o `/home`:

```bash
sudo snapper -c home list
sudo snapper -c home delete 1-10
```

### Forçar sincronização e rebalancear o disco

Após apagar, o espaço pode não aparecer imediatamente porque o Btrfs trabalha com "chunks" de alocação. Rode nessa ordem:

```bash
sudo btrfs filesystem sync /
sudo btrfs balance start -dusage=50 /
```

O `balance` pode demorar vários minutos dependendo do tamanho do SSD. Depois confira:

```bash
df -h
sudo btrfs filesystem usage /
```

***

## Por que os snapshots enchem o disco (o problema real)

O snap-pac cria um snapshot PRE e um POST para **cada operação do pacman** — incluindo instalações simples de pacotes pequenos como `htop` ou `wl-clipboard`. Em pouco tempo você acumula dezenas de snapshots sem perceber.

O detalhe mais importante: **os snapshots do `/home` preservam arquivos grandes mesmo depois de deletados.** Se você instalou um jogo enorme pelo Steam, desinstalou, mas havia um snapshot naquele momento, os arquivos do jogo continuam existindo dentro do snapshot. O espaço aparentemente some sem explicação óbvia.

***

## Limitar quantidade de snapshots

O Snapper permite limitar automaticamente quantos snapshots ficam salvos. Isso evita o SSD enchendo aos poucos sem perceber.

As configurações ficam em:

```bash
sudo nano /etc/snapper/configs/root
```

Se você também usa snapshots do `/home`:

```bash
sudo nano /etc/snapper/configs/home
```

### Limites dos snapshots automáticos

Procure estas linhas:

```ini
TIMELINE_LIMIT_HOURLY="5"
TIMELINE_LIMIT_DAILY="7"
TIMELINE_LIMIT_WEEKLY="4"
TIMELINE_LIMIT_MONTHLY="3"
TIMELINE_LIMIT_YEARLY="0"
```

Exemplo conservador (recomendado para SSDs menores):

```ini
TIMELINE_LIMIT_HOURLY="3"
TIMELINE_LIMIT_DAILY="3"
TIMELINE_LIMIT_WEEKLY="1"
TIMELINE_LIMIT_MONTHLY="0"
TIMELINE_LIMIT_YEARLY="0"
```

Quando o limite é atingido, os snapshots mais antigos são apagados automaticamente.

### Limitar snapshots criados pelo pacman

Os snapshots PRE e POST feitos antes/depois de updates usam estas opções:

```ini
NUMBER_LIMIT="10"
NUMBER_LIMIT_IMPORTANT="5"
```

### Aplicar limpeza manualmente

Depois de alterar as configs, você pode forçar a limpeza:

```bash
sudo snapper cleanup number
sudo snapper cleanup timeline
```

***

## Desligar snapshots automáticos (opcional)

Se você realmente não usa rollback e quer evitar o acúmulo completamente:

```bash
# Desligar criação automática por tempo
sudo systemctl disable --now snapper-timeline.timer

# Desligar limpeza automática
sudo systemctl disable --now snapper-cleanup.timer
```

Ou editar a config e apenas parar a criação automática mantendo o snapper ativo para uso manual:

```ini
TIMELINE_CREATE="no"
```

> Só recomendo isso se você tem certeza que não precisa de rollback. Snapshot salva a pele quando um update quebra o boot.

Para verificar o status dos timers:

```bash
sudo systemctl list-timers | grep snapper
```

***

## Liberar espaço adicional além dos snapshots

### Cache do pacman

Esse aqui sozinho pode estar ocupando 10–20GB:

```bash
# Manter só as 2 últimas versões de cada pacote
sudo paccache -rk2

# Remover apenas pacotes não instalados
sudo pacman -Sc

# Limpar tudo (cuidado: vai baixar novamente em updates futuros)
sudo pacman -Scc
```

### Logs do sistema

```bash
sudo journalctl --vacuum-time=7d
```

### Encontrar o que realmente está ocupando espaço

```bash
# Ver os 40 maiores arquivos/pastas
sudo du -xh / | sort -h | tail -40

# Ou instalar o ncdu para uma interface navegável
sudo pacman -S ncdu
sudo ncdu /
```

O `ncdu` é muito melhor para navegar e encontrar pastas gigantes escondidas.

***

## Manutenção

Verificar integridade:

```bash
sudo btrfs scrub start -Bd /
```

Ver erros físicos do SSD:

```bash
sudo btrfs device stats /
```

***

## Resumo rápido

```text
Btrfs
├── CoW → copia antes de alterar
├── Subvolumes → @ e @home separados
├── Snapshots → rollback rápido
├── Snapper → automação
└── grub-btrfs → snapshots no boot
```

Fluxo normal:

```text
pacman -Syu
↓
snapshot PRE automático
↓
update
↓
snapshot POST automático
↓
deu problema?
↓
snapper rollback N
↓
reboot
```

Fluxo de limpeza quando o SSD enche:

```text
sudo snapper list
↓
sudo snapper delete <intervalo>
↓
sudo btrfs filesystem sync /
↓
sudo btrfs balance start -dusage=50 /
↓
df -h (confirmar espaço liberado)
↓
ajustar limites em /etc/snapper/configs/root
```
