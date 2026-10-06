---
title: Como usar um multímetro digital
slug: multimeter-hikari-hm1550
description: Conheça o multímetro Hikari HM 1550
summary: ''
cover:
  image: https://assets.maggioni.dev/posts/eletronica/hikarihm1550.jpeg
tags: []
categories: []
keywords: []
author: Gabriel Maggioni
date: 2026-10-04T10:55:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

O multímetro é uma das ferramentas mais importantes para quem trabalha ou estuda eletrônica. Com ele é possível medir tensão, corrente, resistência, testar continuidade, diodos, capacitores, transistores e muito mais.

Neste artigo vamos conhecer o **Hikari HM-1550**, um multímetro digital True RMS de 4000 contagens, bastante completo para projetos com Arduino, ESP32 e eletrônica em geral.

> **Aviso:** algumas funções envolvem tensões perigosas. Se você ainda está aprendendo eletrônica, pratique primeiro com circuitos de baixa tensão, como 3,3 V, 5 V, 9 V ou 12 V.

***

# Conhecendo o Hikari HM-1550

O HM-1550 possui:

- medição de tensão DC;
- medição de tensão AC;
- True RMS;
- corrente em µA, mA e A;
- resistência;
- continuidade sonora;
- teste de diodo;
- capacitância;
- frequência;
- duty cycle;
- teste de baterias;
- teste hFE de transistores;
- detecção de tensão sem contato;
- identificação de fase;
- função HOLD;
- valores mínimo e máximo;
- iluminação do display;
- lanterna;
- desligamento automático;
- seleção automática de escala.

Ele também possui classificação de segurança **CAT III 600 V**.

***

# As três entradas do multímetro

Na parte inferior do HM-1550 existem três conectores.

## COM

A entrada central é a `COM`, abreviação de _Common_.

A ponta preta fica praticamente sempre nela.

```text
Ponta preta → COM
```

***

## VΩmA

A entrada da direita é utilizada para a maior parte das medições:

- tensão;
- resistência;
- continuidade;
- diodo;
- capacitância;
- frequência;
- µA;
- mA;
- teste de baterias;
- outras funções do aparelho.

Na maior parte do tempo teremos:

```plain
Ponta preta    → COM
Ponta vermelha → VΩmA
```

A entrada de corrente em µA/mA possui proteção por fusível.

***

## 10A

A entrada da esquerda é destinada exclusivamente à medição de correntes mais altas.

```plain
Ponta preta    → COM
Ponta vermelha → 10A
```

Ela não deve ser utilizada para medir tensão, resistência ou continuidade.

Uma boa prática é sempre devolver a ponta vermelha para a entrada `VΩmA` depois de terminar uma medição de corrente alta.

Isso reduz o risco de esquecer a ponta no borne de 10 A e posteriormente tentar medir tensão.

***

# Tensão e corrente: paralelo ou série?

Essa é uma das coisas mais importantes para aprender antes de utilizar um multímetro.

## Tensão é medida em paralelo

Imagine este circuito:

```plain
5V ─── LED ─── resistor ─── GND
```

Se quisermos descobrir a tensão sobre o LED, colocamos uma ponta de cada lado dele:

```plain
       🔴      ⚫
        │      │
5V ─── LED ─── resistor ─── GND
        │      │
        └── V ─┘
```

O circuito não precisa ser interrompido.

O multímetro está simplesmente comparando a tensão existente entre dois pontos.

Por isso podemos pensar:

> Tensão = observar o circuito.

***

## Corrente é medida em série

Para medir corrente precisamos fazer a corrente atravessar o multímetro.

Circuito original:

```plain
5V ─── LED ─── resistor ─── GND
```

Para medir a corrente:

```plain
5V ─── multímetro ─── LED ─── resistor ─── GND
```

Agora toda a corrente que alimenta o LED passa primeiro pelo multímetro.

Podemos pensar:

> Corrente = atravessar o multímetro.

***

## Nunca meça corrente em paralelo

Nunca faça isto com o aparelho configurado para mA ou A:

```plain
5V ───── 🔴
          │
      multímetro
          │
GND ───── ⚫
```

O amperímetro possui resistência interna muito baixa.

Essa ligação cria praticamente um curto-circuito entre a alimentação e o GND.

O resultado pode ser:

- fusível do multímetro queimado;
- fonte entrando em proteção;
- trilhas ou fios aquecendo;
- danos ao circuito;
- em situações de alta energia, risco de acidente.

Portanto:

```plain
Tensão   → paralelo
Corrente → série
```

***

# Funções da chave seletora

## OFF

Desliga o aparelho.

