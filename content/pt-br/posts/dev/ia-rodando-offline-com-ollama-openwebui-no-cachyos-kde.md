---
title: Como ter sua própria LLM rodando offline na sua máquina?
slug: ai-running-offline-pllama
description: Tenha sua própria IA rodando localmente na sua máquina, offline e grátis!
summary: Tenha sua própria IA rodando localmente na sua máquina, offline e grátis!
tags:
  - linux
  - cachyos
  - ollama
  - LLMs
  - docker
  - dev
categories:
  - LLMs
  - dev
keywords: []
author: Gabriel Maggioni
date: 2026-06-02T09:22:00
lastmod: 2026-06-02T09:22:00
showToc: true
TocOpen: false
draft: false
---

# Rode IA Local no Arch Linux: LM Studio + Open WebUI

> Quer rodar modelos de linguagem no seu próprio computador, sem depender de nuvem e sem pagar por API? Neste guia você vai instalar o LM Studio, baixar um modelo e deixar tudo acessível via uma interface bonita usando o Open WebUI com Docker.

---

## O que você vai precisar

- Arch Linux (ou derivados como Manjaro, EndeavourOS)
- Uma GPU razoável **ou** uma CPU moderna com bastante RAM (8 GB no mínimo, 16 GB recomendado)
- Docker instalado
- Vontade de brincar com IA local 🤖

---

## Parte 1 — Instalando o LM Studio

O LM Studio é um aplicativo gráfico que facilita baixar, gerenciar e rodar modelos de linguagem localmente. Ele funciona como um servidor local de IA, compatível com a API do OpenAI.

### Via AUR (jeito Arch)

Se você usa `yay` ou `paru`, é direto ao ponto:

```bash
yay -S lmstudio
```

ou com `paru`:

```bash
paru -S lmstudio
```

Aguarde a compilação e instalação. Pode demorar um pouco na primeira vez.

### Via AppImage (alternativa)

Se preferir não usar o AUR, você pode baixar o AppImage direto do site oficial:

1. Acesse [lmstudio.ai](https://lmstudio.ai) e baixe o arquivo `.AppImage` para Linux.
2. Dê permissão de execução:

```bash
chmod +x LM_Studio-*.AppImage
```

3. Execute:

```bash
./LM_Studio-*.AppImage
```

> 💡 **Dica:** Para integrar melhor ao sistema, você pode usar o `appimaged` ou criar um `.desktop` manualmente em `~/.local/share/applications/`.

---

## Parte 2 — Baixando um modelo

Com o LM Studio aberto, você vai ver uma interface bem amigável. Veja como baixar seu primeiro modelo:

1. **Clique na aba de busca** (ícone de lupa ou "Discover") na barra lateral esquerda.
2. **Pesquise um modelo.** Boas pedidas para começar:
   - `mistral` — leve e eficiente
   - `llama3` — da Meta, muito capaz
   - `phi3` — surpreendentemente bom para o tamanho
   - `gemma` — da Google, ótimo custo-benefício
3. **Escolha a versão correta para sua máquina:**
   - `Q4_K_M` — bom equilíbrio entre velocidade e qualidade
   - `Q8_0` — mais qualidade, precisa de mais RAM
   - Modelos com `7B` no nome têm 7 bilhões de parâmetros (mais leve); `13B` é mais pesado mas melhor
4. **Clique em Download** e aguarde. Os modelos costumam ter entre 4 GB e 10 GB.

> 💡 **Quanto de RAM eu preciso?** Uma regra prática: um modelo `7B Q4` precisa de cerca de 5–6 GB de RAM (ou VRAM se usar GPU). Um `13B Q4` precisa de ~9 GB.

---

## Parte 3 — Rodando o servidor local

Para que o Open WebUI consiga se comunicar com o LM Studio, você precisa ativar o servidor local dele:

1. **Vá na aba "Local Server"** (ícone `<->` na barra lateral).
2. Selecione o modelo que você baixou no menu suspenso.
3. Clique em **"Start Server"**.

O servidor vai subir na porta `1234` por padrão, acessível em `http://localhost:1234`.

Você pode testar se está funcionando abrindo o terminal e rodando:

```bash
curl http://localhost:1234/v1/models
```

Se aparecer uma lista de modelos em JSON, está tudo certo! ✅

---

## Parte 4 — Instalando o Docker

Se você ainda não tem o Docker no Arch, é rapidinho:

```bash
sudo pacman -S docker
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

> ⚠️ **Importante:** Depois de rodar o `usermod`, **faça logout e login novamente** (ou reinicie) para que seu usuário reconheça o grupo `docker`. Caso contrário, você precisará usar `sudo` em todos os comandos Docker.

---

## Parte 5 — Rodando o Open WebUI

O Open WebUI é uma interface web estilo ChatGPT que se conecta ao seu servidor local. Com ele você tem:

- Histórico de conversas organizado
- Suporte a múltiplos modelos
- Upload de documentos (RAG)
- Interface muito mais agradável que o terminal

Para subir o container, rode:

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

O que cada parte faz:

| Opção | O que faz |
|---|---|
| `-d` | Roda em segundo plano |
| `-p 3000:8080` | Expõe na porta 3000 do seu PC |
| `--add-host=host.docker.internal:host-gateway` | Permite o container acessar o LM Studio no host |
| `-v open-webui:/app/backend/data` | Salva seus dados (conversas, config) em um volume persistente |
| `--restart always` | Reinicia automaticamente com o sistema |

Aguarde o download da imagem (pode demorar alguns minutos na primeira vez) e depois acesse:

```
http://localhost:3000
```

---

## Parte 6 — Conectando o Open WebUI ao LM Studio

1. Acesse `http://localhost:3000` no navegador.
2. Na primeira vez, crie uma conta local (não precisa de email real, é só para o sistema local).
3. Vá em **Configurações → Connections** (ou Conexões).
4. Em **OpenAI API**, coloque a URL:

```
http://host.docker.internal:1234/v1
```

5. No campo de API Key, coloque qualquer coisa (ex: `lmstudio`) — o LM Studio não valida isso.
6. Clique em **Save** e depois em **Verify Connection**.

Se aparecer um ✅ verde, está conectado! Agora você pode voltar para a tela principal e já vai ver o modelo disponível para conversar.

---

## Dicas Finais

**Para parar o Open WebUI:**
```bash
docker stop open-webui
```

**Para iniciar novamente:**
```bash
docker start open-webui
```

**Para ver os logs se algo der errado:**
```bash
docker logs open-webui
```

**Usando GPU NVIDIA?**  
Instale o `nvidia-container-toolkit` e adicione `--gpus all` no comando do Docker para acelerar bastante a inferência:

```bash
sudo pacman -S nvidia-container-toolkit
```

```bash
docker run -d -p 3000:8080 \
  --gpus all \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:cuda
```

> Note que no caso com GPU, a imagem muda para `:cuda`.

---

## Conclusão

Agora você tem uma stack completa de IA rodando 100% local no seu Arch Linux:

- **LM Studio** gerencia e serve os modelos
- **Open WebUI** entrega uma interface rica e completa
- **Docker** mantém tudo isolado e fácil de gerenciar

Seus dados ficam no seu computador, você não paga por token e pode experimentar dezenas de modelos diferentes. Bem-vindo ao mundo da IA local! 🚀
