---
title: "Calculadora Universal de Eletrônica"
slug: "universal-eletronic-calculator"
description: "Calculadora de eletrônica online para Lei de Ohm, queda de tensão, resistores, PWM, bateria, transistor BJT, LEDs, conversões elétricas e dimensionamento de fonte."
summary: "Uma calculadora prática para projetos de eletrônica, com ferramentas para tensão, corrente, resistência, potência, LEDs, baterias, PWM, transistores e fontes."
cover: null
tags:
  - eletronica
  - calculadora
  - lei-de-ohm
  - resistores
  - arduino
  - esp32
categories:
  - eletronica
keywords:
  - calculadora de eletrônica
  - calculadora lei de ohm
  - calcular resistência
  - calcular corrente
  - calcular tensão
  - calcular potência
  - resistor para led
  - resistores em série
  - resistores em paralelo
  - calculadora pwm
  - autonomia de bateria
  - transistor bjt
  - dimensionar fonte
author: Gabriel Maggioni
date: 2026-10-05T11:43:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

# Calculadora de Eletrônica

Esta calculadora reúne algumas das fórmulas e cálculos mais úteis para projetos de eletrônica em um único lugar.

Ela pode ser usada para calcular **tensão, corrente, resistência e potência**, além de ajudar no dimensionamento de componentes e na análise de circuitos simples.

Entre as ferramentas disponíveis estão:

- Lei de Ohm e potência elétrica
- análise de queda de tensão
- resistores em série e paralelo
- frequência e duty cycle de PWM
- autonomia de baterias
- cálculo para transistores BJT
- LEDs em série e resistor limitador
- conversões entre unidades elétricas
- dimensionamento básico de fontes de alimentação

A ideia é servir como uma ferramenta rápida de bancada para projetos com **Arduino, ESP32, sensores, LEDs, transistores e outros circuitos eletrônicos**.

> Os resultados são cálculos teóricos. Em um circuito real, tolerância dos componentes, temperatura, perdas e características específicas dos dispositivos podem alterar os valores medidos.

## Calculadora

{{< rawhtml >}}
<div id="calculadora-eletronica">

<style>
#calculadora-eletronica {
    width: 100%;
    color: #eeeeee;
    font-family: Arial, sans-serif;
}

#calculadora-eletronica * {
    box-sizing: border-box;
}

#calculadora-eletronica .calc-main {
    width: 100%;
}

#calculadora-eletronica details {
    background: #171a21;
    border: 1px solid #2a2e38;
    border-radius: 14px;
    margin-bottom: 12px;
    overflow: hidden;
}

#calculadora-eletronica .calc-main {
    background: transparent;
    border: 0;
    margin: 0;
}

#calculadora-eletronica summary {
    cursor: pointer;
    user-select: none;
    list-style: none;

    padding: 16px 18px;

    font-size: 17px;
    font-weight: bold;

    background: #171a21;
    color: #eeeeee;

    border-radius: 14px;
}

#calculadora-eletronica summary::-webkit-details-marker {
    display: none;
}

#calculadora-eletronica summary::after {
    content: "＋";
    float: right;

    color: #8fa0ff;

    font-size: 20px;
    line-height: 1;
}

#calculadora-eletronica details[open] > summary::after {
    content: "−";
}

#calculadora-eletronica .calc-main > summary {
    font-size: 20px;

    border: 1px solid #2a2e38;
}

#calculadora-eletronica .calc-main[open] > summary {
    margin-bottom: 15px;
}

#calculadora-eletronica .calc-content {
    padding: 0 18px 18px;
}

#calculadora-eletronica .calc-description {
    color: #999;

    margin-top: 0;
    margin-bottom: 18px;

    font-size: 14px;
}

#calculadora-eletronica .calc-grid {
    display: grid;

    grid-template-columns:
        repeat(auto-fit, minmax(280px, 1fr));

    gap: 12px;
}

#calculadora-eletronica .calc-input-grid {
    display: grid;

    grid-template-columns:
        repeat(auto-fit, minmax(170px, 1fr));

    gap: 12px;
}

#calculadora-eletronica label {
    display: block;

    margin-bottom: 13px;

    color: #eeeeee;

    font-size: 14px;
}

#calculadora-eletronica input,
#calculadora-eletronica textarea {
    width: 100%;

    margin-top: 6px;

    padding: 10px;

    background: #101217;
    color: #ffffff;

    border: 1px solid #343a46;
    border-radius: 8px;

    font-family: inherit;
    font-size: 16px;
}

#calculadora-eletronica textarea {
    min-height: 110px;
    resize: vertical;
}

#calculadora-eletronica input:focus,
#calculadora-eletronica textarea:focus {
    outline: 2px solid #5865f2;
    border-color: transparent;
}

#calculadora-eletronica button {
    padding: 10px 16px;

    border: 0;
    border-radius: 8px;

    background: #5865f2;
    color: #ffffff;

    font-family: inherit;
    font-size: 14px;
    font-weight: bold;

    cursor: pointer;
}

