---
title: Como identificar pendrives falsos e testar a saúde do seu pendrive no CachyOS/Arch
slug: how-test-and-identify-fake-pendrive
description: ''
summary: ''
cover: null
tags:
  - linux
  - cachyos
  - arch
categories:
  - linux
keywords: []
author: Gabriel Maggioni
date: 2026-07-21T23:15:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

Pendrives e cartões de memória falsificados são mais comuns do que parece, principalmente em compras feitas em marketplaces. O golpe é simples: o firmware do dispositivo informa ao sistema operacional uma capacidade maior do que a real. O Linux (ou qualquer SO) acredita nessa informação e permite que você "copie" arquivos além do limite físico de memória - só que, na prática, esses dados vão sobrescrevendo silenciosamente o que já foi gravado antes.

O resultado é que o pendrive parece funcionar normalmente no dia a dia, até o momento em que você precisa abrir um arquivo antigo e ele simplesmente está corrompido.

Esse post mostra como diagnosticar isso com precisão no CachyOS/Arch, usando a ferramenta feita exatamente para esse propósito: o **F3 (Fight Flash Fraud)**.

## Instalando o F3

```bash
sudo pacman -S f3
```

O pacote inclui várias ferramentas: `f3write`, `f3read`, `f3probe` e `f3fix`. Cada uma serve um propósito diferente no diagnóstico.

## Passo 1 — Identificar o dispositivo

Antes de qualquer coisa, confirme qual é o device correto do seu pendrive. Errar esse passo é perigoso, já que os comandos seguintes podem sobrescrever dados.

```bash
lsblk -f
```

Isso lista todos os discos e partições montados, com o label e ponto de montagem. Também é possível conferir os detalhes reportados pelo kernel:

```bash
sudo dmesg | grep -i -B2 -A10 "sdX"
```

(troque `sdX` pelo nome real do dispositivo, ex: `sdb`)

## Passo 2 — Teste de escrita e leitura (f3write / f3read)

Esse é o teste mais completo, já que grava dados reais no pendrive inteiro e depois confere byte a byte se o que foi lido bate com o que foi escrito.

Com o pendrive montado:

```bash
f3write /caminho/do/pendrive
f3read /caminho/do/pendrive
```

O `f3write` preenche todo o espaço livre com arquivos de teste com padrões conhecidos, mostrando a velocidade de escrita em tempo real. O `f3read` faz a validação.

### Interpretando o resultado

No final do `f3read`, o que importa é:

```
Data OK: <valor>
Data LOST: <valor>
```

- **Data LOST = 0 ou próximo disso** → pendrive genuíno, memória íntegra.
- **Data LOST alto** (uma boa fatia da capacidade anunciada) → pendrive falsificado. O firmware está reportando uma capacidade maior do que a memória flash real instalada.

Vale reparar também na **velocidade de escrita** durante o `f3write`: quedas abruptas de velocidade no meio do processo, ou velocidades muito abaixo do esperado para USB 3.0, são outro indício de fraude ou de chip de baixa qualidade.

**Tempos de referência** (para um pendrive de 64GB genuíno):
- USB 2.0: ~35 min a 1h40
- USB 3.0/3.1/3.2: ~7 a 20 min

Esse teste é demorado (pode passar de 1h em pendrives grandes), mas é o mais confiável porque testa a integridade real dos dados.

## Passo 3 — Descobrir a capacidade real rapidamente (f3probe)

Se você já suspeita que o pendrive é falso e só quer confirmar a capacidade real sem esperar horas, o `f3probe` faz isso em minutos usando busca binária, sem precisar gravar o dispositivo inteiro.

```bash
sudo umount /caminho/do/pendrive
sudo f3probe --destructive --time-ops /dev/sdX
```

⚠️ A flag `--destructive` apaga os dados do pendrive durante o teste — use apenas em um pendrive vazio ou que você já vai formatar de qualquer forma.

O resultado mostra diretamente:

```
Device geometry:
                 *Usable* size: <capacidade real>
                Announced size: <capacidade anunciada>
```

Se a ferramenta identificar fraude, ela classifica o dispositivo explicitamente como `counterfeit` (falsificado) e já sugere o comando para corrigir a partição.

## Passo 4 — Corrigindo o pendrive para uso seguro (f3fix)

Se você decidir usar o pendrive mesmo assim (por exemplo, como pendrive de boot pequeno), o ideal é limitar a partição à capacidade real, evitando que ele continue aceitando gravações além do que existe fisicamente:

```bash
sudo f3fix --last-sec=<último_setor_reportado_pelo_f3probe> /dev/sdX
sudo mkfs.vfat /dev/sdX1
```

Isso não "aumenta" a capacidade real do pendrive — apenas impede que o sistema tente gravar além do limite físico, evitando corrupção silenciosa de dados.

## Resumo rápido

```bash
sudo pacman -S f3

# Teste completo (demorado, mas definitivo)
f3write /caminho/do/pendrive
f3read /caminho/do/pendrive

# Teste rápido de capacidade real (destrutivo)
sudo f3probe --destructive --time-ops /dev/sdX
```

| Sinal | Diagnóstico |
|---|---|
| `Data LOST` próximo de 0 | Pendrive genuíno |
| `Data LOST` alto | Pendrive falsificado |
| Velocidade de escrita caindo abruptamente | Fraude ou chip de baixa qualidade |
| f3probe classifica como `counterfeit` | Confirmação técnica de fraude |

## Nota pessoal

Testei um pendrive SanDisk de 64GB comprado recentemente na shoppe, e o resultado foi bem claro: o `f3probe` identificou apenas **507,62 MB reais** de espaço utilizável, contra os 58,59GB anunciados — menos de 1% da capacidade prometida, classificado como `counterfeit` pela própria ferramenta. Com o print desse teste em mãos, abri reclamação e reembolso no marketplace onde comprei.

Se você tem um pendrive parado há tempos sem nunca ter testado, vale rodar esse diagnóstico antes de confiar nele com arquivos importantes.
