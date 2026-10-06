---
title: Conheça o ARDUINO
slug: arduino-who
description: ''
summary: ''
cover: null
tags:
  - arduíno
  - arduino
categories: []
keywords: []
author: Gabriel Maggioni
date: 2026-09-28T11:51:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

Arduino é uma plataforma de prototipagem eletrônica criada para facilitar o desenvolvimento de projetos com sensores, LEDs, motores, displays, botões e vários outros componentes eletrônicos.

Quando falamos em **Arduino**, normalmente estamos falando de duas coisas:

- A placa física, como o **Arduino Uno**
- O ambiente de programação, chamado **Arduino IDE**

O Arduino Uno, por exemplo, possui um microcontrolador que executa o programa que você envia para ele.

Diferente de um computador comum, o Arduino não possui um sistema operacional completo como Windows ou Linux. Ele executa diretamente o código gravado no microcontrolador.

---

# Para que serve?

O Arduino pode ser usado para controlar praticamente qualquer projeto eletrônico simples ou intermediário.

Alguns exemplos:

- Acender LEDs
- Ler botões
- Medir temperatura
- Medir luminosidade
- Detectar movimento
- Detectar distância
- Controlar motores
- Controlar servos
- Acionar relés
- Mostrar informações em displays
- Fazer alarmes
- Automatizar iluminação
- Criar robôs
- Criar fechaduras eletrônicas
- Criar sistemas de irrigação
- Criar estações meteorológicas
- Fazer protótipos de produtos

O Arduino funciona como o **cérebro do circuito**.

Os sensores enviam informações para ele, o programa analisa essas informações e então o Arduino pode tomar alguma ação.

Por exemplo:

```text
Sensor de distância
        ↓
     Arduino
        ↓
Objeto está perto?
        ↓
       Sim
        ↓
Acender LED vermelho
```

---

# Como o Arduino funciona?

Dentro do Arduino existe um **microcontrolador**.

No Arduino Uno tradicional, normalmente encontramos o microcontrolador:

```text
ATmega328P
```

Esse pequeno chip possui:

- Processador
- Memória
- Entradas digitais
- Saídas digitais
- Entradas analógicas
- Temporizadores
- Interfaces de comunicação

Você escreve um programa no computador e envia esse programa para o Arduino através do cabo USB.

Depois disso, o Arduino consegue executar o programa sozinho sempre que receber energia.

O fluxo básico é:

```text
Computador
   ↓
Arduino IDE
   ↓
Código C/C++
   ↓
Compilação
   ↓
USB
   ↓
Arduino
   ↓
Microcontrolador executa o programa
```

---

# Arduino IDE

A Arduino IDE é o programa utilizado para escrever e enviar códigos para a placa.

Um programa básico de Arduino é chamado de **sketch**.

Um sketch normalmente possui duas funções principais:

```cpp
void setup() {

}

void loop() {

}
```

---

# `setup()`

A função `setup()` executa apenas **uma vez**, quando o Arduino liga ou reinicia.

Ela normalmente é usada para configurar as portas.

Exemplo:

```cpp
void setup() {
  pinMode(13, OUTPUT);
}
```

Aqui estamos dizendo:

> A porta 13 será utilizada como saída.

---

# `loop()`

A função `loop()` executa repetidamente enquanto o Arduino estiver ligado.

Exemplo:

```cpp
void loop() {
  digitalWrite(13, HIGH);
  delay(1000);

  digitalWrite(13, LOW);
  delay(1000);
}
```

Esse código:

1. Liga a porta 13
2. Espera 1 segundo
3. Desliga a porta 13
4. Espera 1 segundo
5. Repete tudo

Se houver um LED conectado corretamente nessa porta, ele ficará piscando.

---

# As portas do Arduino

O Arduino possui vários tipos de pinos.

No Arduino Uno, os principais são:

```text
Portas digitais
Portas PWM
Entradas analógicas
5V
3.3V
GND
VIN
AREF
RESET
```

Cada uma possui uma função diferente.

---

# Portas digitais

No Arduino Uno existem as portas:

```text
0 até 13
```

Elas são chamadas de portas digitais porque trabalham principalmente com dois estados:

```text
LOW
HIGH
```

Normalmente:

```text
LOW  = 0V
HIGH = aproximadamente 5V
```

Essas portas podem funcionar como:

```text
Entrada
```

ou

```text
Saída
```

---

# Porta digital como saída

Uma porta configurada como saída pode controlar dispositivos.