#calculadora-eletronica button:hover {
    opacity: 0.9;
}

#calculadora-eletronica button.calc-secondary {
    background: #292e38;
}

#calculadora-eletronica .calc-buttons {
    display: flex;

    gap: 8px;

    flex-wrap: wrap;

    margin-top: 8px;
}

#calculadora-eletronica .calc-result {
    margin-top: 14px;

    padding: 12px;

    min-height: 44px;

    background: #101217;

    border-radius: 8px;

    color: #eeeeee;

    line-height: 1.8;
}

#calculadora-eletronica .calc-error {
    color: #ff7373;
}

#calculadora-eletronica .calc-warning {
    color: #f7c76f;
}

#calculadora-eletronica strong {
    color: #8fa0ff;
}

#calculadora-eletronica .calc-small {
    color: #999;
    font-size: 13px;
}

#calculadora-eletronica hr {
    border: 0;

    border-top:
        1px solid #2a2e38;

    margin: 15px 0;
}

#calculadora-eletronica .calc-conversion-section {
    margin-top: 15px;
}

#calculadora-eletronica .calc-subtitle {
    margin-top: 5px;
    margin-bottom: 12px;

    color: #eeeeee;

    font-size: 16px;
}

@media (max-width: 600px) {

    #calculadora-eletronica .calc-content {
        padding:
            0 14px 14px;
    }

    #calculadora-eletronica summary {
        padding:
            14px 15px;
    }

    #calculadora-eletronica .calc-input-grid {
        grid-template-columns: 1fr;
    }

    #calculadora-eletronica .calc-grid {
        grid-template-columns: 1fr;
    }

}
</style>


