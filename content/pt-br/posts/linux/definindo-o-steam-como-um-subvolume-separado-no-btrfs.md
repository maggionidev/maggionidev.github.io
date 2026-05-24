---
title: Definindo o Steam como um Subvolume  Separado no BTRFS
slug: ''
description: ''
summary: ''
tags:
  - steam
  - subvolume
  - btrfs
  - linux
  - cachyos
  - arch
categories:
  - linux
  - btrfs
keywords: []
author: Gabriel Maggioni
date: 2026-05-24T12:29:00
lastmod: 2026-05-24T12:29:00
showToc: true
TocOpen: false
draft: false
---

Quem usa **Btrfs** com snapshots automáticos do `/home` — via
[Snapper](https://wiki.archlinux.org/title/Snapper),
[Timeshift](https://github.com/linuxmint/timeshift) ou scripts próprios —
cedo ou tarde percebe que cada snapshot carrega junto vários gigabytes da
biblioteca do Steam. O resultado são snapshots gigantes, rollbacks demorados e
espaço em disco evaporando sem motivo.

A solução é simples: transformar o diretório `~/.local/share/Steam` em um
**subvolume Btrfs independente**. Subvolumes filhos não são incluídos em
snapshots do pai, então o Steam fica completamente fora dos seus backups de
`/home`.

***

## Pré-requisitos

- Partição raiz formatada em **Btrfs**
- `btrfs-progs` instalado
- Steam instalado (ou você vai instalar depois)
- Acesso `sudo`

***

## 1. Feche o Steam completamente

Antes de mexer no diretório, certifique-se de que o Steam \*\*não está em
execução\*\*:

```bash
pkill -9 steam || true
```

***

## 2. Faça backup dos dados atuais

Se já tiver jogos baixados, mova a pasta para um local temporário. Isso
preserva tudo enquanto recriamos a estrutura:

```bash
mv ~/.local/share/Steam ~/.local/share/Steam.bak
```

***

## 3. Crie o subvolume

Recrie o diretório como um subvolume Btrfs real:

```bash
sudo btrfs subvolume create ~/.local/share/Steam
```

Ajuste o dono para o seu usuário (o `sudo` cria com dono `root`):

```bash
sudo chown -R $USER:$USER ~/.local/share/Steam
```

***

## 4. Restaure os dados

Copie o conteúdo do backup de volta para dentro do novo subvolume:

```bash
cp -a ~/.local/share/Steam.bak/. ~/.local/share/Steam/
```

Quando a cópia terminar, remova o backup temporário:

```bash
rm -rf ~/.local/share/Steam.bak
```

> **Dica:** se quiser economizar espaço e tempo, e o backup ainda estiver
> na mesma partição Btrfs, você pode usar `btrfs send/receive` ou simplesmente
> referenciar os dados com reflinks (`cp --reflink=always`).

***

## 5. Verifique o resultado

Liste todos os subvolumes da raiz para confirmar que o Steam aparece como
subvolume independente:

```bash
sudo btrfs subvolume list /
```

A saída deve ser parecida com esta (os IDs e datas variam no seu sistema):

```plain
ID 256 gen 444 top level 5 path @_backup_2026-05-24T02:34:45.946Z
ID 257 gen 469 top level 5 path @home_backup_2026-05-24T02:34:50.945Z
ID 258 gen 711 top level 5 path @root
ID 259 gen 25 top level 5 path @srv
ID 260 gen 1088 top level 5 path @cache
ID 261 gen 1115 top level 5 path @tmp
ID 262 gen 1115 top level 5 path @log
ID 263 gen 1115 top level 329 path .snapshots
ID 264 gen 27 top level 329 path var/lib/portables
ID 265 gen 27 top level 329 path var/lib/machines
ID 279 gen 61 top level 263 path .snapshots/14/snapshot
ID 290 gen 126 top level 263 path .snapshots/25/snapshot
ID 291 gen 142 top level 263 path .snapshots/26/snapshot
ID 292 gen 1115 top level 330 path @home/gabriel/.local/share/Steam
```

O ponto-chave é a última linha: `@home/gabriel/.local/share/Steam` aparece
como um subvolume com seu próprio ID (`292`), subordinado ao subvolume
`@home` (top level `330`), mas **não incluído** em seus snapshots.

***

## 6. (Opcional) Configurar o Snapper para ignorar o subvolume

Se estiver usando o Snapper, ele já exclui subvolumes aninhados por padrão.
Mesmo assim, confira a configuração do seu perfil:

```bash
sudo snapper -c home get-config | grep SUBVOLUME
```

O campo `SUBVOLUME` deve apontar para o ponto de montagem do `@home`
(normalmente `/home`). Pronto — o Snapper jamais entrará em
`~/.local/share/Steam` ao criar um snapshot.

***

## Por que isso funciona?

O Btrfs trata subvolumes como **fronteiras de snapshot**. Quando você faz um
snapshot de `@home`, o kernel copia o estado da árvore de arquivos daquele
subvolume — mas para nos limites de cada subvolume filho. O diretório
`.local/share/Steam` aparece no namespace de arquivos normalmente, mas o
conteúdo interno fica em outro subvolume e simplesmente **não é incluído**.

***

## Resultado final

| Situação | Snapshot de /home inclui Steam? |
| --- | --- |
| Antes (diretório comum) | ✅ Sim — cada snapshot cresce GB |
| Depois (subvolume separado) | ❌ Não — snapshots leves e rápidos |

Seus snapshots de `/home` voltam a representar apenas \*\*seus arquivos
pessoais\*\*: configurações, documentos, projetos — sem arrastar a biblioteca
inteira de jogos junto.
