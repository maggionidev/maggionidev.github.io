---
title: 'Volts, Ampères e Watts: entendendo eletricidade e fontes de PC'
slug: volts-ampere-watts-dc-ac
description: ''
summary: ''
cover: null
tags: []
categories:
  - eletrônica
keywords: []
author: Gabriel Maggioni
date: 2026-09-27T17:24:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

Quando começamos a estudar eletrônica, alguns termos aparecem o tempo inteiro: **Volt, Ampère, Watt, corrente contínua e corrente alternada**.

Eles parecem complicados no começo, mas os conceitos básicos são relativamente simples.

---

## O que é Volt?

O **Volt (V)** representa a **tensão elétrica**.

Uma forma simples de imaginar isso é pensar em água dentro de um encanamento.

A tensão seria equivalente à **pressão da água**.

Quanto maior a tensão, maior é a "força" disponível para empurrar cargas elétricas pelo circuito.

### Exemplos comuns

- USB: **5 V**
- ESP32: normalmente **3,3 V**
- Arduino Uno: normalmente **5 V**
- Fonte de PC: possui linhas de **3,3 V, 5 V e 12 V**
- Tomada residencial no Brasil: normalmente **127 V ou 220 V**

> É importante respeitar a tensão suportada pelo equipamento. Aplicar uma tensão muito maior do que a especificada pode destruir um componente.

---

## O que é Ampère?

O **Ampère (A)** representa a **corrente elétrica**.

Continuando a comparação com água:

- **Volts** representam a pressão.
- **Ampères** representam a quantidade de água passando pelo cano.

Também é muito comum encontrarmos valores em **miliampères (mA)**.