Exemplo:

```cpp
pinMode(7, OUTPUT);
```

Para ligar:

```cpp
digitalWrite(7, HIGH);
```

Para desligar:

```cpp
digitalWrite(7, LOW);
```

Isso pode ser usado para controlar:

- LEDs
- Buzzers
- Transistores
- MOSFETs
- Relés
- Outros circuitos

---

# Porta digital como entrada

Também podemos utilizar uma porta para receber informações.

Exemplo:

```cpp
pinMode(2, INPUT);
```

Depois podemos ler o estado dela:

```cpp
int estado = digitalRead(2);
```

O resultado normalmente será:

```text
HIGH
```

ou:

```text
LOW
```

Isso é útil para:

- Botões
- Sensores digitais
- Interruptores
- Sensores de movimento
- Módulos eletrônicos

---

# INPUT_PULLUP

O Arduino também possui resistores internos que podem ser usados nas entradas.

Por exemplo:

```cpp
pinMode(2, INPUT_PULLUP);
```

Nesse modo, o Arduino ativa internamente um resistor de pull-up.

Isso é muito utilizado com botões.

Um circuito simples pode ficar:

```text
Pino 2 ---- botão ---- GND
```

Quando o botão não estiver pressionado:

```text
HIGH
```

Quando o botão for pressionado:

```text
LOW
```

---

# O que é uma entrada flutuante?

Se você configurar:

```cpp
pinMode(2, INPUT);
```

e não conectar corretamente o pino a HIGH ou LOW, ele pode ficar em um estado chamado **floating**, ou flutuante.

Nesse estado, o Arduino pode ler valores aleatórios.

Por exemplo:

```text
HIGH
LOW
HIGH
HIGH
LOW
```

Mesmo sem você pressionar nada.

Por isso normalmente utilizamos:

- Resistor pull-up
- Resistor pull-down
- `INPUT_PULLUP`

---

# Portas PWM

Algumas portas digitais possuem o símbolo:

```text
~
```

No Arduino Uno normalmente são:

```text
3
5
6
9
10
11
```

Essas portas possuem suporte a **PWM**.

PWM significa:

```text
Pulse Width Modulation
```

ou:

```text
Modulação por Largura de Pulso
```

PWM permite simular diferentes níveis de potência ligando e desligando uma saída muito rapidamente.

Por exemplo:

```cpp
analogWrite(9, 128);
```

O valor pode variar entre:

```text
0 até 255
```

Onde:

```text
0   = 0%
128 = aproximadamente 50%
255 = 100%
```

Isso pode ser usado para:

- Controlar brilho de LED
- Controlar velocidade de motores
- Gerar sinais
- Controlar alguns dispositivos eletrônicos

---

# Entradas analógicas

No Arduino Uno existem normalmente:

```text
A0
A1
A2
A3
A4
A5
```

Essas portas permitem medir uma tensão variável.

Por exemplo:

```cpp
int valor = analogRead(A0);
```

No Arduino Uno, o resultado normalmente varia entre:

```text
0 até 1023
```

Porque o conversor analógico possui resolução de 10 bits.

Isso significa aproximadamente:

```text
0    = 0V
1023 = 5V
```

Essas entradas podem ser usadas com:

- Potenciômetros
- LDR
- Sensores analógicos
- Joysticks
- Sensores de temperatura
- Sensores de luminosidade

---

# Exemplo com potenciômetro

Imagine um potenciômetro conectado assim:

```text
5V --------+
           |
      Potenciômetro
           |
GND -------+
           |
          A0
```

Podemos ler sua posição:

```cpp
int valor = analogRead(A0);

Serial.println(valor);
```

O Arduino retornará aproximadamente:

```text
0
100
350
512
800
1023
```

dependendo da posição do potenciômetro.

---

# Pinos de alimentação

Além das portas de entrada e saída, o Arduino possui pinos de alimentação.

Os principais são:

```text
5V
3.3V
GND
VIN
```

---

# 5V

O pino:

```text
5V
```

fornece aproximadamente 5 volts.

Pode ser usado para alimentar pequenos componentes e sensores.

Por exemplo:

```text
Arduino 5V
   |
   +---- Sensor
```

Porém o Arduino possui limite de corrente.

Não devemos utilizar esse pino para alimentar dispositivos que consomem muita corrente.

Por exemplo:

- Motores grandes
- Vários servos
- Fitas LED grandes
- Relés grandes diretamente
- Bombas