O HM-1550 também possui desligamento automático, útil caso você esqueça o multímetro ligado.

***

# V⎓ - tensão contínua

Essa será provavelmente uma das funções mais utilizadas em projetos de eletrônica.

Serve para medir fontes e sinais DC, como:

- 3,3 V do ESP32;
- 5 V do Arduino;
- baterias;
- pilhas;
- power banks;
- fontes de bancada;
- módulos eletrônicos.

Ligação:

```plain
Preta    → COM
Vermelha → VΩmA
```

Exemplo para conferir a saída de 5 V do Arduino:

```plain
Vermelha → 5V
Preta    → GND
```

O display deverá mostrar algo próximo de:

```plain
5.00 V
```

Se as pontas forem invertidas, normalmente aparecerá:

```plain
-5.00 V
```

Isso simplesmente indica que a polaridade está invertida.

***

# V\~ - tensão alternada

Serve para medir tensão AC.

É utilizada, por exemplo, para:

- rede elétrica;
- transformadores AC;
- sinais alternados.

Na rede elétrica, a medição é feita colocando o multímetro em paralelo com os dois pontos que queremos comparar.

```plain
Preta    → COM
Vermelha → VΩmA
Seletor  → V~
```

> Trabalhar diretamente com a rede elétrica pode causar choque grave ou fatal. Para aprender, prefira circuitos de baixa tensão.

***

# LIVE / NCV

O HM-1550 também possui funções para detectar presença de tensão AC.

## NCV

`NCV` significa _Non-Contact Voltage_.

É uma detecção de tensão **sem contato elétrico direto**.

Ao aproximar o aparelho de um cabo energizado, ele pode detectar o campo elétrico ao redor do condutor.

É útil para verificações rápidas.

Porém:

> O NCV não deve ser utilizado como única forma de confirmar que um circuito está realmente desligado.

A ausência de detecção não garante ausência de tensão.

***

## LIVE

A função LIVE permite verificar a presença de fase utilizando a ponta de prova.

Ela é diferente do NCV porque existe contato com o condutor.

***

# Hz / %

Essa posição permite medir frequência e duty cycle.

## Hz

`Hz` significa Hertz.

Um Hertz corresponde a um ciclo por segundo.

Exemplos:

```plain
1 Hz     = 1 ciclo por segundo
100 Hz   = 100 ciclos por segundo
1 kHz    = 1000 ciclos por segundo
```

É muito útil para analisar:

- PWM;
- osciladores;
- sinais digitais;
- sensores;
- circuitos eletrônicos.

***

# Duty Cycle %

O duty cycle indica quanto tempo um sinal permanece ativo durante cada ciclo.

Por exemplo, um PWM de 50%:

```plain
ON  █████-----
ON  █████-----
ON  █████-----
```

Significa que o sinal fica aproximadamente:

```plain
50% ligado
50% desligado
```

Isso aparece bastante em Arduino e ESP32.

***

# Ω - resistência

O símbolo `Ω` representa Ohm, unidade utilizada para medir resistência elétrica.

Essa função pode ser utilizada para testar:

- resistores;
- potenciômetros;
- LDR;
- fios;
- componentes resistivos.

Exemplos:

```plain
330 Ω
1 kΩ
10 kΩ
100 kΩ
1 MΩ
```

## Importante

Resistência deve ser medida com o circuito **desligado**.

Nunca tente medir resistência de um componente enquanto ele estiver sendo alimentado.

***

# Continuidade

A função de continuidade verifica se existe um caminho elétrico entre dois pontos.

Quando a resistência entre eles é suficientemente baixa, o multímetro emite um sinal sonoro.

Exemplo:

```plain
ponta ─────── fio ─────── ponta

BEEEEEP
```

É extremamente útil para testar:

- fios;
- jumpers;
- fusíveis;
- trilhas de PCB;
- soldas;
- interruptores;
- conectores.

Também ajuda bastante a encontrar fios rompidos e conexões ruins.

A continuidade deve ser testada com o circuito desligado.

***

# Teste de diodo

O modo de diodo mede aproximadamente a queda de tensão existente em uma junção semicondutora.

Pode ser utilizado para testar:

- diodos;
- LEDs;
- junções de transistores.

Um diodo de silício comum pode apresentar algo próximo de:

```plain
0,5 V
0,6 V
0,7 V
```

Invertendo as pontas, normalmente teremos:

```plain
OL
```

Isso indica que a corrente não está passando naquela direção.

***

# Testando LEDs

Um LED também é um diodo.

Dependendo do LED e da tensão fornecida pelo multímetro, ele pode inclusive acender levemente durante o teste.