```text
1 A = 1000 mA
````

Portanto:

```plain
100 mA  = 0,1 A
500 mA  = 0,5 A
2000 mA = 2 A
```

Uma fonte capaz de fornecer **5 A** não força obrigatoriamente 5 A no equipamento.

Se um circuito precisa apenas de **500 mA**, ele normalmente consumirá aproximadamente esses 500 mA.

Por isso, uma fonte de:

```plain
5 V / 10 A
```

pode alimentar um equipamento que precisa de:

```plain
5 V / 1 A
```

desde que a **tensão seja correta**.

A capacidade maior de corrente apenas significa que a fonte pode fornecer mais corrente caso seja necessário.

***

## O que é Watt?

O **Watt (W)** representa a **potência elétrica**.

Em circuitos simples de corrente contínua, podemos calcular a potência multiplicando a tensão pela corrente:

```plain
W = V × A
```

Por exemplo:

```plain
5 V × 2 A = 10 W
```

Portanto, um dispositivo operando em **5 V** e consumindo **2 A** utiliza aproximadamente **10 watts**.

Outro exemplo:

```plain
12 V × 5 A = 60 W
```

Temos então uma potência de **60 W**.

### Forma fácil de lembrar

| Unidade | Significado |
| --- | --- |
| Volt (V) | Tensão |
| Ampère (A) | Corrente |
| Watt (W) | Potência |

***

# Corrente Contínua e Corrente Alternada

Outra diferença fundamental na eletrônica é entre **corrente contínua (DC)** e **corrente alternada (AC)**.

As siglas vêm do inglês:

- **DC** = Direct Current
- **AC** = Alternating Current

Em português:

- **DC** = Corrente Contínua
- **AC** = Corrente Alternada

***

## O que é Corrente Contínua?

Na **corrente contínua**, a polaridade permanece definida e a corrente circula normalmente em uma única direção.

É o tipo de alimentação encontrado em:

- pilhas;
- baterias;
- power banks;
- Arduino;
- ESP32;
- USB;
- fontes de bancada;
- saída de fontes de computador.

Uma bateria pode possuir, por exemplo:

```plain
+9 V
GND
```

Existe, portanto, um terminal positivo e um terminal negativo.

Grande parte da eletrônica digital funciona com corrente contínua.

***

## O que é Corrente Alternada?

Na **corrente alternada**, a tensão muda de polaridade periodicamente.

É o tipo de eletricidade fornecido pelas tomadas residenciais.

No Brasil, a rede elétrica utiliza normalmente uma frequência de:

```plain
60 Hz
```

Isso significa que a forma de onda elétrica completa **60 ciclos por segundo**.

Diferente de uma bateria, a tensão da tomada não possui simplesmente um positivo fixo e um negativo fixo.

Em uma rede AC residencial encontramos normalmente condutores como:

- **fase**;
- **neutro**;
- **terra**.

Esses termos não devem ser confundidos diretamente com o positivo e negativo de uma bateria.

***

## O que significa Hz?

**Hertz (Hz)** é uma unidade de frequência.

Ela representa quantas vezes um fenômeno periódico acontece por segundo.

Por exemplo:

```plain
1 Hz    = 1 ciclo por segundo
60 Hz   = 60 ciclos por segundo
1000 Hz = 1000 ciclos por segundo
```

A rede elétrica brasileira opera normalmente em **60 Hz**.

A frequência também aparece em muitas outras áreas da eletrônica, como:

- sinais de áudio;
- PWM;
- rádios;
- Wi-Fi;
- processadores;
- comunicação entre dispositivos.

***

## AC e DC possuem símbolos diferentes

Em equipamentos eletrônicos normalmente encontramos símbolos indicando o tipo de corrente.

### Corrente contínua

Costuma aparecer como:

```plain
DC ⎓
```

### Corrente alternada

Costuma aparecer como:

```plain
AC ~
```

Por isso, um multímetro possui modos diferentes para medir tensão AC e DC.

Por exemplo:

```plain
V⎓ = tensão contínua
V~ = tensão alternada
```

Selecionar o modo correto é importante para obter uma medição correta.

***

# O que uma fonte de alimentação faz?

Uma fonte de alimentação geralmente recebe a corrente alternada da tomada e transforma essa energia em corrente contínua adequada para equipamentos eletrônicos.

Uma fonte de PC, por exemplo, recebe:

```plain
127 V AC
```

ou:

```plain
220 V AC
```

e converte essa energia para tensões contínuas como:

```plain
12 V DC
5 V DC
3,3 V DC
```

É por isso que os componentes do computador não recebem diretamente os **127 V ou 220 V** da tomada.

A fonte realiza a conversão.

***

## O carregador do celular também faz isso

Um carregador USB funciona seguindo uma ideia semelhante.

Ele recebe, por exemplo:

```plain
127/220 V AC
```

da tomada e fornece algo como:

```plain
5 V DC
```

para o celular.

Carregadores modernos podem fornecer outras tensões usando protocolos de carregamento rápido, como:

- **5 V**
- **9 V**
- **12 V**
- **15 V**
- **20 V**

Isso depende do carregador e do dispositivo conectado.

***

# O que é polaridade?

Em circuitos de corrente contínua existe normalmente uma polaridade definida.

Temos:

```plain
Positivo (+)
Negativo (-)
```

Ou, em muitos circuitos:

```plain
VCC
GND
```

Alguns componentes não funcionam corretamente se forem conectados ao contrário.

Um LED, por exemplo, possui polaridade.

Normalmente temos:

```plain
Ânodo   = positivo
Cátodo  = negativo
```

Baterias também possuem polaridade.

Por isso, é importante observar o positivo e negativo antes de conectar componentes.

***

# GND significa negativo?

**Não necessariamente.**

GND significa **Ground**, ou terra/referência elétrica do circuito.

Em muitos circuitos simples alimentados por bateria, o GND é conectado ao terminal negativo da fonte.

Por isso, na prática, frequentemente vemos algo como:

```plain
+5 V
GND
```

Mas conceitualmente GND representa principalmente o **ponto de referência de tensão do circuito**.

Quando dizemos:

```plain
5 V
```

normalmente queremos dizer:

```plain
5 V em relação ao GND
```

***

# Então o que significa uma fonte de PC de 650 W?

Quando uma fonte de computador possui potência nominal de **650 W**, significa que ela foi projetada para fornecer até aproximadamente **650 watts de potência total**, respeitando os limites especificados pelo fabricante.

Isso **não significa** que o computador consome 650 W o tempo inteiro.

Se o computador estiver usando apenas:

```plain
200 W
```

a fonte fornecerá aproximadamente a potência necessária naquele momento.

Os **650 W** representam a capacidade máxima nominal da fonte, e não um consumo constante.

***

# As tensões de uma fonte ATX

Uma fonte ATX possui várias linhas de tensão.

As principais são:

- **+12 V**
- **+5 V**
- **+3,3 V**
- **5VSB**
- **-12 V**

***

## Linha +12 V

Atualmente é a linha mais importante de uma fonte de PC.

Ela alimenta principalmente componentes de maior potência, como:

- processador;
- placa de vídeo;
- ventoinhas;
- bombas de water cooler;
- motores de HDs.

Imagine uma fonte cuja linha de 12 V suporte até 50 A:

```plain
12 V × 50 A = 600 W
```

Isso representa até aproximadamente **600 W disponíveis nessa linha**, respeitando os limites definidos pelo fabricante.

***

## Linha +5 V

A linha de 5 V pode alimentar:

- portas USB;
- SSDs;
- HDDs;
- partes da placa-mãe;
- alguns periféricos.

É também uma tensão extremamente comum em projetos eletrônicos.

***

## Linha +3,3 V

A linha de 3,3 V é utilizada principalmente por circuitos digitais.

Pode aparecer em:

- chips;
- memória;
- controladores;
- circuitos da placa-mãe.

O **ESP32** também trabalha internamente principalmente com lógica de **3,3 V**.

***

## Linha 5VSB

Existe também a linha **5VSB**, ou **5 Volts Standby**.

Ela permanece disponível mesmo quando o computador está desligado pelo botão frontal, desde que a fonte continue conectada à rede elétrica e ligada.

Essa linha permite funções como:

- detectar o botão de ligar;
- determinados recursos de espera;
- funções de standby da placa-mãe.

***

## Linha -12 V

Fontes ATX também possuem uma pequena linha de **-12 V**.

Essa tensão era mais importante em computadores antigos e atualmente possui uso bastante limitado.

***

# Posso somar todas as linhas da fonte?

Não necessariamente.

Uma fonte pode indicar algo como:

```plain
+12 V: 50 A
+5 V:  20 A
+3,3 V: 20 A
```

Mas isso não significa que podemos simplesmente calcular a potência máxima de cada linha e somar tudo.

A fonte possui **limites combinados de potência**.

Esses limites aparecem na etiqueta da própria fonte.

Por isso, ao analisar uma fonte ATX, é importante observar não apenas os ampères de cada linha, mas também a potência máxima combinada indicada pelo fabricante.

***

# O que é resistência?

Outro conceito fundamental é a **resistência elétrica**.

Ela representa a oposição à passagem da corrente elétrica.

Sua unidade é o **Ohm**, representado pelo símbolo:

```plain
Ω
```

Resistores são usados justamente para controlar correntes e tensões dentro dos circuitos.

Um exemplo muito comum é o LED.

Não devemos normalmente conectar um LED diretamente em uma fonte de alimentação porque uma corrente muito alta pode passar por ele.

Por isso utilizamos um **resistor para limitar essa corrente**.

***

# Lei de Ohm

Tensão, corrente e resistência estão relacionadas pela famosa **Lei de Ohm**:

```plain
V = R × I
```

Onde:

```plain
V = tensão em volts
R = resistência em ohms
I = corrente em ampères
```

Também podemos reorganizar a fórmula:

```plain
I = V / R
```

ou:

```plain
R = V / I
```

### Exemplo

Se tivermos:

```plain
V = 5 V
R = 1000 Ω
```

Podemos calcular:

```plain
I = V / R

