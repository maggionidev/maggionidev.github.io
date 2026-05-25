---
title: Resolvendo problema de vibração dongle 2.4GHz MACHENIKE G5 PRO no CachyOS
slug: ''
description: ''
summary: ''
tags:
  - dev
  - controller
  - gaming
  - machenike g5 pro
categories:
  - dev
  - gaming
keywords: []
author: Gabriel Maggioni
date: 2026-05-25T19:21:00
lastmod: 2026-05-25T19:21:00
showToc: true
TocOpen: false
draft: false
---

Eu recentemente comprei um 8BitDo Ultimate 2 e um Machenike G5 Pro 🎮 pra usar no meu CachyOS.

O 8BitDo funcionou perfeito de primeira. O Machenike também... menos a vibração 😭
Via cabo funcionava, mas no dongle 2.4GHz ele se recusava a vibrar.

Comecei diagnosticando assim:

```plain
dmesg -w

```

Depois conectei o controle e percebi que ele entrava em:

```plain
2345:e02e
hid-generic

```

em vez de:

```plain
2345:e00b
xpad
Xbox 360 Controller for Windows

```

Também testei o force feedback:

```plain
sudo pacman -S linuxconsole
fftest /dev/input/eventXX

```

e recebia:

```plain
Function not implemented

```

ou seja: o Linux estava carregando `hid-generic` em vez do `xpad`, então não existia suporte real a rumble/vibração.

Encontrei a solução no [reddit](https://www.reddit.com/r/linux_gaming/comments/1co49zb/help_with_vibration_not_working_on_machenike_g5/?tl=pt-br) e consegui fazer funcionar.

Primeiro forcei o modo Xbox no controle:

- conectei via cabo USB
- liguei segurando `Home + A`

Depois adicionei quirks USB temporários:

```plain
echo -n "2345:e00b:ik,2345:e02e:ik,2345:e02f:ik" | sudo tee /sys/module/usbcore/parameters/quirks

```

Criei uma regra udev:

Arquivo:
`/etc/udev/rules.d/98-joystick.rules`

Conteúdo:

```plain
ACTION=="add", ATTRS{idVendor}=="2345", ATTRS{idProduct}=="e02e", RUN+="/sbin/modprobe xpad", RUN+="/bin/sh -c 'echo 2345 e02e > /sys/bus/usb/drivers/xpad/new_id'"

```

Depois adicionei no GRUB:

Arquivo:
`/etc/default/grub`

Na linha `GRUB_CMDLINE_LINUX_DEFAULT`:

```plain
usbcore.quirks=2345:e00b:ik,2345:e02e:ik,2345:e02f:ik

```

Regenerei o grub:

```plain
sudo grub-mkconfig -o /boot/grub/grub.cfg

```

Recarreguei o udev:

```plain
sudo udevadm control --reload-rules
sudo udevadm trigger

```

Depois disso:

- o controle virou `Xbox 360 Controller for Windows`
- o driver `xpad` carregou corretamente
- a vibração passou a funcionar
- a Steam reconheceu normal
- o `fftest` finalmente detectou force feedback

Pra testar vibração:

```plain
fftest /dev/input/by-id/usb-MACHENIKE_Xbox_360_Controller_for_Windows_*-event-joystick

```

Talvez ajude alguém sofrendo com esse controle no Linux 😭
