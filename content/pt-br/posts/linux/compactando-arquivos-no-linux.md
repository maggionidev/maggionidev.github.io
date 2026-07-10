---
title: Compactando arquivos no Linux
slug: linux-compressing-files
description: ''
summary: ''
cover: null
tags:
  - linux
  - 7z
  - zip
categories:
  - linux
keywords: []
author: Gabriel Maggioni
date: 2026-07-09T21:25:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

A compactação de arquivos faz parte do dia a dia de quem usa Linux. Ela economiza espaço em disco, facilita o envio de arquivos pela internet, reduz o tempo de transferência e permite reunir vários arquivos em um único pacote.

Se você usa uma distribuição baseada em Arch Linux, como Arch Linux, CachyOS, EndeavourOS ou Manjaro, praticamente todas as ferramentas apresentadas aqui estão disponíveis nos repositórios oficiais.

Neste guia você aprenderá:

* O que é compactação.
* A diferença entre compactar e arquivar.
* Como usar os principais formatos.
* Como criar e extrair arquivos.
* Como fazer compressão máxima.
* Quando cada algoritmo é recomendado.
* Comandos úteis para o dia a dia.

---

# Entendendo a diferença entre arquivar e compactar

Muita gente acha que são a mesma coisa, mas não são.

## Arquivar

Arquivar significa apenas juntar vários arquivos em um único.

Exemplo:

```
foto1.jpg
foto2.jpg
foto3.jpg
```

vira

```
fotos.tar
```

O tamanho praticamente não muda.

Quem faz isso normalmente é o `tar`.

---

## Compactar

Compactar significa reduzir o tamanho dos dados.

Exemplo:

```
1 GB
```

pode virar

```
600 MB
```

dependendo do conteúdo.

Quem faz isso são programas como:

* gzip
* bzip2
* xz
* zstd
* 7z
* lrzip

---

## Arquivar + Compactar

Na prática quase sempre usamos os dois juntos.

```
Arquivos
      ↓
tar
      ↓
arquivo.tar
      ↓
gzip
      ↓
arquivo.tar.gz
```

---

# Instalando as ferramentas

No Arch Linux:

```bash
sudo pacman -S tar gzip bzip2 xz zstd p7zip lrzip unzip zip
```

---

# TAR

O TAR apenas junta arquivos.

Criando:

```bash
tar -cf arquivos.tar pasta/
```

Extrair:

```bash
tar -xf arquivos.tar
```

Ver conteúdo:

```bash
tar -tf arquivos.tar
```

---

# GZIP

É um dos formatos mais antigos e compatíveis.

Compactar:

```bash
gzip arquivo.txt
```

Resultado:

```
arquivo.txt.gz
```

Descompactar:

```bash
gunzip arquivo.txt.gz
```

ou

```bash
gzip -d arquivo.txt.gz
```

---

# TAR + GZIP

Muito comum.

Criar:

```bash
tar -czf backup.tar.gz pasta/
```

Extrair:

```bash
tar -xzf backup.tar.gz
```

---

# BZIP2

Compacta melhor que gzip, mas é mais lento.

Criar:

```bash
tar -cjf backup.tar.bz2 pasta/
```

Extrair:

```bash
tar -xjf backup.tar.bz2
```

---

# XZ

Excelente taxa de compressão.

Criar:

```bash
tar -cJf backup.tar.xz pasta/
```

Extrair:

```bash
tar -xJf backup.tar.xz
```

Modo máximo:

```bash
xz -9e arquivo.iso
```

Explicação:

* `-9` = máxima compressão
* `-e` = modo extremo

---

# Zstandard (zstd)

Hoje é um dos melhores algoritmos.

Possui:

* ótima velocidade
* excelente compressão
* uso em várias distribuições Linux

Compactar:

```bash
zstd arquivo.iso
```

Extrair:

```bash
unzstd arquivo.iso.zst
```

Compressão máxima:

```bash
zstd -22 --ultra arquivo.iso
```

Usando todos os núcleos:

```bash
zstd -22 --ultra -T0 arquivo.iso
```

Arquivar e compactar:

```bash
tar -cf - pasta | zstd -19 -T0 -o backup.tar.zst
```

Extrair:

```bash
tar --zstd -xf backup.tar.zst
```

---

# ZIP

Muito usado para compartilhar arquivos com Windows.

Criar:

```bash
zip -r arquivo.zip pasta/
```

Extrair:

```bash
unzip arquivo.zip
```

Listar:

```bash
unzip -l arquivo.zip
```

---

# 7-Zip

Provavelmente o melhor formato para uso geral.

Criar:

```bash
7z a arquivo.7z pasta/
```

Extrair:

```bash
7z x arquivo.7z
```

Listar:

```bash
7z l arquivo.7z
```

Testar integridade:

```bash
7z t arquivo.7z
```

---

# Compressão máxima com 7-Zip

```bash
7z a arquivo.7z pasta \
    -mx=9 \
    -m0=lzma2 \
    -md=8g \
    -mfb=273 \
    -ms=on \
    -mmt=on
```

Significado:

