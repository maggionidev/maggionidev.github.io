---
title: Btop não mostra o uso da GFU
slug: btop-nao-mostra-gpu-amd
description: Como corrigir a ausência de dados da GPU AMD Radeon no monitor de sistema btop.
summary: Como corrigir a ausência de dados da GPU AMD Radeon no monitor de sistema btop.
cover: null
tags:
  - linux
  - cachyos
  - arch
categories:
  - cachyos
keywords: []
author: Gabriel Maggioni
date: 2026-07-12T16:14:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: true
draft: false
---

## Introdução

O btop é um dos monitores de sistema mais completos para terminal, mas em algumas instalações ele simplesmente não exibe os dados da GPU, mesmo quando a placa é compatível. Esse problema é comum em GPUs AMD, como a Radeon RX 6600, e costuma ter causa simples: falta de uma biblioteca ou de uma build específica do btop. Este artigo mostra como identificar e corrigir isso no Arch Linux e derivados, como o CachyOS.

## Requisitos

- Arch Linux ou derivado (CachyOS, Manjaro, EndeavourOS)
- Acesso a sudo
- yay ou outro helper de AUR instalado

## Desenvolvimento

### Por que a GPU não aparece

O btop depende da biblioteca ROCm SMI para coletar informações de GPUs AMD. Sem ela, o programa simplesmente ignora essa seção. Além disso, a versão do btop disponível nos repositórios oficiais muitas vezes não é compilada com suporte a GPU habilitado, o que torna a instalação da biblioteca insuficiente por si só.

### Instalando a biblioteca correta

O nome do pacote costuma gerar confusão. Não é rocm-smi, e sim rocm-smi-lib:

```
sudo pacman -S rocm-smi-lib
```

Esse pacote fornece a interface que o btop usa para ler temperatura, uso e memória da GPU.

### Conferindo a configuração

Depois da instalação, vale checar o arquivo `~/.config/btop/btop.conf` e confirmar que a linha abaixo está presente e configurada como "Auto" ou "On":

```
show_gpu_info = "Auto"
```

Na maioria dos casos esse valor já vem correto por padrão.

## Recapitulação

O problema tem origem na ausência da biblioteca rocm-smi-lib e, em muitos casos, na falta de suporte a GPU na build padrão do btop. A solução envolve instalar a biblioteca certa, trocar para a versão btop-gpu-git do AUR e confirmar a configuração show_gpu_info.

## Conclusão

Esse tipo de situação é comum em ferramentas de monitoramento no Linux, já que dependem de bibliotecas específicas para cada fabricante de hardware. Vale guardar esse processo para qualquer ferramenta parecida que não reconheça a GPU de primeira, já que a causa costuma ser a mesma: biblioteca ausente ou build sem suporte habilitado.