Esses dispositivos normalmente precisam de uma fonte externa.

---

# 3.3V

O pino:

```text
3.3V
```

fornece aproximadamente:

```text
3,3 volts
```

Alguns sensores e módulos eletrônicos trabalham nessa tensão.

Sempre verifique a tensão suportada pelo componente.

---

# GND

GND significa:

```text
Ground
```

ou:

```text
Terra/referência elétrica do circuito
```

Ele funciona como o ponto de referência para as tensões do circuito.

Normalmente um circuito precisa de:

```text
Alimentação
+
GND
```

Exemplo:

```text
5V ---- LED ---- resistor ---- GND
```

Quando usamos fontes externas junto com o Arduino, normalmente precisamos conectar os GNDs.

Exemplo:

```text
Fonte externa GND
        |
        +------ Arduino GND
```

Isso cria uma referência elétrica comum entre os dispositivos.

---

# VIN

O pino:

```text
VIN
```

pode ser usado para alimentar a placa através do regulador interno em situações apropriadas.

Ele não é equivalente ao pino de 5V.

É importante verificar a tensão recomendada para a placa específica antes de utilizá-lo.

---

# RESET

O pino ou botão:

```text
RESET
```

reinicia o microcontrolador.

Quando você pressiona RESET, o Arduino começa novamente pelo:

```cpp
setup()
```

e depois continua executando:

```cpp
loop()
```

---

# AREF

AREF significa:

```text
Analog Reference
```

Ele está relacionado à tensão de referência utilizada pelas entradas analógicas.

Em projetos básicos normalmente você não precisará utilizá-lo.

---

# Portas 0 e 1

No Arduino Uno:

```text
0 = RX
1 = TX
```

Esses pinos são utilizados pela comunicação serial.

```text
RX = Receive
TX = Transmit
```

Eles também são utilizados na comunicação entre o Arduino e o computador através do conversor USB/Serial da placa.

Por isso é recomendado tomar cuidado ao conectar outros dispositivos nesses pinos enquanto estiver enviando programas para o Arduino.

---

# Comunicação Serial

O Arduino pode enviar informações para o computador.

No código:

```cpp
void setup() {
  Serial.begin(9600);
}
```

Depois:

```cpp
Serial.println("Olá!");
```

Na Arduino IDE podemos abrir o **Monitor Serial**.

O resultado aparecerá:

```text
Olá!
```

Isso é extremamente útil para:

- Testar sensores
- Encontrar erros
- Ver valores
- Depurar programas
- Acompanhar estados do circuito

---

# A4 e A5 também possuem outra função

No Arduino Uno, normalmente:

```text
A4 = SDA
A5 = SCL
```

Esses pinos são utilizados pelo protocolo:

```text
I2C
```

O I2C permite conectar dispositivos como:

- Displays OLED
- Displays LCD
- RTCs
- Sensores
- Expansores de portas

Um exemplo de ligação:

```text
Arduino      Display OLED

5V/VCC  ---> VCC
GND     ---> GND
A4      ---> SDA
A5      ---> SCL
```

Dependendo do módulo, a tensão pode ser 3,3 V em vez de 5 V.

---

# SPI

O Arduino também possui comunicação:

```text
SPI
```

No Arduino Uno ela utiliza normalmente os pinos:

```text
10
11
12
13
```

Com funções como:

```text
SS
MOSI
MISO
SCK
```

O SPI pode ser utilizado com:

- Cartões SD
- Displays
- Módulos RFID
- Conversores
- Memórias
- Outros microcontroladores

---

# Como conectar um LED

Um dos circuitos mais simples é:

```text
Arduino porta 7
      |
    resistor
      |
     LED
      |
     GND
```

Um resistor como:

```text
220 Ω
```

é frequentemente usado com LEDs comuns, dependendo da tensão, LED e corrente desejada.

Código:

```cpp
void setup() {
  pinMode(7, OUTPUT);
}

void loop() {
  digitalWrite(7, HIGH);
  delay(1000);

  digitalWrite(7, LOW);
  delay(1000);
}
```

---

# Por que usar resistor no LED?

Um LED não deve normalmente ser conectado diretamente entre uma porta digital e o GND sem limitação de corrente.

O resistor limita a corrente.

Sem ele, você pode danificar:

- O LED
- A porta do microcontrolador

Exemplo:

```text
Arduino
  |
  +---- resistor ---- LED ---- GND
```

---

# Como conectar um botão