A queda de tensão depende da cor e da tecnologia utilizada.

***

# Capacitância

O HM-1550 também consegue medir capacitores.

As unidades mais comuns são:

```plain
pF
nF
µF
mF
```

Por exemplo, um capacitor marcado como:

```plain
100 µF
```

pode apresentar algo como:

```plain
98 µF
102 µF
105 µF
```

dependendo da tolerância do componente.

## Antes de medir

Sempre descarregue o capacitor antes de conectá-lo ao multímetro.

Capacitores grandes podem armazenar energia mesmo depois que o equipamento foi desligado.

***

# BAT - teste de baterias

O multímetro possui uma posição dedicada para verificar algumas baterias e pilhas comuns.

Ela possui opções para:

- 1,5 V;
- 3 V;
- 9 V.

Pode ser utilizada para pilhas e baterias compatíveis com essas faixas.

Para outros tipos de bateria, como uma célula Li-ion de 3,7 V, normalmente é melhor utilizar a medição convencional `V⎓`.

***

# hFE - teste de transistor

O HM-1550 possui uma função para testar transistores BJT.

`hFE` representa aproximadamente o ganho de corrente do transistor.

A relação básica é:

```plain
hFE ≈ Ic / Ib
```

Onde:

```plain
Ic = corrente do coletor
Ib = corrente da base
```

Se o aparelho mostrar:

```plain
0265
```

isso significa aproximadamente:

```plain
hFE = 265
```

O valor não deve ser tratado como perfeitamente fixo.

O ganho de um transistor pode variar bastante entre unidades do mesmo modelo e também de acordo com corrente, temperatura e condições de operação.

Portanto, hFE é útil para testes e comparação, mas um projeto eletrônico não deve depender de um valor exato de ganho.

***

# µA - microampères

Um microampère corresponde a:

```plain
1 µA = 0,000001 A
```

ou:

```plain
1000 µA = 1 mA
```

Essa escala é utilizada para correntes muito pequenas.

Exemplos:

- circuitos de baixo consumo;
- sensores;
- corrente de repouso;
- pequenos sinais.

A medição é feita em **série**.

***

# mA - miliampères

Um miliampère corresponde a:

```plain
1000 mA = 1 A
```

Essa faixa é muito útil em eletrônica.

Um LED, por exemplo, pode consumir:

```plain
5 mA
10 mA
20 mA
```

dependendo do circuito.

Para medir:

```plain
5V ─── multímetro ─── LED ─── resistor ─── GND
```

O multímetro precisa estar no caminho da corrente.

***

# 10A

A posição de 10 A é utilizada para correntes maiores.

Aqui a ponta vermelha deve ser movida para a entrada:

```plain
10A
```

Então teremos:

```plain
Preta    → COM
Vermelha → 10A
```

Essa entrada possui um circuito interno diferente daquele utilizado para µA e mA.

A faixa de 10 A também não é destinada a medições prolongadas em corrente máxima.

***

# Botão SEL

`SEL` significa _Select_.

Ele alterna entre diferentes funções que compartilham a mesma posição da chave seletora.

Dependendo da posição, pode alternar entre funções como:

- resistência;
- continuidade;
- diodo;
- capacitância;
- frequência;
- duty cycle;
- corrente AC e DC;
- NCV e LIVE.

***

# HOLD

O botão `H` permite congelar a leitura no display.

Imagine que você esteja medindo:

```plain
5.03 V
```

Pressione HOLD e a tela continuará mostrando:

```plain
5.03 V
```

mesmo depois de retirar as pontas.

Isso é útil quando é difícil olhar simultaneamente para o circuito e para o display.

***

# MIN/MAX

Essa função registra os valores mínimos e máximos encontrados durante uma medição.

Imagine uma alimentação variando:

```plain
4,90 V
5,01 V
5,10 V
4,95 V
```

O multímetro pode registrar:

```plain
MIN = 4,90 V
MAX = 5,10 V
```

Isso é útil para descobrir oscilações que poderiam passar despercebidas.

***

# REL

`REL` significa medição relativa.

Ela permite utilizar um valor atual como referência.

Isso pode ser útil, por exemplo, para compensar pequenas capacitâncias introduzidas pelas próprias pontas de prova.

***

# O que é True RMS?

Na frente do HM-1550 está escrito:

```plain
TRUE RMS
```

RMS significa:

```plain
Root Mean Square
```

Em português, podemos chamar de **valor eficaz**.

Quando dizemos que uma tomada possui aproximadamente:

```plain
220 V AC
```

esses 220 V representam o valor RMS da tensão.

