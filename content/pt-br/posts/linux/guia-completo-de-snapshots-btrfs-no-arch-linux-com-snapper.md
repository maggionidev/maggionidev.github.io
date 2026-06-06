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
lastmod: 2026-06-06T14:32:00
showToc: true
TocOpen: false
draft: false
---

# Btrfs + Snapper no Arch Linux / CachyOS

## O que é Btrfs

Btrfs é um sistema de arquivos moderno que utiliza **Copy-on-Write (CoW)**.

Quando um arquivo é alterado, o Btrfs não sobrescreve os dados existentes imediatamente. Ele grava os dados modificados em outro local e só então atualiza as referências.

Isso permite recursos como:

* Snapshots instantâneos
* Rollback do sistema
* Compressão transparente
* Checksums para detecção de corrupção
* Subvolumes

***

# Subvolumes

Subvolumes são como "mini sistemas de arquivos" dentro da mesma partição Btrfs.

Configuração comum no Arch/CachyOS:

```text
@
@home
@srv
@cache
@snapshots
```

Normalmente:

```text
@      → /
@home  → /home
```

Por isso um snapshot da raiz não inclui sua pasta pessoal.

Ver os subvolumes existentes:

```bash
sudo btrfs subvolume list /
```

Exemplo:

```text
ID 400 path @
ID 399 path @home
ID 263 path .snapshots
```

Ver quais subvolumes estão montados:

```bash
findmnt -t btrfs
```

***

# Snapshots

Snapshots são "fotografias" do estado de um subvolume em determinado momento.

Eles são extremamente rápidos porque o Btrfs não copia todos os arquivos.

Criar snapshot somente leitura da raiz:

```bash
sudo btrfs subvolume snapshot -r / /.snapshots/root-snap-$(date +%Y-%m-%d_%H-%M)
```

Criar snapshot da home:

```bash
sudo btrfs subvolume snapshot -r /home /home/.snapshots/home-snap-$(date +%Y-%m-%d_%H-%M)
```

Importante:

> Snapshot não é backup.

Se o SSD morrer, os snapshots morrem junto porque estão armazenados no mesmo disco.

***

# Snapper

Snapper automatiza a criação e gerenciamento de snapshots.

Instalação:

```bash
sudo pacman -S snapper snap-pac grub-btrfs
```

Criar configurações:

```bash
sudo snapper -c root create-config /
sudo snapper -c home create-config /home
```

Ativar timers:

