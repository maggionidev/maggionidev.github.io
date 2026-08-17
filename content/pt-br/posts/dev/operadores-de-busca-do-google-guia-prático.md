---
title: 'Operadores de busca do Google: guia prático'
slug: google-search-operators
description: Lista completa dos operadores de busca do Google que ainda funcionam, e como combinar pra achar exatamente o que você quer.
summary: ''
cover: null
tags:
  - google
categories:
  - dev
keywords: []
author: Gabriel Maggioni
date: 2026-08-16T23:12:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

## Resumo do post

Todo mundo usa o Google, mas quase ninguém usa o Google direito. Nesse post eu vou te mostrar os operadores de pesquisa (search operators): comandos que você adiciona na busca pra filtrar resultado com precisão cirúrgica, em vez de ficar rolando 5 páginas de lixo pra achar uma informação.

***

## Introdução

Cara, deixa eu te contar uma coisa: 90% das pessoas usam o Google exatamente do mesmo jeito que usavam em 2010. Digita umas palavras soltas, aperta enter, torce pra vir algo bom.

Só que o Google tem uma porrada de comandos escondidos que a maioria nunca aprendeu, porque ninguém ensina isso na escola nem em curso nenhum. É tipo aquele atalho de teclado que só o cara sênior do trabalho conhece e nunca te ensinou.

Esses comandos se chamam **operadores de pesquisa** (ou *search operators*, se você quiser parecer chique). São palavras ou símbolos que você adiciona à busca pra dizer pro Google exatamente o que você quer, e não o que ele *acha* que você quer.

Vou te mostrar os que realmente valem a pena, sem entulhar a lista com operador raro que você nunca vai usar na vida.

***

## Por que isso importa

Pensa assim: buscar sem operador é tipo escrever uma query de banco de dados sem WHERE. Você traz a tabela inteira e fica catando na mão o que interessa.

Com operador, você já filtra na fonte. Menos scroll, menos lixo, resposta mais rápida. Se você trabalha com SEO, pesquisa, programação ou só quer parar de perder tempo procurando coisa na internet, isso aqui vale muito mais que qualquer curso de "produtividade" que te vendem por aí.

***

## Operadores que valem a pena conhecer

### Aspas `" "`

Busca a frase exata, na ordem exata. Sem sinônimo, sem o Google "interpretando" o que você quis dizer.

```plain
"erro de segmentação no kernel"
```

Se você já perdeu tempo procurando uma mensagem de erro específica e o Google te devolveu resultado genérico, o problema era você não ter usado aspas.

### Sinal de menos `-`

Exclui termo ou site do resultado.

```plain
python -cobra
```

Isso ali tira as cobras da sua busca sobre a linguagem de programação. Útil pra quando uma palavra tem dois significados e o Google insiste em te mostrar o errado.

### `OR` ou `|`

Busca por uma coisa OU outra.

```plain
linux OR "sistema operacional"
```

### `AND`

Busca resultado que tenha os dois termos. Na prática o Google já assume isso por padrão quando você digita várias palavras, mas às vezes ajuda deixar explícito, principalmente combinado com outros operadores.

### Parênteses `( )`

Agrupa termos e operadores pra montar busca mais complexa, tipo uma expressão lógica de programação.

```plain
(ubuntu OR debian) site:reddit.com
```

### Asterisco `*`

Curinga. Substitui uma palavra ou frase desconhecida.

```plain
"o * é o mapa de montagem do sistema"
```

Bom pra quando você lembra de parte de uma frase, música ou citação, mas esqueceu um pedaço.

### Dois pontos `..`

Busca resultado dentro de um intervalo numérico.

```plain
notebook R$2000..R$4000
```

### `site:`

Restringe a busca a um site ou domínio específico.

```plain
site:github.com dotfiles arch linux
```

Esse aqui é provavelmente o operador mais usado do planeta, e com razão.

### `filetype:` ou `ext:`

Filtra por tipo de arquivo.

```plain
filetype:pdf "manual arch linux"
```

### `intitle:`

A palavra-chave precisa aparecer no título da página.

```plain
intitle:"configurar fstab"
```

### `allintitle:`

Igual o `intitle:`, só que todas as palavras precisam estar no título, não só uma.

### `inurl:`

A palavra-chave precisa estar na URL.

```plain
inurl:tutorial linux
```

### `allinurl:`

Todas as palavras precisam estar na URL.

### `intext:`

A palavra-chave precisa aparecer no corpo do texto da página.

### `allintext:`

Todas as palavras precisam aparecer no corpo do texto.

### `before:` e `after:`

Filtra resultado indexado antes ou depois de uma data.

```plain
kernel linux after:2025-01-01
```

### `define:`

Busca a definição de uma palavra ou expressão.

### `AROUND(X)`

Busca palavras que estejam a uma distância de X palavras uma da outra.

```plain
linux AROUND(3) windows
```

***

## Dicas importantes na hora de usar

Algumas coisas que fazem diferença de verdade:

**Sem espaço entre o operador e o termo.** `site:exemplo.com` funciona, `site: exemplo.com` não. Parece bobo, mas é o erro mais comum. É tipo esquecer um ponto e vírgula no código: pequeno, mas quebra tudo.

**`OR` e `AROUND(X)` em maiúsculo.** O resto geralmente não faz diferença de caixa, mas esses dois exigem.

**Combine vários operadores na mesma busca.** Você pode empilhar quantos quiser:

```plain
site:github.com filetype:md intitle:dotfiles -windows
```

Isso busca arquivo `.md` dentro do GitHub, com "dotfiles" no título, excluindo qualquer coisa que mencione Windows.



**_## Um exemplo prático

Bora supor que você quer achar um tutorial de fstab, em PDF, publicado esse ano, sem incluir resultado do Windows:

```plain
intitle:fstab filetype:pdf after:2026-01-01 -windows
```

Só isso já elimina 90% do ruído que você teria numa busca comum. É a diferença entre pescar com rede e pescar com arpão.