| Opção     | Explicação               |
| --------- | ------------------------ |
| -mx=9     | compressão máxima        |
| -m0=lzma2 | algoritmo LZMA2          |
| -md=8g    | dicionário de 8 GB       |
| -mfb=273  | máxima busca por padrões |
| -ms=on    | compressão sólida        |
| -mmt=on   | usa todos os núcleos     |

Se você possui bastante RAM:

```
-md=16g
```

funciona muito bem em máquinas com 32 GB ou mais.

---

# LRZIP

Pouco conhecido, mas excelente para arquivos enormes.

Instalar:

```bash
sudo pacman -S lrzip
```

Compactar:

```bash
lrzip arquivo.iso
```

Modo máximo:

```bash
lrzip -L9 arquivo.iso
```

Extrair:

```bash
lrzip -d arquivo.iso.lrz
```

---

# Qual algoritmo comprime mais?

Em geral:

| Algoritmo | Compressão | Velocidade |
| --------- | ---------: | ---------: |
| gzip      |         ⭐⭐ |      ⭐⭐⭐⭐⭐ |
| bzip2     |        ⭐⭐⭐ |        ⭐⭐⭐ |
| xz        |      ⭐⭐⭐⭐⭐ |          ⭐ |
| zstd      |       ⭐⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |
| 7z LZMA2  |      ⭐⭐⭐⭐⭐ |         ⭐⭐ |
| lrzip     |      ⭐⭐⭐⭐⭐ |          ⭐ |

---

# Nem todo arquivo pode ser comprimido

Esse é um erro muito comum.

Arquivos já comprimidos dificilmente diminuem de tamanho novamente.

Exemplos:

```
.mp4
.mkv
.jpg
.png
.webp
.mp3
.flac
.zip
.rar
.7z
.xz
.gz
.zst
.nsp
.xci
.apk
```

Você pode tentar compactá-los, mas normalmente o arquivo final fica praticamente do mesmo tamanho ou até alguns kilobytes maior devido aos metadados do formato.

---

# Arquivos que costumam comprimir muito

```
.txt
.log
.csv
.json
.xml
.html
.css
.js
.cpp
.c
.py
.java
.sql
```

Também:

* imagens BMP
* áudio WAV sem compressão
* bancos de dados
* máquinas virtuais
* ISOs contendo muitos arquivos de texto

---

# Como descobrir o tipo do arquivo

```bash
file arquivo
```

Exemplo:

```
arquivo: gzip compressed data
```

---

# Ver o tamanho dos arquivos

Formato legível:

```bash
du -sh arquivo
```

Ou:

```bash
ls -lh
```

---

# Comparando algoritmos rapidamente

## Gzip

Muito rápido.

Ideal para:

* logs
* servidores
* backups simples

---

## XZ

Compacta muito.

Ideal para:

* backups
* ISOs
* distribuição de software

---

## Zstandard

Equilíbrio excelente.

Ideal para praticamente tudo.

---

## 7-Zip

Ótima compressão e muitos recursos.

Ideal para:

* armazenamento
* compartilhamento
* backups

---

## LRZIP

Especialista em arquivos gigantes.

Ideal para:

* imagens de disco
* backups enormes

---

# Exemplos práticos

Compactar uma pasta inteira:

```bash
tar -czf fotos.tar.gz Fotos/
```

Extrair:

```bash
tar -xzf fotos.tar.gz
```

---

Criar um 7z:

```bash
7z a fotos.7z Fotos/
```

---

Extrair:

```bash
7z x fotos.7z
```

---

Criar um ZIP:

```bash
zip -r fotos.zip Fotos/
```

---

Extrair:

```bash
unzip fotos.zip
```

---

Criar um TAR + ZSTD:

```bash
tar -cf - Fotos | zstd -19 -T0 -o fotos.tar.zst
```

---

Extrair:

```bash
tar --zstd -xf fotos.tar.zst
```

---

# Dicas finais

* Não espere milagres ao compactar arquivos que já utilizam compressão interna, como vídeos, músicas, imagens JPEG ou jogos em formatos como `.nsp` e `.xci`.
* Para documentos, código-fonte e arquivos de texto, o ganho costuma ser significativo.
* O `zstd` é uma excelente escolha para uso diário por combinar velocidade e boa taxa de compressão.
* O `7z` com LZMA2 continua sendo uma das melhores opções quando o objetivo é obter a menor quantidade possível de dados, mesmo que isso leve mais tempo.
* Se você pretende apenas agrupar arquivos sem reduzir o tamanho, use apenas o `tar`.
* Para compartilhar arquivos com usuários de Windows, o formato ZIP continua sendo a opção mais compatível.

## Conclusão

Não existe um único algoritmo que seja o melhor em todas as situações. A escolha depende do equilíbrio entre velocidade, compatibilidade e taxa de compressão.

Como regra geral:

* **ZIP**: máxima compatibilidade.
* **gzip**: rápido e amplamente suportado.
* **xz**: excelente compressão para distribuição de arquivos.
* **zstd**: melhor equilíbrio entre desempenho e eficiência.
* **7z (LZMA2)**: uma das maiores taxas de compressão para uso geral.
* **lrzip**: indicado para arquivos muito grandes e cenários específicos.

Conhecer essas ferramentas permite escolher a opção mais adequada para cada tarefa, seja um backup, um pacote para distribuição ou simplesmente economizar espaço em disco.