```bash
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

Ver configurações existentes:

```bash
sudo snapper list-configs
```

Exemplo:

```text
Config | Subvolume
root   | /
home   | /home
```

Criar snapshot manual:

```bash
sudo snapper -c root create --description "antes-da-atualizacao"
```

Listar snapshots:

```bash
sudo snapper list
```

***

# Rollback

Ver snapshots disponíveis:

```bash
sudo snapper list
```

Restaurar snapshot:

```bash
sudo snapper rollback N
sudo reboot
```

Exemplo:

```bash
sudo snapper rollback 33
```

Recuperar arquivo apagado:

```bash
sudo cp /.snapshots/33/snapshot/caminho/arquivo .
```

Comparar snapshots:

```bash
sudo snapper diff 33..0
```

***

# Entendendo o uso real do disco

Em Btrfs, `df -h` pode enganar porque snapshots compartilham blocos.

Ver uso real:

```bash
sudo btrfs filesystem usage /
```

Exemplo de alerta:

```text
Data,single: Size:437.01GiB, Used:433.74GiB (99.25%)
```

Acima de 90% merece atenção.

***

# Quanto espaço os snapshots ocupam?

Ver uso total dos snapshots:

```bash
sudo btrfs filesystem du -s /.snapshots
```

Exemplo:

```text
Total:      119.70GiB
Exclusive:    881MiB
Shared:      36.82GiB
```

Significado:

* **Total**: tamanho lógico dos arquivos.
* **Shared**: dados compartilhados com outros snapshots.
* **Exclusive**: espaço que seria liberado ao apagar os snapshots.

A coluna mais importante é **Exclusive**.

Ver cada snapshot individualmente:

```bash
sudo btrfs filesystem du /.snapshots/*
```

Serve para descobrir qual snapshot está consumindo espaço.

***

# Apagando snapshots

Nunca use:

```bash
rm -rf
```

Snapshots são subvolumes.

Use:

```bash
sudo snapper list
```

Apagar um snapshot:

```bash
sudo snapper delete 20
```

Apagar vários:

```bash
sudo snapper delete 14-94
```

Home:

```bash
sudo snapper -c home list
sudo snapper -c home delete 1-10
```

***

# O mistério do subvolid=5

Muita gente vê isto:

```bash
sudo btrfs subvolume list /
```

e encontra:

```text
@_backup_2026...
@home_backup_2026...
```

mas não consegue acessá-los.

Motivo:

O sistema normalmente monta apenas o subvolume padrão (`@`).

Ver qual é o subvolume padrão:

```bash
sudo btrfs subvolume get-default /
```

Exemplo:

```text
ID 400 path @
```

Isso significa que você está enxergando apenas o subvolume `@`, não a raiz real do Btrfs.

***

# Montando a raiz real do Btrfs

Todo sistema Btrfs possui um subvolume especial:

```text
subvolid=5
```

Ele representa o topo absoluto da árvore Btrfs.

Criar ponto de montagem:

```bash
sudo mkdir -p /mnt/btrfs-top
```

Montar a raiz real:

```bash
sudo mount -o subvolid=5 /dev/nvme0n1p2 /mnt/btrfs-top
```

Agora você consegue enxergar:

```bash
sudo ls -lah /mnt/btrfs-top
```

Exemplo:

```text
@
@home
@srv
@cache
@_backup_2026...
@home_backup_2026...
```

Desmontar quando terminar:

```bash
sudo umount /mnt/btrfs-top
```

Pense assim:

```text
subvolid=5
│
├── @
├── @home
├── @srv
├── @cache
├── .snapshots
├── @_backup_2026...
└── @home_backup_2026...
```

Normalmente o sistema monta apenas:

```text
@
```

Montar o `subvolid=5` permite enxergar toda a árvore.

***

# Backups criados pelo rollback

Após um rollback, o Snapper costuma criar subvolumes de segurança:

```text
@_backup_2026-05-31...
@home_backup_2026-05-31...
```

Eles não aparecem no:

```bash
sudo snapper list
```

mas aparecem em:

```bash
sudo btrfs subvolume list /
```

depois que você monta o `subvolid=5`.

Remover:

```bash
sudo btrfs subvolume delete "/mnt/btrfs-top/@_backup_..."
sudo btrfs subvolume delete "/mnt/btrfs-top/@home_backup_..."
```

Sincronizar remoção:

```bash
sudo btrfs subvolume sync /mnt/btrfs-top
```

***

# Liberando espaço após exclusões

Às vezes o espaço não aparece imediatamente.

Sincronizar:

```bash
sudo btrfs filesystem sync /
```

Reorganizar chunks parcialmente utilizados:

```bash
sudo btrfs balance start -dusage=50 /
```

Verificar novamente:

```bash
df -h
sudo btrfs filesystem usage /
```

***

# Por que snapshots lotam o SSD?

O Snapper cria snapshots PRE e POST durante atualizações.

Exemplo:

```text
PRE
↓
pacman -Syu
↓
POST
```

Mesmo uma atualização pequena gera novos snapshots.

Outro caso comum:

1. Instala jogo de 50 GB.
2. Cria snapshot.
3. Remove o jogo.

O snapshot continua preservando aqueles dados antigos.

Resultado: espaço continua ocupado.

***

# Limitar quantidade de snapshots

Editar:

```bash
sudo nano /etc/snapper/configs/root
```

Configuração conservadora:

```ini
TIMELINE_LIMIT_HOURLY="3"
TIMELINE_LIMIT_DAILY="3"
TIMELINE_LIMIT_WEEKLY="1"
TIMELINE_LIMIT_MONTHLY="0"
TIMELINE_LIMIT_YEARLY="0"

NUMBER_LIMIT="10"
NUMBER_LIMIT_IMPORTANT="5"
```

Executar limpeza manual:

```bash
sudo snapper cleanup number
sudo snapper cleanup timeline
```

***

# Desativar snapshots automáticos

Desativar completamente:

```bash
sudo systemctl disable --now snapper-timeline.timer
sudo systemctl disable --now snapper-cleanup.timer
```

Ou manter o Snapper instalado sem criar timelines:

```ini
TIMELINE_CREATE="no"
```

Ver timers ativos:

```bash
sudo systemctl list-timers | grep snapper
```

***

# Encontrar quem está ocupando espaço

Cache do pacman:

```bash
sudo du -sh /var/cache/pacman/pkg
sudo paccache -rk2
sudo pacman -Scc
```

Logs:

```bash
sudo journalctl --vacuum-time=7d
```

Diretórios mais pesados:

```bash
sudo du -xh / | sort -h | tail -40
```

Ferramenta interativa:

```bash
sudo pacman -S ncdu
sudo ncdu /
```

***

# Manutenção

Verificar integridade dos dados:

```bash
sudo btrfs scrub start -Bd /
```

Ver estatísticas dos dispositivos:

```bash
sudo btrfs device stats /
```

***

# Resumo

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
PRE snapshot
↓
atualização
↓
POST snapshot
↓
problema?
↓
snapper rollback N
↓
reboot
```

Fluxo de limpeza:

```text
snapper list
↓
snapper delete <intervalo>
↓
montar subvolid=5
↓
verificar @_backup_* e @home_backup_*
↓
btrfs subvolume delete
↓
btrfs filesystem sync /
↓
btrfs balance start -dusage=50 /
↓
btrfs filesystem usage /
```