Uma senoide de 220 V RMS possui um pico aproximadamente igual a:

```plain
311 V
```

O RMS representa uma tensão equivalente em termos de potência dissipada em uma carga resistiva.

***

# Por que True RMS é importante?

Multímetros AC simples podem assumir que o sinal medido é uma senoide perfeita.

Isso pode gerar erros quando o sinal possui outra forma.

O True RMS permite obter uma leitura mais adequada em sinais não senoidais, dentro dos limites de frequência e especificações do aparelho.

Pode ser útil em situações envolvendo:

- ondas distorcidas;
- controles de potência;
- inversores;
- determinados sinais PWM;
- circuitos eletrônicos com formas de onda não senoidais.

True RMS não significa que qualquer sinal poderá ser medido perfeitamente.

O multímetro continua possuindo limites de frequência, tensão e largura de banda.

***

# O que significa 4000 Counts?

Na frente do aparelho também encontramos:

```plain
4000 Counts
```

`Counts` está relacionado à resolução do instrumento.

Um aparelho de 4000 contagens consegue representar aproximadamente valores de:

```plain
0 até 3999
```

dentro de uma faixa antes de precisar trocar para outra.

Por exemplo:

```plain
3,999 V
```

Ao ultrapassar essa faixa, o multímetro pode mudar automaticamente para uma escala maior.

***

# 4000 Counts não significa 4000 medições por segundo

Esse é um erro fácil de cometer.

`Counts` está relacionado à resolução das faixas do instrumento.

Não representa a velocidade de atualização do display.

***

# Auto Range

O HM-1550 possui seleção automática de escala.

Em multímetros manuais é comum encontrarmos posições como:

```plain
2 V
20 V
200 V
600 V
```

O usuário precisa escolher a faixa correta.

No HM-1550 basta selecionar:

```plain
V⎓
```

e o aparelho escolhe automaticamente uma faixa apropriada.

Isso torna o uso muito mais simples.

***

# O que significa CAT III 600 V?

Na parte inferior do aparelho encontramos:

```plain
CAT III 600V
```

Isso é uma classificação de segurança.

Ela está relacionada à capacidade do instrumento de suportar transientes e sobretensões encontrados em determinados ambientes elétricos.

De forma simplificada:

```plain
CAT I   → circuitos eletrônicos protegidos

CAT II  → equipamentos ligados diretamente a tomadas

CAT III → instalações elétricas fixas e distribuição

CAT IV  → entrada da instalação e rede externa
```

Portanto:

```plain
CAT III 600 V
```

não significa simplesmente:

> "Posso medir qualquer coisa até 600 V."

A categoria considera também os picos transitórios que podem aparecer naquele tipo de instalação.

***

# O símbolo de dois quadrados

No aparelho também existe um símbolo parecido com:

```plain
▣
```

Um quadrado dentro de outro representa **dupla isolação**.

Isso significa que o equipamento possui uma construção de isolamento projetada para aumentar a proteção do usuário.

***

# Fusíveis de 500 mA e 10 A

Na parte inferior do multímetro aparecem indicações relacionadas aos fusíveis internos.

A entrada de corrente baixa possui proteção própria, enquanto a entrada de 10 A possui outro fusível.

Esses fusíveis ajudam a proteger o multímetro caso seja cometida uma ligação incorreta ou ocorra uma corrente excessiva.

Porém, o fusível não deve ser utilizado como justificativa para fazer medições arriscadas.

Ele é uma proteção adicional, não um substituto para o uso correto.

***

# O que significa OL?

Durante alguns testes o display pode mostrar:

```plain
OL
```

O significado depende da função utilizada.

Pode indicar:

- circuito aberto;
- resistência muito alta;
- diodo bloqueando;
- valor acima da faixa disponível.

Por exemplo, no modo continuidade, com as pontas separadas:

```plain
OL
```

é perfeitamente normal.

No teste de um diodo:

```plain
Vermelha → ânodo
Preta    → cátodo

0,6 V
```

Invertendo:

```plain
Vermelha → cátodo
Preta    → ânodo

OL
```

também pode ser completamente normal.

***

# Quando posso medir com o circuito ligado?

Uma regra simples para iniciantes:

## Pode estar ligado

Para medir:

```plain
Tensão
Corrente
Frequência
Duty cycle
```

Respeitando os limites do multímetro e fazendo a ligação correta.

***

## Deve estar desligado

Para medir:

```plain
Resistência
Continuidade
Diodo
Capacitância
```

Essas funções utilizam o próprio multímetro para aplicar um pequeno sinal ao componente.

