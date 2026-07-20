---
title: Como Identificar Tris e N-Gons no Blender
slug: identify-tris-and-n-gons-in-blender
description: Como isolar e visualizar todos os tris ou n-gons da sua malha
summary: Como isolar e visualizar todos os tris ou n-gons da sua malha
cover: null
tags:
  - blender
categories:
  - blender
keywords: []
author: Gabriel Maggioni
date: 2026-07-20T08:48:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

Manter uma topologia limpa é fundamental para quem trabalha com modelagem 3D, especialmente se o objetivo é exportar para jogos, aplicar subdivision surface ou preparar a malha para animação. Triângulos (tris) e faces com mais de quatro lados (n-gons) podem causar problemas de shading, deformação e desempenho.

Abaixo estão três formas de localizar e visualizar esses tipos de face no Blender — do método pontual à visualização em tempo real.

---

## 1. Selecionar Faces por Número de Lados (o mais direto)

Este é o método mais rápido para isolar e visualizar todos os tris ou n-gons da sua malha.

1. Entre no **Edit Mode** (Modo de Edição) pressionando `Tab`.
2. Mude para o **Face Selection Mode** (Modo de Seleção de Faces) — clique no ícone de quadrado no canto superior esquerdo da viewport ou pressione `3`.
3. No menu superior da viewport 3D, vá em **Select** (Selecionar) > **Select Faces by Sides** (Selecionar Faces por Lados).
4. Uma pequena janela de operador vai aparecer no canto inferior esquerdo. Nela, você define o que quer selecionar:

   **Para selecionar triângulos (tris):**
   - Em **Number of Vertices** (Número de Vértices), digite `3`.
   - Em **Type** (Tipo), escolha **Equal** (Igual).

   **Para selecionar n-gons (faces com mais de 4 lados):**
   - Em **Number of Vertices** (Número de Vértices), digite `4`.
   - Em **Type** (Tipo), escolha **Greater Than** (Maior Que).

Pronto! Todas as faces do tipo escolhido ficarão selecionadas, facilitando a visualização e a correção.

---

## 2. Usar a Sobreposição de Análise de Malha (visualização em tempo real)

Esta ferramenta funciona como um "raio-X" da sua malha, colorindo cada face de acordo com seu tipo em tempo real.

1. Entre no **Edit Mode** (Modo de Edição) pressionando `Tab`.
2. Certifique-se de que o viewport está no modo de sombreamento **Solid** (Sólido).
3. No canto superior direito da viewport, clique no ícone de seta para baixo ao lado do ícone de **Show Overlays** (Mostrar Sobreposições) — o ícone com dois círculos concêntricos.
4. No menu que se abre, ative a opção **Mesh Analysis** (Análise de Malha).
5. Com a opção ativada, novas configurações aparecerão logo abaixo. Você pode escolher o que deseja visualizar:
   - Selecione **Triangles** (Triângulos), **Quads** (Quads) ou **N-Gons** para que cada um seja destacado com uma cor diferente.

Essa é uma ótima maneira de monitorar a topologia enquanto você modela.

---

## 3. Ver as Estatísticas da Malha (informação geral)

Se você precisa apenas de uma contagem total, e não de identificar a localização de cada face:

1. No canto inferior direito da viewport 3D, clique no pequeno botão com o ícone de **Scene Statistics** (Estatísticas da Cena) — que parece um gráfico de barras.
2. Um menu suspenso aparecerá. Ative a opção **Scene Statistics** (Estatísticas da Cena).
3. Agora, no canto superior esquerdo da viewport, você verá informações como `Verts: X | Edges: Y | Faces: Z | Tris: W`. A contagem de triângulos (`Tris`) já considera a triangulação de todas as faces.
4. Para uma visão mais detalhada (como a contagem separada de quads e n-gons), você pode abrir o painel lateral pressionando `N` e, na aba **Item**, procurar por informações sobre a malha selecionada.

---

## Qual método usar?

- **Correção pontual:** use a seleção por número de lados (método 1).
- **Modelagem contínua:** deixe a Mesh Analysis ativada (método 2) para acompanhar a topologia em tempo real.
- **Checagem rápida:** consulte as estatísticas da cena (método 3) quando só precisar de números gerais.
