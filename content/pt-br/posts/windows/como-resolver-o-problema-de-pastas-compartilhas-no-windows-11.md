---
title: Como resolver o problema de pastas compartilhas no windows 11
slug: solving-the-windows11-shared-folder-problem
description: Resolvendo o problema de compartilhamento de pastas no windows 11
summary: ''
cover: null
tags:
  - windows
categories:
  - windows
keywords:
  - Windows
author: Gabriel Maggioni
date: 2026-09-11T11:43:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

Se o Windows 11 consegue enxergar outros computadores na rede, mas apresenta erro ao tentar acessar \*\*pastas compartilhadas, impressoras ou outros dispositivos\*\*, algumas configurações de rede e do protocolo SMB podem estar bloqueando a conexão.

Neste guia, vamos configurar o Windows 11 para permitir o compartilhamento corretamente.

## 1. Configure a rede como privada

Abra:

\*\*Configurações > Rede e Internet\*\*

Clique na conexão que você está utilizando, seja \*\*Ethernet\*\* ou \*\*Wi-Fi\*\*.

Em \*\*Tipo de perfil de rede\*\*, altere:

\`\`\`text
Público → Privado
\`\`\`

O perfil privado permite que o computador seja descoberto por outros dispositivos da mesma rede.

---

## 2. Ative a descoberta de rede

Agora acesse:

\*\*Configurações > Rede e Internet > Configurações avançadas de rede > Configurações de compartilhamento avançadas\*\*

### Redes privadas

Ative:

\* \*\*Descoberta de rede\*\*
\* \*\*Configurar dispositivos conectados à rede automaticamente\*\*
\* \*\*Compartilhamento de arquivos e impressoras\*\*

### Redes públicas

Por segurança, deixe a \*\*Descoberta de rede desativada\*\*.

### Todas as redes

Se você deseja acessar compartilhamentos sem utilizar usuário e senha, desative:

\* \*\*Compartilhamento protegido por senha\*\*

> Em redes corporativas ou redes que você não controla, é recomendável manter autenticação e outras proteções habilitadas.

---

## 3. Ajuste as configurações SMB

Se mesmo após as configurações anteriores o Windows continuar apresentando erros ao acessar outro computador, abra o \*\*PowerShell como Administrador\*\*.

Execute:

\`\`\`powershell
Set-SmbClientConfiguration -EnableInsecureGuestLogons $true -Force
Set-SmbClientConfiguration -RequireSecuritySignature $false -Force
Set-SmbServerConfiguration -RequireSecuritySignature $false -Force
\`\`\`

O primeiro comando permite conexões SMB utilizando acesso de convidado. Os outros dois removem a exigência de assinatura SMB no cliente e no servidor.

\*\*Atenção:\*\* essas configurações reduzem a segurança do SMB e devem ser utilizadas principalmente em redes locais confiáveis ou quando você precisa de compatibilidade com computadores/dispositivos antigos. A própria Microsoft alerta que logons de convidado inseguros e conexões SMB sem assinatura podem facilitar ataques de interceptação e roubo de credenciais.

---

## 4. Verifique os computadores da rede

Abra o \*\*Explorador de Arquivos\*\* e clique em:

\`\`\`text
Rede
\`\`\`

Caso apareça uma mensagem informando que:

> A descoberta de rede e o compartilhamento de arquivos estão desativados

clique no aviso e escolha a opção para \*\*ativar a descoberta de rede e o compartilhamento de arquivos\*\*.

Após alguns segundos, os computadores da rede devem começar a aparecer.

Você também pode acessar diretamente outro computador utilizando o endereço IP:

\`\`\`text
\\192.168.1.100
\`\`\`

ou pelo nome:

\`\`\`text
\\NOME-DO-PC
\`\`\`

Se tudo estiver configurado corretamente, as pastas e impressoras compartilhadas daquele computador deverão aparecer normalmente.

## Conclusão

Problemas de compartilhamento no Windows 11 geralmente estão relacionados ao \*\*perfil público da rede, descoberta de rede desativada, compartilhamento protegido por senha ou às novas exigências de segurança do SMB\*\*.

Depois de ajustar essas opções, o acesso entre computadores Windows em uma rede local tende a voltar a funcionar normalmente.