<details class="calc-main">

    <summary>
        EXPANDIR CALCULADORA
    </summary>

    <div class="calc-main-content">


        <!-- ============================== -->
        <!-- CALCULADORA UNIVERSAL -->
        <!-- ============================== -->

        <details>

            <summary>
                Calculadora Elétrica Universal
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Preencha exatamente dois valores entre
                    tensão, corrente, resistência e potência.
                </p>


                <div class="calc-input-grid">

                    <label>
                        Tensão (V)

                        <input
                            id="universal-v"
                            type="number"
                            step="any"
                            placeholder="Ex.: 5"
                        >
                    </label>


                    <label>
                        Corrente (A)

                        <input
                            id="universal-i"
                            type="number"
                            step="any"
                            placeholder="Ex.: 0.0005"
                        >
                    </label>


                    <label>
                        Resistência (Ω)

                        <input
                            id="universal-r"
                            type="number"
                            step="any"
                            placeholder="Ex.: 10000"
                        >
                    </label>


                    <label>
                        Potência (W)

                        <input
                            id="universal-p"
                            type="number"
                            step="any"
                            placeholder="Ex.: 0.0025"
                        >
                    </label>

                </div>


                <div class="calc-buttons">

                    <button id="universal-calc">
                        Calcular
                    </button>

                    <button
                        id="universal-clear"
                        class="calc-secondary"
                    >
                        Limpar
                    </button>

                </div>


                <div
                    id="universal-result"
                    class="calc-result"
                >
                    Preencha dois valores.
                </div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- QUEDA -->
        <!-- ============================== -->

        <details>

            <summary>
                Análise de queda de tensão
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Informe a tensão antes e depois.
                    Resistência e corrente são opcionais.
                </p>


                <div class="calc-input-grid">

                    <label>
                        Tensão antes (V)

                        <input
                            id="drop-before"
                            type="number"
                            step="any"
                            placeholder="Ex.: 10"
                        >
                    </label>


                    <label>
                        Tensão depois (V)

                        <input
                            id="drop-after"
                            type="number"
                            step="any"
                            placeholder="Ex.: 1"
                        >
                    </label>


                    <label>
                        Resistência (Ω)
                        <span class="calc-small">
                            opcional
                        </span>

                        <input
                            id="drop-r"
                            type="number"
                            step="any"
                            placeholder="Ex.: 10000"
                        >
                    </label>


                    <label>
                        Corrente (A)
                        <span class="calc-small">
                            opcional
                        </span>

                        <input
                            id="drop-i"
                            type="number"
                            step="any"
                            placeholder="Ex.: 0.0009"
                        >
                    </label>

                </div>


                <button id="drop-calc">
                    Analisar
                </button>


                <div
                    id="drop-result"
                    class="calc-result"
                >
                    Informe pelo menos as tensões.
                </div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- RESISTORES -->
        <!-- ============================== -->

        <details>

            <summary>
                Resistores em série e paralelo
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Digite os valores separados
                    por vírgula ou espaço.
                </p>


                <label>
                    Resistores (Ω)

                    <input
                        id="resistors-list"
                        type="text"
                        placeholder="Ex.: 100, 220, 1000"
                    >
                </label>


                <button id="resistors-calc">
                    Calcular
                </button>


                <div
                    id="resistors-result"
                    class="calc-result"
                ></div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- PWM -->
        <!-- ============================== -->

        <details>

            <summary>
                Frequência PWM
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Calcule período e tempos HIGH e LOW.
                </p>


                <div class="calc-input-grid">

                    <label>
                        Frequência (Hz)

                        <input
                            id="pwm-frequency"
                            type="number"
                            step="any"
                            value="1000"
                        >
                    </label>


                    <label>
                        Duty cycle (%)

                        <input
                            id="pwm-duty"
                            type="number"
                            step="any"
                            value="50"
                        >
                    </label>

                </div>


                <button id="pwm-calc">
                    Calcular
                </button>


                <div
                    id="pwm-result"
                    class="calc-result"
                ></div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- BATERIA -->
        <!-- ============================== -->

        <details>

            <summary>
                Bateria e autonomia
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Estime autonomia e energia disponível.
                </p>


                <div class="calc-input-grid">

                    <label>
                        Tensão da bateria (V)

                        <input
                            id="battery-v"
                            type="number"
                            step="any"
                            value="3.7"
                        >
                    </label>


                    <label>
                        Capacidade (mAh)

                        <input
                            id="battery-capacity"
                            type="number"
                            step="any"
                            placeholder="Ex.: 2500"
                        >
                    </label>


                    <label>
                        Consumo médio (mA)

                        <input
                            id="battery-current"
                            type="number"
                            step="any"
                            placeholder="Ex.: 250"
                        >
                    </label>


                    <label>
                        Eficiência utilizável (%)

                        <input
                            id="battery-efficiency"
                            type="number"
                            step="any"
                            value="85"
                        >
                    </label>

                </div>


                <button id="battery-calc">
                    Calcular
                </button>


                <div
                    id="battery-result"
                    class="calc-result"
                ></div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- BJT -->
        <!-- ============================== -->

        <details>

            <summary>
                Transistor BJT
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Calcula corrente de base e resistor
                    de base para uso como chave.
                </p>


                <div class="calc-input-grid">

                    <label>
                        Tensão do GPIO (V)

                        <input
                            id="bjt-gpio"
                            type="number"
                            step="any"
                            value="5"
                        >
                    </label>


                    <label>
                        VBE (V)

                        <input
                            id="bjt-vbe"
                            type="number"
                            step="any"
                            value="0.7"
                        >
                    </label>


                    <label>
                        Corrente do coletor (mA)

                        <input
                            id="bjt-collector"
                            type="number"
                            step="any"
                            placeholder="Ex.: 100"
                        >
                    </label>


                    <label>
                        Ganho forçado

                        <input
                            id="bjt-beta"
                            type="number"
                            step="any"
                            value="10"
                        >
                    </label>


                    <label>
                        VCE(sat) (V)

                        <input
                            id="bjt-vce"
                            type="number"
                            step="any"
                            value="0.2"
                        >
                    </label>

                </div>


                <button id="bjt-calc">
                    Calcular
                </button>


                <div
                    id="bjt-result"
                    class="calc-result"
                ></div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- LED -->
        <!-- ============================== -->

        <details>

            <summary>
                LEDs em série
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Calcula resistor,
                    corrente e potência.
                </p>


                <div class="calc-input-grid">

                    <label>
                        Tensão da fonte (V)

                        <input
                            id="led-source"
                            type="number"
                            step="any"
                            value="5"
                        >
                    </label>


                    <label>
                        Queda de cada LED (V)

                        <input
                            id="led-vf"
                            type="number"
                            step="any"
                            value="2"
                        >
                    </label>


                    <label>
                        Quantidade

                        <input
                            id="led-count"
                            type="number"
                            step="1"
                            value="1"
                        >
                    </label>


                    <label>
                        Corrente desejada (mA)

                        <input
                            id="led-current"
                            type="number"
                            step="any"
                            value="10"
                        >
                    </label>

                </div>


                <button id="led-calc">
                    Calcular
                </button>


                <div
                    id="led-result"
                    class="calc-result"
                ></div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- FONTE -->
        <!-- ============================== -->

        <details>

            <summary>
                Dimensionamento de fonte
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Informe o consumo dos componentes em mA.
                </p>


                <label>
                    Tensão da fonte (V)

                    <input
                        id="psu-voltage"
                        type="number"
                        step="any"
                        value="5"
                    >
                </label>


                <label>
                    Componentes

                    <textarea
                        id="psu-components"
                        placeholder="ESP32: 250
