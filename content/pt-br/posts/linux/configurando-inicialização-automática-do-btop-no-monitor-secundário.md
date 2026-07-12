---
title: Configurando inicialização automática do Btop no monitor secundário
slug: konsole-fullscreen-btop-boot-monitor-especifico
description: Configure o KDE para iniciar o Konsole em tela cheia executando o btop, direcionado a um monitor específico.
summary: Configure o KDE para iniciar o Konsole em tela cheia executando o btop, direcionado a um monitor específico.
cover: null
tags:
  - linux
  - dev
  - arch
  - cachyos
categories:
  - linux
  - cachyos
keywords: []
author: Gabriel Maggioni
date: 2026-07-12T16:30:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

## Introdução

Eu possuo dois monitores, o meu principal, e um menor  secundário (Arzopa), eu queria que ao ligar meu PC com CachyOS, automaticamente se abrisse uma janela do btop (monitor de recursos). 

Assim fica aquela janelinha legal mostrando o uso dos componentes em tempo real.

Ter um monitor de sistema sempre visível é útil para quem acompanha uso de CPU, memória e disco com frequência. Neste artigo, mostro como configurar o KDE Plasma para abrir automaticamente o Konsole em tela cheia ao dar boot, já executando o btop dentro do shell fish, e ainda direcionar essa janela para um monitor específico em um setup com múltiplos displays.

## Requisitos

- KDE Plasma (testado no Plasma 6.7, sessão Wayland)
- Konsole como terminal
- Shell fish configurado
- btop instalado
- Ambiente com dois ou mais monitores (para a parte de posicionamento)

## Desenvolvimento

### Definindo o comando de inicialização

O primeiro passo é montar o comando que abre o Konsole em tela cheia, roda o fish e, dentro dele, executa o btop:

```
konsole  -e fish -c "btop; exec fish"
```

O `-e fish -c "btop; exec fish"` faz o fish rodar o btop imediatamente; quando o btop é fechado (tecla q), o `exec fish` substitui o processo atual por um novo fish, mantendo o terminal aberto no prompt normal em vez de fechar a janela.

### Adicionando ao Autostart

Para que esse comando rode automaticamente a cada boot, use o recurso de inicialização automática do KDE:

1. Abra Configurações do Sistema.
2. Vá em Inicialização e Encerramento > Autostart.
3. Clique em Adicionar Novo > Aplicativo
4. Cole o comando `konsole  -e fish -c "btop; exec fish"`
5. Marque a caixinha "executar no terminal"
6. Salve.

Também é possível fazer isso manualmente criando um arquivo `.desktop` em `~/.config/autostart/`:

```
[Desktop Entry]
Type=Application
Name=Btop no Konsole
Exec=konsole  -e fish -c "btop; exec fish"
X-KDE-autostart-after=ksmserver
```

Essa segunda opção é útil se você quiser versionar sua configuração de dotfiles.

### Forçando a janela a abrir em um monitor específico

Em ambientes Wayland, o KWin (gerenciador de janelas do KDE) não aceita opções de posicionamento por linha de comando, como `--geometry`, que funcionam apenas no X11. A forma correta de resolver isso é criar uma Regra de Janela.

Primeiramente, abra o terminal, execute o comando desejado, no meu caso o `btop`, posiciono ele no monitor que eu quero, maximizado.

Em seguida:

1. Vá em Configurações do Sistema > Gerenciamento de Janelas > Regras de Janelas.
2. Clique em Adicionar Nova Regra.
3. Clique em "Detectar propriedades da janela"
4. Clique encima da janela do console, no outro monitor. Isso vai puxar as configurações atuais daquela tela;
5.Vai aparecer uma lista de variáveis referente aquela janela e monitor. Queremos copiar elas para que quando dê boot, ela inicie exatamente nessa mesma aparência que estamos vendo. Clique no "+" para ir adicionando: posição, tamanho, maximizado horizontalmente, maximizado verticalmente, áreas de trabalhos virtuais, camadas.
6. Clique em aplicar.

### Por que a combinação Autostart + Window Rule é necessária

O Autostart cuida de executar o comando no momento certo do boot. A Window Rule cuida de onde essa janela vai aparecer. Um não substitui o outro: sem o Autostart, você precisaria abrir manualmente; sem a Window Rule, o Konsole abriria no monitor padrão, ignorando sua preferência.

## Recapitulação

O comando `konsole -e fish -c "btop; exec fish"` abre um terminal em tela cheia já rodando o btop, retornando ao fish normal ao sair. Adicionar esse comando ao Autostart do KDE garante que ele rode a cada boot. Para forçar a janela a aparecer em um monitor específico no Wayland, a solução é uma Regra de Janela configurada em Gerenciamento de Janelas, já que opções de posicionamento por linha de comando não funcionam nesse protocolo.

## Conclusão

Esse tipo de configuração é especialmente útil em estações com múltiplos monitores, onde um dos displays fica reservado para monitoramento constante do sistema. O mesmo princípio de Window Rules pode ser reaproveitado para qualquer outro aplicativo que você queira sempre no mesmo monitor, independente da ordem em que as janelas são abertas.
