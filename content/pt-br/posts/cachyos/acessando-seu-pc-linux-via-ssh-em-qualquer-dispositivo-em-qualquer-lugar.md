---
title: Acessando seu PC (Linux) via SSH - em qualquer dispositivo, em qualquer lugar
slug: cachyos-tailscale-guide
description: ''
summary: ''
tags:
  - cachyos
  - dev
categories:
  - cachyos
keywords: []
author: Gabriel Maggioni
date: 2026-06-17T21:13:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: true
---

## Introdução

Você quer acessar o terminal do seu PC remotamente sem complicações com roteadores ou firewalls? O **Tailscale** é a solução perfeita: cria uma VPN privada e segura entre seus dispositivos, e ainda oferece um recurso de SSH nativo que substitui a necessidade de senhas e chaves manuais.

Neste guia, você vai aprender a instalar, configurar e usar o Tailscale SSH no CachyOS para acessar seu PC de qualquer lugar, diretamente do navegador do celular ou de um app cliente SSH como o Termius.

***

## 📋 Pré-requisitos

- **CachyOS** (ou qualquer Arch Linux) instalado e com acesso à internet.
- **Conta no Tailscale** (gratuita) – crie em [tailscale.com](https://tailscale.com).
- **Celular Android/iOS** com acesso à internet.
- **App Tailscale** instalado no celular (baixe da loja oficial).

***

## 🚀 Passo 1: Instalar o Tailscale no CachyOS

Abra o terminal e instale o pacote `tailscale` disponível nos repositórios oficiais:

```bash
sudo pacman -S tailscale
```

Inicie e habilite o serviço para que ele rode em segundo plano:

```bash
sudo systemctl start tailscaled
sudo systemctl enable tailscaled
```

Conecte seu PC à sua rede Tailscale (Tailnet):

```bash
sudo tailscale up
```

O terminal exibirá um link. Copie-o, cole no navegador e faça login com sua conta Tailscale.

Verifique se está conectado e anote o IP Tailscale do seu PC:

```bash
tailscale ip
```

Saída esperada (exemplo):

```plain
100.94.2.11
fd7a:115c:a1e0::3701:2d4
```

> ✏️ **Dica:** O IP `100.x.x.x` é o endereço que você usará para acessar o PC de outros dispositivos.

***

## 🔐 Passo 2: Ativar o Tailscale SSH no CachyOS

Agora ative o servidor SSH nativo do Tailscale:

```bash
sudo tailscale set --ssh
```

Isso fará com que o Tailscale escute conexões SSH na porta 22, mas **apenas** para requisições vindas de dentro da sua rede Tailscale.

Confirme o status:

```bash
tailscale status
```

Você deve ver algo como:

```plain
100.94.2.11   cachyos-x8664   seuemail@gmail.com   linux   SSH: enabled
```

***


## 📱 Passo 3: Instalar o Tailscale no Celular

1. Baixe o app Tailscale na **Google Play Store** (Android) ou **App Store** (iOS).
2. Faça login com a **mesma conta** que você usou no CachyOS (`devmaggioni@gmail.com`).
3. Ative a VPN – seu celular agora faz parte da mesma rede Tailscale.

Verifique se o celular aparece na lista de dispositivos do Admin Console.

***

## 🖥️ Passo 4: Acessar o SSH pelo Navegador (sem instalar app)

A maneira mais rápida  é usar o próprio Admin Console do Tailscale:

1. No celular, abra o navegador e vá para [https://login.tailscale.com/admin/machines](https://login.tailscale.com/admin/machines).
2. Localize seu PC CachyOS e clique no botão **SSH** (ícone de terminal).
3. Uma sessão de terminal web será aberta diretamente no seu PC – você pode dar comandos como se estivesse sentado na frente dele.

> ✅ **Vantagem:** Não precisa instalar nenhum app de terminal no celular.

***

## 📲 Passo 5: Acessar via App Cliente SSH (Termius)

Se você prefere um app dedicado com mais recursos, instale o **Termius**  e configure da seguinte forma:

### 5.1. Obter o IP do Tailscale no CachyOS

No terminal do PC, execute novamente para confirmar o IP atual:

```bash
tailscale ip
```

Anote o endereço IP (ex: `100.94.2.11`).

### 5.2. Criar um novo host no Termius

1. Abra o Termius e toque no ícone **+** (Adicionar novo host).
2. Preencha os campos:

- **Address** (Endereço): `100.94.2.11`
- **Port** (Porta): `22` (padrão SSH)
- **Username** (Usuário): `seunome` (ou o nome de usuário do seu CachyOS)
- **Password** (Senha): **deixe em branco** – o Tailscale SSH autentica via identidade, não usa senha.

3. Salve o host.

### 5.3. Conectar

Toque no host criado. O Termius tentará estabelecer a conexão. Se a ACL estiver correta, você será autenticado automaticamente e verá o prompt do seu CachyOS.

> ⚠️ **Caso peça senha:** Isso geralmente indica que a ACL não está configurada ou o Tailscale SSH não está ativo. Verifique os passos anteriores.

***

## 🧪 Passo 6: Teste local (opcional)

Para confirmar que o SSH do Tailscale está funcionando, teste no próprio CachyOS:

```bash
tailscale ssh localhost
```

Isso deve abrir uma sessão SSH para o mesmo computador. Se falhar, revise a ativação e a ACL.

***

## ❓ Solução de problemas comuns

| Problema | Verificação / Solução |
| --- | --- |
| **Conexão recusada** | - O serviço `tailscaled` está rodando? `sudo systemctl status tailscaled`<br>- O SSH foi ativado? `sudo tailscale set --ssh` |
| **Permissão negada** | - A ACL está salva? Acesse o Admin Console e confirme a regra SSH.<br>- O usuário na ACL corresponde ao seu nome de usuário (`gabriel`)? |
| **Termius pede senha** | - O Tailscale SSH **não usa senha**. Deixe o campo senha em branco.<br>- Se quiser usar senha do sistema, instale e configure o `openssh` separadamente e use o IP do Tailscale. |
| **Celular não vê o PC** | - Verifique se o celular está logado na mesma conta Tailscale.<br>- O app Tailscale no celular está ativo (VPN ligada)? |
| **IP mudou** | - O IP Tailscale pode mudar se a rede reiniciar. Use o comando `tailscale ip` para obter o atual, ou use o **nome do host** (ex: `cachyos-x8664`) no campo Address do Termius – o Tailscale resolve o nome automaticamente. |

***

## 🧠 Dicas finais

- **Tailscale SSH vs OpenSSH:** O Tailscale SSH usa autenticação baseada em identidade (Tailscale), dispensando o gerenciamento de chaves e senhas. É mais seguro e fácil.
- **Multi-usuário:** Se você tiver mais de um usuário no CachyOS, pode adicionar múltiplos `users` na ACL ou usar `["autogroup:nonroot"]` para permitir qualquer usuário não-root.
- **Firewall:** O Tailscale gerencia automaticamente as regras de firewall para as conexões internas – você não precisa abrir portas no roteador.
- **Desconectar:** Para sair do Tailscale no PC, use `sudo tailscale down`.

***

## 🎯 Conclusão

Com este guia, você configurou o Tailscale no CachyOS, ativou o SSH nativo, criou a política de acesso e agora pode acessar o terminal do seu PC remotamente **pelo celular**, seja pelo navegador ou pelo Termius, sem precisar de Termux ou de configurações complicadas.

Tudo isso com criptografia de ponta a ponta e sem expor seu PC à internet pública. 🚀

***

**Gostou?** Compartilhe com outros usuários de CachyOS ou Arch Linux. Se tiver dúvidas, a [documentação oficial do Tailscale](https://tailscale.com/docs) é um excelente recurso complementar.