Servo 1: 500
Servo 2: 500
OLED: 30
LEDs: 80"
                    ></textarea>
                </label>


                <label>
                    Margem de segurança (%)

                    <input
                        id="psu-margin"
                        type="number"
                        step="any"
                        value="30"
                    >
                </label>


                <button id="psu-calc">
                    Calcular fonte
                </button>


                <div
                    id="psu-result"
                    class="calc-result"
                ></div>

            </div>

        </details>



        <!-- ============================== -->
        <!-- CONVERSÕES -->
        <!-- ============================== -->

        <details>

            <summary>
                Conversões
            </summary>

            <div class="calc-content">

                <p class="calc-description">
                    Digite qualquer campo.
                </p>


                <h3 class="calc-subtitle">
                    Resistência
                </h3>

                <div class="calc-input-grid">

                    <label>
                        Ω

                        <input
                            id="conv-ohm"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        kΩ

                        <input
                            id="conv-kohm"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        MΩ

                        <input
                            id="conv-mohm"
                            type="number"
                            step="any"
                        >
                    </label>

                </div>


                <hr>


                <h3 class="calc-subtitle">
                    Tensão
                </h3>

                <div class="calc-input-grid">

                    <label>
                        V

                        <input
                            id="conv-v"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        mV

                        <input
                            id="conv-mv"
                            type="number"
                            step="any"
                        >
                    </label>

                </div>


                <hr>


                <h3 class="calc-subtitle">
                    Corrente
                </h3>

                <div class="calc-input-grid">

                    <label>
                        A

                        <input
                            id="conv-a"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        mA

                        <input
                            id="conv-ma"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        µA

                        <input
                            id="conv-ua"
                            type="number"
                            step="any"
                        >
                    </label>

                </div>


                <hr>


                <h3 class="calc-subtitle">
                    Potência
                </h3>

                <div class="calc-input-grid">

                    <label>
                        W

                        <input
                            id="conv-w"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        mW

                        <input
                            id="conv-mw"
                            type="number"
                            step="any"
                        >
                    </label>


                    <label>
                        µW

                        <input
                            id="conv-uw"
                            type="number"
                            step="any"
                        >
                    </label>

                </div>

            </div>

        </details>

    </div>

</details>