Um exemplo utilizando `INPUT_PULLUP`:

```text
Pino 2 ---- botão ---- GND
```

Código:

```cpp
void setup() {
  pinMode(2, INPUT_PULLUP);
}

void loop() {

  if (digitalRead(2) == LOW) {
    // botão pressionado
  }

}
```

---

# Botão controlando LED

Exemplo completo:

```cpp
#define LED 7
#define BOTAO 2

void setup() {
  pinMode(LED, OUTPUT);
  pinMode(BOTAO, INPUT_PULLUP);
}

void loop() {

  if (digitalRead(BOTAO) == LOW) {
    digitalWrite(LED, HIGH);
  } else {
    digitalWrite(LED, LOW);
  }

}
```

Quando o botão for pressionado:

```text
LED ligado
```

Quando soltar:

```text
LED desligado
```

---

# `pinMode()`

A função:

```cpp
pinMode()
```

define como um pino será utilizado.

Exemplos:

```cpp
pinMode(7, OUTPUT);
```

```cpp
pinMode(2, INPUT);
```

```cpp
pinMode(2, INPUT_PULLUP);
```

---

# `digitalWrite()`

Controla uma saída digital.

```cpp
digitalWrite(7, HIGH);
```

liga a saída.

```cpp
digitalWrite(7, LOW);
```

desliga.

---

# `digitalRead()`

Lê uma entrada digital.

```cpp
int estado = digitalRead(2);
```

O resultado será:

```text
HIGH
```

ou:

```text
LOW
```

---

# `analogRead()`

Lê uma entrada analógica.

```cpp
int valor = analogRead(A0);
```

No Arduino Uno normalmente retorna:

```text
0 até 1023
```

---

# `analogWrite()`

No Arduino Uno, `analogWrite()` utiliza PWM nos pinos compatíveis.

Exemplo:

```cpp
analogWrite(9, 128);
```

Isso gera aproximadamente 50% de duty cycle.

---

# `delay()`

A função:

```cpp
delay()
```

faz o programa esperar uma quantidade de milissegundos.

Exemplo:

```cpp
delay(1000);
```

significa:

```text
1000 ms = 1 segundo
```

---

# Exemplo de projeto

Imagine um sensor de distância HC-SR04.

O Arduino pode medir a distância de um objeto.

Quando a distância for:

```text
maior que 50 cm
```

acende LED verde.

Entre:

```text
20 e 50 cm
```

acende LED amarelo.

Abaixo de:

```text
20 cm
```

acende LED vermelho.

O funcionamento seria:

```text
HC-SR04
   |
   v
Arduino
   |
   v
Calcula distância
   |
   +---- longe ------> LED verde
   |
   +---- médio ------> LED amarelo
   |
   +---- perto ------> LED vermelho
```

Esse mesmo princípio pode ser utilizado em projetos muito maiores.

---

# Arduino não fornece potência infinita

Uma coisa muito importante ao começar com Arduino é entender que suas portas foram feitas principalmente para **sinais**, não para fornecer muita potência.

Você não deve ligar diretamente em uma porta:

```text
Motor grande
Servo potente
Lâmpada
Fita LED grande
Bomba
Solenoide
```

Para controlar cargas maiores normalmente utilizamos:

- Transistor
- MOSFET
- Relé
- Driver de motor

Por exemplo:

```text
Arduino
   |
   | sinal
   v
 MOSFET
   |
   v
 Motor
   |
Fonte externa
```

O Arduino apenas controla o MOSFET.

Quem fornece a energia para o motor é a fonte externa.

---

# Arduino e protoboard

Durante os testes é muito comum utilizar uma:

```text
Protoboard
```

Ela permite montar circuitos sem precisar soldar.

Você pode conectar:

- Resistores
- LEDs
- Botões
- Sensores
- Transistores
- Jumpers
- Circuitos integrados

Depois que o projeto estiver funcionando, você pode criar algo mais permanente utilizando:

- Placa perfurada
- Soldagem
- PCB personalizada

---

# Como começar a usar

O processo básico é:

## 1. Instalar a Arduino IDE

Instale a IDE compatível com seu sistema operacional.

---

## 2. Conectar o Arduino

Utilize um cabo USB.

```text
PC ---- USB ---- Arduino
```

---

## 3. Selecionar a placa

Na Arduino IDE selecione a placa correspondente.

Por exemplo:

```text
Arduino Uno
```

---

## 4. Selecionar a porta

Selecione a porta serial onde o Arduino apareceu.

