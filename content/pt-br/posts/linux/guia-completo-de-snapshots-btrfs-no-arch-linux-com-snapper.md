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

## O que é Btrfs

Sistema de arquivos moderno com **Copy-on-Write (CoW)**: em vez de sobrescrever arquivos, cria uma nova versão antes de alterar a antiga. Isso permite snapshots rápidos e rollback do sistema.

---

## Subvolumes

"Mini partições" dentro do mesmo disco. No Arch/CachyOS:

```
@       → /
@home   → /home
```

Snapshot de `/` **não** inclui `/home`.

```bash
sudo btrfs subvolume list /
findmnt -t btrfs
```

---

## Snapshots

```bash
# Raiz
sudo btrfs subvolume snapshot -r / /.snapshots/meu-snap-$(date +%Y-%m-%d_%H-%M)

# Home
sudo btrfs subvolume snapshot -r /home /home/.snapshots/home-snap-$(date +%Y-%m-%d_%H-%M)
```

**Snapshot ≠ Backup.** Se o SSD morrer, os snapshots morrem junto.

---

## Snapper

```bash
# Instalar
sudo pacman -S snapper snap-pac grub-btrfs

# Configurar
sudo snapper -c root create-config /
sudo snapper -c home create-config /home

# Ativar
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

Cria snapshot PRE + POST automaticamente a cada `pacman -Syu`.

```bash
# Snapshot manual
sudo snapper -c root create --description "antes-de-atualizar"
```

---

## Rollback

```bash
sudo snapper list
sudo snapper rollback N
sudo reboot

# Recuperar arquivo apagado
sudo cp /.snapshots/2/snapshot/caminho/arquivo .

# Ver diferenças
sudo snapper diff 2..0
```

---

## Ver uso real do disco

`df -h` não é confiável no Btrfs.

```bash
sudo btrfs filesystem usage /

# Espaço por snapshot (coluna "excl" = quanto seria liberado ao deletar)
sudo btrfs qgroup show -reF /

# Peso dos snapshots
sudo du -sh /.snapshots
sudo du -sh /home/.snapshots 2>/dev/null
```

Sinal de alerta na saída do `filesystem usage`:
```
Data,single: Size:437.01GiB, Used:433.74GiB (99.25%)
```

---

## Apagar snapshots

> **Nunca use `rm -rf`.** Use `btrfs subvolume delete` ou Snapper.

```bash
sudo snapper list

# Apagar um
sudo snapper delete 20

# Apagar intervalo (verifique a lista antes — número inexistente falha o comando)
sudo snapper delete 14-94

# Home
sudo snapper -c home list
sudo snapper -c home delete 1-10
```

---

## Subvolumes de backup do rollback

Após um `snapper rollback`, o CachyOS cria subvolumes no nível raiz do Btrfs que **não aparecem** no `snapper list`:

```
@_backup_2026-05-31T16:39:04.582Z
@home_backup_2026-05-31T16:38:57.703Z
```

```bash
# Montar o nível raiz (confirme o dispositivo com findmnt -t btrfs)
sudo mkdir -p /mnt/btrfs-top
sudo mount -o subvolid=5 /dev/nvme0n1p2 /mnt/btrfs-top

ls -lah /mnt/btrfs-top

# Apagar os backups
sudo btrfs subvolume delete "/mnt/btrfs-top/@_backup_..."
sudo btrfs subvolume delete "/mnt/btrfs-top/@home_backup_..."

sudo btrfs subvolume sync /mnt/btrfs-top
```

---

## Forçar liberação do espaço

Após apagar, o espaço pode não aparecer imediatamente — o Btrfs trabalha com chunks de alocação.

```bash
sudo btrfs filesystem sync /
sudo btrfs balance start -dusage=50 /

df -h
sudo btrfs filesystem usage /
```

---

## Por que os snapshots enchem o disco

O `snap-pac` cria PRE + POST para **cada** operação do pacman, mesmo pacotes pequenos. Snapshots do `/home` também preservam arquivos grandes já deletados — se havia snapshot quando um jogo de 50GB estava instalado, ele continua ocupando espaço mesmo após desinstalar.

---

## Limitar snapshots

```bash
sudo nano /etc/snapper/configs/root
# (repita para /home se necessário)
```

Conservador (recomendado para SSDs menores):

```
TIMELINE_LIMIT_HOURLY="3"
TIMELINE_LIMIT_DAILY="3"
TIMELINE_LIMIT_WEEKLY="1"
TIMELINE_LIMIT_MONTHLY="0"
TIMELINE_LIMIT_YEARLY="0"

NUMBER_LIMIT="10"
NUMBER_LIMIT_IMPORTANT="5"
```

```bash
# Forçar limpeza manualmente
sudo snapper cleanup number
sudo snapper cleanup timeline
```

---

## Desligar snapshots automáticos (opcional)

```bash
sudo systemctl disable --now snapper-timeline.timer
sudo systemctl disable --now snapper-cleanup.timer
```

Ou só parar a criação mantendo o Snapper ativo:
```
TIMELINE_CREATE="no"
```

```bash
sudo systemctl list-timers | grep snapper
```

---

## Liberar espaço adicional

```bash
# Cache do pacman
sudo du -sh /var/cache/pacman/pkg
sudo paccache -rk2
sudo pacman -Scc

# Logs
sudo journalctl --vacuum-time=7d

# Encontrar o que está pesando
sudo du -xh / | sort -h | tail -40
sudo pacman -S ncdu && sudo ncdu /
```

---

## Manutenção

```bash
sudo btrfs scrub start -Bd /
sudo btrfs device stats /
```

---

## Resumo

```
Btrfs
├── CoW → copia antes de alterar
├── Subvolumes → @ e @home separados
├── Snapshots → rollback rápido
├── Snapper → automação
└── grub-btrfs → snapshots no boot
```

**Fluxo normal:**
```
pacman -Syu → PRE → update → POST → deu problema? → snapper rollback N → reboot
```

**Fluxo de limpeza:**
```
snapper list
↓ snapper delete <intervalo>
↓ verificar subvolumes de backup no btrfs-top
↓ btrfs subvolume delete "@_backup_..."
↓ btrfs filesystem sync / && btrfs balance start -dusage=50 /
↓ df -h
↓ ajustar limites em /etc/snapper/configs/root
```