<script>
(function () {

    const root =
        document.getElementById(
            "calculadora-eletronica"
        );

    if (!root) {
        return;
    }


    function el(id) {

        return root.querySelector(
            "#" + id
        );

    }


    function getNumber(id) {

        const input =
            el(id);

        if (!input) {
            return null;
        }

        const value =
            input.value.trim();

        if (value === "") {
            return null;
        }

        const number =
            Number(value);

        if (!Number.isFinite(number)) {
            return null;
        }

        return number;

    }


    function formatNumber(number) {

        if (!Number.isFinite(number)) {
            return "—";
        }

        if (number === 0) {
            return "0";
        }

        const abs =
            Math.abs(number);

        if (
            abs >= 1000000 ||
            abs < 0.000001
        ) {

            return number.toExponential(4);

        }

        return Number(
            number.toPrecision(7)
        ).toString();

    }


    function formatCurrent(amps) {

        const abs =
            Math.abs(amps);

        if (abs >= 1) {

            return (
                formatNumber(amps) +
                " A"
            );

        }

        if (abs >= 0.001) {

            return (
                formatNumber(
                    amps * 1000
                ) +
                " mA"
            );

        }

        return (
            formatNumber(
                amps * 1000000
            ) +
            " µA"
        );

    }


    function formatResistance(ohms) {

        const abs =
            Math.abs(ohms);

        if (abs >= 1000000) {

            return (
                formatNumber(
                    ohms / 1000000
                ) +
                " MΩ"
            );

        }

        if (abs >= 1000) {

            return (
                formatNumber(
                    ohms / 1000
                ) +
                " kΩ"
            );

        }

        return (
            formatNumber(ohms) +
            " Ω"
        );

    }


    function formatPower(watts) {

        const abs =
            Math.abs(watts);

        if (abs >= 1) {

            return (
                formatNumber(watts) +
                " W"
            );

        }

        if (abs >= 0.001) {

            return (
                formatNumber(
                    watts * 1000
                ) +
                " mW"
            );

        }

        return (
            formatNumber(
                watts * 1000000
            ) +
            " µW"
        );

    }


    function error(message) {

        return (
            '<span class="calc-error">' +
            message +
            '</span>'
        );

    }


    /*
    =====================================
    UNIVERSAL
    =====================================
    */

    el("universal-calc")
    .addEventListener(
        "click",
        function () {

            let V =
                getNumber(
                    "universal-v"
                );

            let I =
                getNumber(
                    "universal-i"
                );

            let R =
                getNumber(
                    "universal-r"
                );

            let P =
                getNumber(
                    "universal-p"
                );


            const result =
                el(
                    "universal-result"
                );


            const filled =
                [V, I, R, P]
                .filter(
                    value =>
                        value !== null
                )
                .length;


            if (filled !== 2) {

                result.innerHTML =
                    error(
                        "Preencha exatamente dois valores."
                    );

                return;

            }


            if (
                V !== null &&
                I !== null
            ) {

                if (I === 0) {
                    result.innerHTML =
                        error(
                            "A corrente não pode ser zero."
                        );

                    return;
                }

                R = V / I;
                P = V * I;

            }


            else if (
                V !== null &&
                R !== null
            ) {

                if (R <= 0) {
                    result.innerHTML =
                        error(
                            "Resistência inválida."
                        );

                    return;
                }

                I = V / R;

                P =
                    (V * V) / R;

            }


            else if (
                V !== null &&
                P !== null
            ) {

                if (
                    V === 0 ||
                    P <= 0
                ) {

                    result.innerHTML =
                        error(
                            "Valores inválidos."
                        );

                    return;

                }

                I = P / V;

                R =
                    (V * V) / P;

            }


            else if (
                I !== null &&
                R !== null
            ) {

                if (R <= 0) {

                    result.innerHTML =
                        error(
                            "Resistência inválida."
                        );

                    return;

                }

                V = I * R;

                P =
                    I * I * R;

            }


            else if (
                I !== null &&
                P !== null
            ) {

                if (
                    I === 0 ||
                    P <= 0
                ) {

                    result.innerHTML =
                        error(
                            "Valores inválidos."
                        );

                    return;

                }

                V = P / I;

                R =
                    P /
                    (I * I);

            }


            else if (
                R !== null &&
                P !== null
            ) {

                if (
                    R <= 0 ||
                    P < 0
                ) {

                    result.innerHTML =
                        error(
                            "Valores inválidos."
                        );

                    return;

                }

                V =
                    Math.sqrt(
                        P * R
                    );

                I =
                    Math.sqrt(
                        P / R
                    );

            }


            el("universal-v").value =
                formatNumber(V);

            el("universal-i").value =
                formatNumber(I);

            el("universal-r").value =
                formatNumber(R);

            el("universal-p").value =
                formatNumber(P);


            result.innerHTML = `

                Tensão:
                <strong>
                ${formatNumber(V)} V
                </strong>

                <br>

                Corrente:
                <strong>
                ${formatCurrent(I)}
                </strong>

                <br>

                Resistência:
                <strong>
                ${formatResistance(R)}
                </strong>

                <br>

                Potência:
                <strong>
                ${formatPower(P)}
                </strong>

                <hr>

                ${formatNumber(I)} A
                |
                ${formatNumber(I * 1000)} mA
                |
                ${formatNumber(I * 1000000)} µA
            `;

        }
    );


    el("universal-clear")
    .addEventListener(
        "click",
        function () {

            [
                "universal-v",
                "universal-i",
                "universal-r",
                "universal-p"
            ]
            .forEach(
                id =>
                    el(id).value = ""
            );


            el(
                "universal-result"
            ).innerHTML =
                "Preencha dois valores.";

        }
    );


    /*
    =====================================
    QUEDA DE TENSÃO
    =====================================
    */

    el("drop-calc")
    .addEventListener(
        "click",
        function () {

            const before =
                getNumber(
                    "drop-before"
                );

            const after =
                getNumber(
                    "drop-after"
                );

            let R =
                getNumber(
                    "drop-r"
                );

            let I =
                getNumber(
                    "drop-i"
                );


            const result =
                el(
                    "drop-result"
                );


            if (
                before === null ||
                after === null
            ) {

                result.innerHTML =
                    error(
                        "Informe as tensões antes e depois."
                    );

                return;

            }


            const drop =
                before - after;


            const percentage =
                before !== 0
                ?
                (
                    drop /
                    Math.abs(before)
                ) * 100
                :
                0;


            let html = `

                Queda:
                <strong>
                ${formatNumber(drop)} V
                </strong>

                <br>

                Queda percentual:
                <strong>
                ${formatNumber(percentage)}%
                </strong>
            `;


            if (
                R !== null &&
                I === null
            ) {

                if (R <= 0) {

                    result.innerHTML =
                        error(
                            "Resistência inválida."
                        );

                    return;

                }

                I =
                    drop / R;


                const P =
                    drop * I;


                html += `

                    <hr>

                    Corrente:
                    <strong>
                    ${formatCurrent(I)}
                    </strong>

                    <br>

                    Potência:
                    <strong>
                    ${formatPower(P)}
                    </strong>
                `;

            }


            else if (
                I !== null &&
                R === null
            ) {

                if (I === 0) {

                    result.innerHTML =
                        error(
                            "A corrente não pode ser zero."
                        );

                    return;

                }

                R =
                    drop / I;


                const P =
                    drop * I;


                html += `

                    <hr>

                    Resistência:
                    <strong>
                    ${formatResistance(R)}
                    </strong>

                    <br>

                    Potência:
                    <strong>
                    ${formatPower(P)}
                    </strong>
                `;

            }


            else if (
                R !== null &&
                I !== null
            ) {

                const expected =
                    R * I;

                const difference =
                    Math.abs(
                        expected -
                        drop
                    );

                const P =
                    drop * I;


                html += `

                    <hr>

                    Corrente:
                    <strong>
                    ${formatCurrent(I)}
                    </strong>

                    <br>

                    Resistência:
                    <strong>
                    ${formatResistance(R)}
                    </strong>

                    <br>

                    Queda esperada:
                    <strong>
                    ${formatNumber(expected)} V
                    </strong>

                    <br>

                    Diferença:
                    ${formatNumber(difference)} V

                    <br>

                    Potência:
                    <strong>
                    ${formatPower(P)}
                    </strong>
                `;

            }


            result.innerHTML =
                html;

        }
    );


    /*
    =====================================
    RESISTORES
    =====================================
    */

    el("resistors-calc")
    .addEventListener(
        "click",
        function () {

            const text =
                el(
                    "resistors-list"
                ).value;


            const values =
                text
                .split(/[,;\s]+/)
                .map(Number)
                .filter(
                    value =>
                        Number.isFinite(value) &&
                        value > 0
                );


            const result =
                el(
                    "resistors-result"
                );


            if (
                values.length === 0
            ) {

                result.innerHTML =
                    error(
                        "Digite pelo menos um resistor."
                    );

                return;

            }


            const series =
                values.reduce(
                    (total, value) =>
                        total + value,
                    0
                );


            const parallel =
                1 /
                values.reduce(
                    (total, value) =>
                        total +
                        (1 / value),
                    0
                );


            result.innerHTML = `

                Quantidade:
                ${values.length}

                <br>

                Série:
                <strong>
                ${formatResistance(series)}
                </strong>

                <br>

                Paralelo:
                <strong>
                ${formatResistance(parallel)}
                </strong>
            `;

        }
    );


    /*
    =====================================
    PWM
    =====================================
    */

    el("pwm-calc")
    .addEventListener(
        "click",
        function () {

            const frequency =
                getNumber(
                    "pwm-frequency"
                );

            const duty =
                getNumber(
                    "pwm-duty"
                );


            const result =
                el(
                    "pwm-result"
                );


            if (
                frequency === null ||
                duty === null ||
                frequency <= 0 ||
                duty < 0 ||
                duty > 100
            ) {

                result.innerHTML =
                    error(
                        "Use frequência maior que zero e duty entre 0 e 100%."
                    );

                return;

            }


            const period =
                1 / frequency;

            const high =
                period *
                (
                    duty / 100
                );

            const low =
                period - high;


            result.innerHTML = `

                Período:
                <strong>
                ${formatNumber(period * 1000)} ms
                </strong>

                <br>

                ${formatNumber(period * 1000000)} µs

                <br><br>

                HIGH:
                <strong>
                ${formatNumber(high * 1000000)} µs
                </strong>

                <br>

                LOW:
                <strong>
                ${formatNumber(low * 1000000)} µs
                </strong>
            `;

        }
    );


    /*
    =====================================
    BATERIA
    =====================================
    */

    el("battery-calc")
    .addEventListener(
        "click",
        function () {

            const voltage =
                getNumber(
                    "battery-v"
                );

            const capacity =
                getNumber(
                    "battery-capacity"
                );

            const current =
                getNumber(
                    "battery-current"
                );

            const efficiency =
                getNumber(
                    "battery-efficiency"
                );


            const result =
                el(
                    "battery-result"
                );


            if (
                voltage === null ||
                capacity === null ||
                current === null ||
                efficiency === null ||
                voltage <= 0 ||
                capacity <= 0 ||
                current <= 0 ||
                efficiency <= 0 ||
                efficiency > 100
            ) {

                result.innerHTML =
                    error(
                        "Digite valores válidos."
                    );

                return;

            }


            const usableCapacity =
                capacity *
                (
                    efficiency / 100
                );


            const hours =
                usableCapacity /
                current;


            const wh =
                voltage *
                (
                    capacity / 1000
                );


            const loadPower =
                voltage *
                (
                    current / 1000
                );


            result.innerHTML = `

                Energia nominal:
                <strong>
                ${formatNumber(wh)} Wh
                </strong>

                <br>

                Capacidade utilizável:
                ${formatNumber(usableCapacity)} mAh

                <br>

                Potência média:
                ${formatPower(loadPower)}

                <hr>

                Autonomia estimada:
                <strong>
                ${formatNumber(hours)} horas
                </strong>

                <br>

                ≈
                ${formatNumber(hours * 60)}
                minutos
            `;

        }
    );


    /*
    =====================================
    BJT
    =====================================
    */

    el("bjt-calc")
    .addEventListener(
        "click",
        function () {

            const gpio =
                getNumber(
                    "bjt-gpio"
                );

            const vbe =
                getNumber(
                    "bjt-vbe"
                );

            const collectorMA =
                getNumber(
                    "bjt-collector"
                );

            const beta =
                getNumber(
                    "bjt-beta"
                );

            const vce =
                getNumber(
                    "bjt-vce"
                );


            const result =
                el(
                    "bjt-result"
                );


            if (
                gpio === null ||
                vbe === null ||
                collectorMA === null ||
                beta === null ||
                vce === null ||
                gpio <= vbe ||
                collectorMA <= 0 ||
                beta <= 0
            ) {

                result.innerHTML =
                    error(
                        "Digite valores válidos."
                    );

                return;

            }


            const collector =
                collectorMA / 1000;


            const base =
                collector /
                beta;


            const resistor =
                (
                    gpio - vbe
                ) /
                base;


            const transistorPower =
                collector *
                vce;


            result.innerHTML = `

                Corrente de coletor:
                <strong>
                ${formatNumber(collectorMA)} mA
                </strong>

                <br>

                Corrente de base:
                <strong>
                ${formatCurrent(base)}
                </strong>

                <br>

                Resistor de base:
                <strong>
                ${formatResistance(resistor)}
                </strong>

                <br>

                Potência aproximada:
                <strong>
                ${formatPower(transistorPower)}
                </strong>

                <hr>

                <span class="calc-warning">
                Verifique se a corrente de base
                calculada está dentro do limite
                do GPIO.
                </span>
            `;

        }
    );


    /*
    =====================================
    LED
    =====================================
    */

    el("led-calc")
    .addEventListener(
        "click",
        function () {

            const source =
                getNumber(
                    "led-source"
                );

            const vf =
                getNumber(
                    "led-vf"
                );

            const count =
                getNumber(
                    "led-count"
                );

            const currentMA =
                getNumber(
                    "led-current"
                );


            const result =
                el(
                    "led-result"
                );


            if (
                source === null ||
                vf === null ||
                count === null ||
                currentMA === null ||
                source <= 0 ||
                vf <= 0 ||
                count <= 0 ||
                currentMA <= 0
            ) {

                result.innerHTML =
                    error(
                        "Digite valores válidos."
                    );

                return;

            }


            const totalLEDVoltage =
                vf * count;


            const remaining =
                source -
                totalLEDVoltage;


            if (remaining <= 0) {

                result.innerHTML =
                    error(
                        "A tensão da fonte é insuficiente."
                    );

                return;

            }


            const current =
                currentMA /
                1000;


            const resistor =
                remaining /
                current;


            const resistorPower =
                remaining *
                current;


            const theoreticalMax =
                Math.floor(
                    source / vf
                );


            const recommendedMax =
                Math.max(
                    0,

                    Math.floor(
                        (
                            source - 1
                        ) /
                        vf
                    )
                );


            result.innerHTML = `

                Queda nos LEDs:
                <strong>
                ${formatNumber(totalLEDVoltage)} V
                </strong>

                <br>

                Tensão no resistor:
                ${formatNumber(remaining)} V

                <br>

                Resistor:
                <strong>
                ${formatResistance(resistor)}
                </strong>

                <br>

                Potência:
                <strong>
                ${formatPower(resistorPower)}
                </strong>

                <hr>

                Máximo teórico:
                ${theoreticalMax}

                <br>

                Máximo recomendado aproximado:
                <strong>
                ${recommendedMax}
                </strong>
            `;

        }
    );


    /*
    =====================================
    FONTE
    =====================================
    */

    el("psu-calc")
    .addEventListener(
        "click",
        function () {

            const voltage =
                getNumber(
                    "psu-voltage"
                );

            const margin =
                getNumber(
                    "psu-margin"
                );


            const text =
                el(
                    "psu-components"
                ).value;


            const result =
                el(
                    "psu-result"
                );


            if (
                voltage === null ||
                margin === null ||
                voltage <= 0 ||
                margin < 0
            ) {

                result.innerHTML =
                    error(
                        "Digite valores válidos."
                    );

                return;

            }


            const lines =
                text
                .split("\n")
                .filter(
                    line =>
                        line.trim() !== ""
                );


            let totalMA = 0;
            let count = 0;


            lines.forEach(
                line => {

                    const match =
                        line.match(
                            /(-?\d+(?:[.,]\d+)?)(?!.*\d)/
                        );


                    if (!match) {
                        return;
                    }


                    const current =
                        Number(
                            match[1]
                            .replace(
                                ",",
                                "."
                            )
                        );


                    if (
                        !Number.isFinite(current) ||
                        current < 0
                    ) {
                        return;
                    }


                    totalMA +=
                        current;

                    count++;

                }
            );


            if (count === 0) {

                result.innerHTML =
                    error(
                        "Informe pelo menos um componente."
                    );

                return;

            }


            const recommendedMA =
                totalMA *
                (
                    1 +
                    margin / 100
                );


            const recommendedA =
                recommendedMA /
                1000;


            const power =
                voltage *
                recommendedA;


            const ratings = [
                0.5,
                1,
                1.5,
                2,
                2.5,
                3,
                4,
                5,
                6,
                8,
                10,
                12,
                15,
                20,
                30
            ];


            let suggested =
                ratings.find(
                    value =>
                        value >=
                        recommendedA
                );


            if (!suggested) {

                suggested =
                    Math.ceil(
                        recommendedA
                    );

            }


            result.innerHTML = `

                Componentes:
                <strong>
                ${count}
                </strong>

                <br>

                Consumo total:
                <strong>
                ${formatNumber(totalMA)} mA
                </strong>

                <br>

                ${formatNumber(totalMA / 1000)} A

                <br>

                Margem:
                ${formatNumber(margin)}%

                <hr>

                Corrente mínima:
                <strong>
                ${formatNumber(recommendedA)} A
                </strong>

                <br>

                Potência mínima:
                <strong>
                ${formatNumber(power)} W
                </strong>

                <br><br>

                Fonte sugerida:
                <strong>
                ${formatNumber(voltage)} V /
                ${formatNumber(suggested)} A
                ou maior
                </strong>
            `;

        }
    );


    /*
    =====================================
    CONVERSÕES
    =====================================
    */

    function bindConversion(
        ids,
        convert
    ) {

        ids.forEach(
            id => {

                el(id)
                .addEventListener(
                    "input",
                    function () {

                        const value =
                            Number(
                                el(id).value
                            );


                        if (
                            !Number.isFinite(
                                value
                            )
                        ) {
                            return;
                        }


                        convert(
                            id,
                            value
                        );

                    }
                );

            }
        );

    }


    bindConversion(
        [
            "conv-ohm",
            "conv-kohm",
            "conv-mohm"
        ],
        function (
            source,
            value
        ) {

            let ohms;

            if (
                source ===
                "conv-ohm"
            ) {
                ohms = value;
            }

            if (
                source ===
                "conv-kohm"
            ) {
                ohms =
                    value * 1000;
            }

            if (
                source ===
                "conv-mohm"
            ) {
                ohms =
                    value * 1000000;
            }


            if (
                source !==
                "conv-ohm"
            ) {
                el("conv-ohm").value =
                    formatNumber(
                        ohms
                    );
            }

            if (
                source !==
                "conv-kohm"
            ) {
                el("conv-kohm").value =
                    formatNumber(
                        ohms / 1000
                    );
            }

            if (
                source !==
                "conv-mohm"
            ) {
                el("conv-mohm").value =
                    formatNumber(
                        ohms / 1000000
                    );
            }

        }
    );


    bindConversion(
        [
            "conv-v",
            "conv-mv"
        ],
        function (
            source,
            value
        ) {

            const volts =
                source === "conv-v"
                ?
                value
                :
                value / 1000;


            if (
                source !==
                "conv-v"
            ) {
                el("conv-v").value =
                    formatNumber(
                        volts
                    );
            }

            if (
                source !==
                "conv-mv"
            ) {
                el("conv-mv").value =
                    formatNumber(
                        volts * 1000
                    );
            }

        }
    );


    bindConversion(
        [
            "conv-a",
            "conv-ma",
            "conv-ua"
        ],
        function (
            source,
            value
        ) {

            let amps;

            if (
                source ===
                "conv-a"
            ) {
                amps = value;
            }

            if (
                source ===
                "conv-ma"
            ) {
                amps =
                    value / 1000;
            }

            if (
                source ===
                "conv-ua"
            ) {
                amps =
                    value / 1000000;
            }


            if (
                source !==
                "conv-a"
            ) {
                el("conv-a").value =
                    formatNumber(
                        amps
                    );
            }

            if (
                source !==
                "conv-ma"
            ) {
                el("conv-ma").value =
                    formatNumber(
                        amps * 1000
                    );
            }

            if (
                source !==
                "conv-ua"
            ) {
                el("conv-ua").value =
                    formatNumber(
                        amps * 1000000
                    );
            }

        }
    );


    bindConversion(
        [
            "conv-w",
            "conv-mw",
            "conv-uw"
        ],
        function (
            source,
            value
        ) {

            let watts;

            if (
                source ===
                "conv-w"
            ) {
                watts = value;
            }

            if (
                source ===
                "conv-mw"
            ) {
                watts =
                    value / 1000;
            }

            if (
                source ===
                "conv-uw"
            ) {
                watts =
                    value / 1000000;
            }


            if (
                source !==
                "conv-w"
            ) {
                el("conv-w").value =
                    formatNumber(
                        watts
                    );
            }

            if (
                source !==
                "conv-mw"
            ) {
                el("conv-mw").value =
                    formatNumber(
                        watts * 1000
                    );
            }

            if (
                source !==
                "conv-uw"
            ) {
                el("conv-uw").value =
                    formatNumber(
                        watts * 1000000
                    );
            }

        }
    );

})();
</script>

</div>
{{< /rawhtml >}}