No Windows ela pode aparecer como:

```text
COM3
COM4
COM5
```

No Linux normalmente aparece como algo parecido com:

```text
/dev/ttyACM0
```

ou:

```text
/dev/ttyUSB0
```

---

# Permissão da porta no Linux

Em distribuições Linux, pode acontecer de aparecer um erro como:

```text
Permission denied
```

ao tentar acessar:

```text
/dev/ttyUSB0
```

ou:

```text
/dev/ttyACM0
```

Uma solução comum é adicionar seu usuário ao grupo responsável pelas portas seriais.

Dependendo da distribuição, frequentemente é:

```bash
sudo usermod -aG dialout $USER
```

Em algumas distribuições, como sistemas baseados em Arch Linux, o grupo utilizado pode ser:

```bash
uucp
```

Por exemplo:

```bash
sudo usermod -aG uucp $USER
```

Depois disso normalmente é necessário sair da sessão e entrar novamente para que a alteração seja aplicada.

---

# 5. Escrever o código

Por exemplo:

```cpp
void setup() {
  pinMode(13, OUTPUT);
}

void loop() {
  digitalWrite(13, HIGH);
  delay(1000);

  digitalWrite(13, LOW);
  delay(1000);
}
```

---

# 6. Compilar

A IDE transforma seu código em instruções que o microcontrolador consegue executar.

Se houver algum erro de programação, ele aparecerá durante a compilação.

---

# 7. Enviar para a placa

Clique em:

```text
Upload
```

A IDE enviará o programa pela USB.

Depois disso, o Arduino começará a executar o programa automaticamente.

---

# O Arduino precisa ficar conectado ao computador?

Não.

O computador normalmente é necessário apenas para:

```text
Programar
Depurar
Monitorar
```

Depois que o código foi gravado, você pode alimentar o Arduino utilizando uma fonte apropriada.

O código continuará armazenado no microcontrolador mesmo quando ele for desligado.

Quando receber energia novamente, ele executará o programa novamente.

---

# Estrutura básica de um projeto Arduino

Normalmente podemos pensar em três partes:

```text
Entrada
Processamento
Saída
```

Por exemplo:

```text
Sensor PIR
   |
   v
ENTRADA
   |
   v
Arduino
   |
   v
PROCESSAMENTO
   |
   v
LED / Buzzer
   |
   v
SAÍDA
```

Outro exemplo:

```text
Sensor de temperatura
        |
        v
     Arduino
        |
        v
 Temperatura > 30°C?
        |
       Sim
        |
        v
   Liga ventilador
```

---

# Resumo das principais portas do Arduino Uno

| Pino | Função |
|---|---|
| 0 | Digital / RX Serial |
| 1 | Digital / TX Serial |
| 2 | Digital |
| 3 | Digital / PWM |
| 4 | Digital |
| 5 | Digital / PWM |
| 6 | Digital / PWM |
| 7 | Digital |
| 8 | Digital |
| 9 | Digital / PWM |
| 10 | Digital / PWM / SPI |
| 11 | Digital / PWM / SPI |
| 12 | Digital / SPI |
| 13 | Digital / SPI / LED integrado |
| A0 | Entrada analógica |
| A1 | Entrada analógica |
| A2 | Entrada analógica |
| A3 | Entrada analógica |
| A4 | Analógica / SDA I2C |
| A5 | Analógica / SCL I2C |
| 5V | Alimentação 5 V |
| 3.3V | Alimentação 3,3 V |
| GND | Referência elétrica |
| VIN | Entrada de alimentação |
| RESET | Reinicia o microcontrolador |
| AREF | Referência analógica |

---

# Conclusão

Arduino é uma das maneiras mais simples de começar a aprender eletrônica e sistemas embarcados.

Com uma única placa você consegue aprender conceitos como:

- Tensão
- Corrente
- Resistência
- Entradas digitais
- Saídas digitais
- Entradas analógicas
- PWM
- Sensores
- Motores
- Comunicação serial
- I2C
- SPI
- Automação
- Programação de microcontroladores

O conceito principal é simples:

```text
Receber informações
        ↓
Processar informações
        ↓
Controlar alguma coisa
```

Por exemplo:

```text
Sensor
   ↓
Arduino
   ↓
Programa
   ↓
LED / Motor / Display / Relé
```

A partir desse princípio é possível criar desde um simples LED piscando até robôs, sistemas de automação e dispositivos eletrônicos completos.
