---
title: Liberando espaço dos backups automáticos do BTRFS
slug: ''
description: ''
summary: ''
cover: null
tags:
  - btrfs
  - linux
  - cachyos
categories:
  - linux
keywords: []
author: Gabriel Maggioni
date: 2026-07-12T13:35:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

# Como remover os backups automáticos do Btrfs após um Restore

Se você usa **CachyOS** ou qualquer distribuição Arch com **Btrfs** e **Btrfs Assistant**, talvez já tenha percebido que, depois de restaurar um snapshot, o espaço em disco continua ocupado.

Isso acontece porque o **Restore não substitui imediatamente o sistema antigo**.

Antes de restaurar o snapshot, o Btrfs Assistant cria automaticamente uma cópia dos seus subvolumes atuais para que seja possível desfazer a restauração caso algo dê errado.

Esses backups normalmente possuem nomes como:

```text
@_backup_2026-07-12T16:24:41.937Z
@home_backup_2026-07-12T16:24:46.605Z
```

Se você já verificou que a restauração funcionou e não pretende voltar ao estado anterior, pode apagar esses backups e recuperar bastante espaço.

## Passo 1: Monte o subvolume raiz do Btrfs

Normalmente trabalhamos apenas dentro do subvolume `@`, então esses backups ficam "escondidos".

Para enxergar todos os subvolumes do sistema, monte o nível superior do Btrfs (`subvolid=5`).

Primeiro descubra em qual partição está o sistema:

```bash
findmnt -no SOURCE /
```

Exemplo de saída:

```text
/dev/nvme0n1p2
```

Agora monte o topo do Btrfs:

```bash
sudo mount -o subvolid=5 /dev/nvme0n1p2 /mnt
```

## Passo 2: Liste todos os subvolumes

```bash
sudo btrfs subvolume list /mnt
```

Você verá algo parecido com isto:

```text
ID 413 path @
ID 414 path @home
ID 389 path @_backup_2026-07-12T16:24:41.937Z
ID 390 path @home_backup_2026-07-12T16:24:46.605Z
```

Os subvolumes iniciados por:

- `@_backup_`
- `@home_backup_`

são justamente os backups criados automaticamente durante o Restore.

## Passo 3: Remova os backups

Depois de confirmar que o sistema restaurado está funcionando corretamente, remova os subvolumes de backup:

```bash
sudo btrfs subvolume delete /mnt/@_backup_2026-07-12T16:24:41.937Z

sudo btrfs subvolume delete /mnt/@home_backup_2026-07-12T16:24:46.605Z
```

Substitua os nomes pelos que aparecem no seu computador.

## Passo 4: Desmonte o topo do Btrfs

Quando terminar, basta desmontar:

```bash
sudo umount /mnt
```

Isso apenas remove a montagem temporária. O sistema continua funcionando normalmente.

## Passo 5: Reorganize o espaço livre

Embora os backups tenham sido apagados, o Btrfs pode manter parte dos blocos alocados.

Execute um balance leve para reorganizar os dados e liberar espaço:

```bash
sudo btrfs balance start -dusage=5 -musage=5 /
```

## Passo 6: Confira o espaço recuperado

```bash
df -h
```

Ou veja informações mais detalhadas:

```bash
sudo btrfs filesystem usage /
```

## Conclusão

Os backups automáticos existem para proteger você durante uma restauração. Eles são uma rede de segurança caso o sistema restaurado apresente problemas.

Depois que você confirmar que tudo está funcionando corretamente, esses subvolumes deixam de ter utilidade e podem ser removidos para recuperar espaço em disco.

Em alguns casos, principalmente após várias restaurações, eles podem ocupar dezenas ou até centenas de gigabytes sem que o usuário perceba.