Aplicar tensão externa durante esses testes pode gerar leituras erradas ou até danificar o instrumento.

***

# Exemplos práticos

## Conferir os 5 V do Arduino

Configure:

```plain
V⎓
```

Depois:

```plain
Vermelha → 5V
Preta    → GND
```

Você deverá obter algo próximo de:

```plain
5 V
```

***

# Conferir os 3,3 V do ESP32

```plain
Vermelha → 3V3
Preta    → GND
```

Resultado esperado:

```plain
≈ 3,3 V
```

***

# Medir a corrente de um LED

Circuito normal:

```plain
5V ─── LED ─── resistor ─── GND
```

Abra o circuito:

```plain
5V ─── multímetro ─── LED ─── resistor ─── GND
```

Se aparecer:

```plain
5 mA
```

significa que aproximadamente 5 miliampères estão atravessando todo aquele circuito.

Como os componentes estão em série, a mesma corrente passa pelo:

```plain
multímetro
LED
resistor
```

***

# Testar um resistor

Desligue o circuito ou retire pelo menos um dos terminais do resistor quando necessário.

Configure o multímetro para:

```plain
Ω
```

Coloque uma ponta em cada lado do resistor.

Exemplo:

```plain
330.2 Ω
```

Para um resistor nominal de:

```plain
330 Ω
```

a leitura está dentro do esperado.

***

# Testar um LDR

Um LDR é um resistor cuja resistência varia com a quantidade de luz.

Você pode encontrar algo como:

```plain
Luz forte → 1,8 kΩ
Sombra    → 22 kΩ
```

Quanto maior a iluminação, normalmente menor será a resistência do LDR.

Isso permite utilizá-lo em divisores de tensão para detectar luminosidade.

***

# Testar um potenciômetro

Um potenciômetro possui três terminais.

Medindo entre as duas extremidades encontramos aproximadamente sua resistência total.

Por exemplo:

```plain
10 kΩ
```

Medindo entre o terminal central e uma das extremidades, a resistência muda conforme giramos o eixo:

```plain
0 Ω
2 kΩ
5 kΩ
8 kΩ
10 kΩ
```

***

# Testar um fusível

Configure para continuidade.

Coloque uma ponta em cada extremidade.

Se o multímetro apitar:

```plain
BEEP
```

o fusível provavelmente possui continuidade.

Se mostrar circuito aberto:

```plain
OL
```

o fusível provavelmente está rompido.

***

# Uma regra que evita muitos acidentes

Antes de encostar as pontas em qualquer circuito, faça estas três perguntas:

```plain
1. O que eu quero medir?

2. A chave seletora está na função correta?

3. A ponta vermelha está no conector correto?
```

Principalmente antes de medir tensão.

Se a ponta vermelha estiver na entrada `10A` ou a chave estiver configurada para corrente, não coloque as pontas diretamente entre positivo e negativo de uma fonte.

***

# Resumo rápido

| Função | Para que serve | Circuito ligado? |
| --- | --- | --- |
| `V⎓` | Tensão DC | Sim |
| `V~` | Tensão AC | Sim |
| `Ω` | Resistência | Não |
| Continuidade | Verificar conexão | Não |
| Diodo | LEDs e diodos | Não |
| Capacitância | Capacitores | Não |
| `µA` | Correntes muito pequenas | Sim |
| `mA` | Corrente baixa | Sim |
| `10A` | Corrente alta | Sim |
| `Hz` | Frequência | Sim |
| `%` | Duty cycle | Sim |
| `hFE` | Ganho de transistor BJT | Transistor fora do circuito |

A regra mais importante:

```plain
TENSÃO   → MULTÍMETRO EM PARALELO

CORRENTE → MULTÍMETRO EM SÉRIE

RESISTÊNCIA / CONTINUIDADE / DIODO / CAPACITÂNCIA
→ CIRCUITO DESLIGADO
```

***

# Conclusão

O Hikari HM-1550 oferece praticamente todas as funções necessárias para começar a estudar eletrônica, diagnosticar circuitos e trabalhar em projetos com Arduino e ESP32.

Mais importante do que decorar todas as posições do seletor é entender **o que o multímetro está fazendo em cada medição**.

Quando medimos tensão, estamos comparando dois pontos.

Quando medimos corrente, fazemos a corrente passar através do instrumento.

Quando medimos resistência, continuidade, diodos ou capacitores, o próprio multímetro aplica um pequeno sinal ao componente, por isso o circuito deve estar desligado.

Entendendo esses princípios, o multímetro deixa de ser apenas um aparelho cheio de símbolos e passa a ser uma das ferramentas mais úteis da bancada.