I = 5 / 1000

I = 0,005 A
```

Convertendo para miliampères:

```plain
0,005 A = 5 mA
```

Portanto:

```plain
I = 5 mA
```

A Lei de Ohm é uma das fórmulas mais importantes para quem está começando em eletrônica.

***

# E a eficiência da fonte?

Nenhuma fonte transforma 100% da energia retirada da tomada em energia útil.

Parte da energia é perdida principalmente na forma de **calor**.

Imagine um computador que esteja recebendo **300 W** da fonte e que ela esteja operando com eficiência de **90%**.

A energia retirada da tomada seria aproximadamente:

```plain
300 W ÷ 0,90 ≈ 333 W
```

Nesse exemplo:

```plain
Energia útil:      300 W
Energia da tomada: 333 W
Perdas:             33 W
```

Cerca de **33 W seriam perdidos durante a conversão**.

É por isso que eficiência é uma característica importante em fontes de computador.

Certificações como **80 Plus** existem justamente para classificar determinados níveis de eficiência.

***

# Energia e potência não são a mesma coisa

Também é comum confundir **Watt (W)** com **Watt-hora (Wh)**.

- **Watt (W)** representa potência.
- **Watt-hora (Wh)** representa uma quantidade de energia.

Por exemplo, um equipamento consumindo:

```plain
100 W
```

durante:

```plain
2 horas
```

consumiria:

```plain
100 W × 2 h = 200 Wh
```

Ou:

```plain
0,2 kWh
```

É justamente o **kWh** que aparece na conta de energia elétrica.

***

# Resumo rápido

Uma forma simples de decorar:

| Unidade | Significado |
| --- | --- |
| **Volt (V)** | Tensão elétrica |
| **Ampère (A)** | Corrente elétrica |
| **Watt (W)** | Potência |
| **Ohm (Ω)** | Resistência |
| **Hertz (Hz)** | Frequência |
| **AC** | Corrente alternada |
| **DC** | Corrente contínua |

## Principais fórmulas

### Potência elétrica

```plain
W = V × A
```

### Lei de Ohm

```plain
V = R × I
```

```plain
I = V / R
```

```plain
R = V / I
```

## Exemplos

```plain
5 V × 2 A = 10 W
```

```plain
12 V × 5 A = 60 W
```

```plain
12 V × 50 A = 600 W
```

***

# Conclusão

Entender esses conceitos básicos já facilita bastante o estudo de:

- Arduino;
- ESP32;
- fontes de bancada;
- fontes ATX;
- baterias;
- sensores;
- motores;
- LEDs;
- circuitos eletrônicos em geral.

A partir daqui, conceitos como **resistores, transistores, MOSFETs, capacitores, PWM e reguladores de tensão** começam a fazer muito mais sentido.
