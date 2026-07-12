---
title: 'OmniVoice: alternativa local ao ElevenLabs que roda na sua GPU'
slug: Neste tutorial você aprenderá a instalar e configurar o OmniVoice no CachyOS utilizando uma AMD Radeon RX 6600
description: Neste tutorial você aprenderá a instalar e configurar o OmniVoice no CachyOS utilizando uma AMD Radeon RX 6600
summary: ''
cover: null
tags:
  - amd
  - ia
  - LLMs
  - linux
  - dev
categories:
  - linux
  - dev
keywords: []
author: Gabriel Maggioni
date: 2026-07-12T13:06:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

O OmniVoice é um modelo de inteligência artificial para síntese de voz que pode ser executado inteiramente no seu próprio computador. Diferente de serviços como o ElevenLabs, que dependem da nuvem, ele permite gerar áudio localmente utilizando a GPU, oferecendo maior privacidade, funcionamento sem internet, ausência de limites de uso e tempos de resposta muito menores após a configuração inicial.

Neste tutorial, mostrarei exatamente como configurei o OmniVoice em um CachyOS com uma AMD Radeon RX 6600 utilizando ROCm. Embora o procedimento tenha sido realizado no CachyOS, ele também pode ser reproduzido em praticamente qualquer distribuição baseada em Arch Linux, como Arch Linux, EndeavourOS, Garuda Linux e Manjaro, com poucas ou nenhuma adaptação.

Ao final deste guia você terá o OmniVoice executando localmente, aproveitando toda a capacidade da GPU AMD para gerar vozes de alta qualidade sem depender de serviços externos.

---

## Instalando o Python 3.11

O OmniVoice funciona melhor utilizando o Python 3.11.

```bash
pacman -S python311
```

## Criando um ambiente virtual

Crie um diretório para o projeto e inicialize um ambiente virtual.

```bash
mkdir -p ~/dev/omnivoice
cd ~/dev/omnivoice

python3.11 -m venv .venv
```

Se você utiliza **Fish Shell**:

```bash
source .venv/bin/activate.fish
```

Caso utilize **Bash**:

```bash
source .venv/bin/activate
```

## Atualizando o pip

```bash
pip install -U pip
```

## Instalando o PyTorch com suporte ao ROCm

Instale a versão do PyTorch compatível com ROCm.

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm6.4
```

## Verificando se a GPU foi reconhecida

Execute:

```bash
python -c "import torch; print(torch.cuda.is_available()); print(torch.version.hip)"
```

O resultado esperado é parecido com este:

```text
True
6.4.x
```

Se `torch.cuda.is_available()` retornar `False`, provavelmente o ROCm ainda não está reconhecendo corretamente sua GPU.

## Instalando o OmniVoice

```bash
pip install omnivoice
```

## Executando a interface Web

Inicie o servidor:

```bash
omnivoice-demo --ip 127.0.0.1 --port 8001
```

Depois abra no navegador:

```text
http://127.0.0.1:8001
```

Toda a inferência acontece localmente na sua máquina. Nenhum áudio é enviado para servidores externos.

## Caso a RX 6600 não seja detectada

Algumas placas RDNA2 precisam informar manualmente sua arquitetura ao ROCm.

Antes de iniciar o OmniVoice execute:

```bash
export HSA_OVERRIDE_GFX_VERSION=10.3.0
export PYTORCH_ROCM_ARCH=gfx1030
```

Depois execute novamente:

```bash
omnivoice-demo --ip 127.0.0.1 --port 8001
```

No meu caso essas variáveis foram necessárias para que a RX 6600 fosse utilizada corretamente.

## Teste rápido via Python

Você também pode verificar se tudo está funcionando carregando o modelo diretamente.

```python
from omnivoice import OmniVoice
import torch

model = OmniVoice.from_pretrained(
    "k2-fsa/OmniVoice",
    device_map="cuda:0",
    dtype=torch.float16,
)

audio = model.generate(
    text="Olá, este é um teste do OmniVoice."
)
```

Mesmo utilizando uma GPU AMD, o parâmetro continua sendo:

```python
device_map="cuda:0"
```

Isso acontece porque o backend ROCm implementa a mesma interface CUDA utilizada pelo PyTorch.

## Resumo da instalação

```bash
yay -S python311

mkdir -p ~/dev/omnivoice
cd ~/dev/omnivoice

python3.11 -m venv .venv
source .venv/bin/activate.fish

pip install -U pip

pip install torch torchvision torchaudio \
  --index-url https://download.pytorch.org/whl/rocm6.4

python -c "import torch; print(torch.cuda.is_available()); print(torch.version.hip)"

pip install omnivoice

export HSA_OVERRIDE_GFX_VERSION=10.3.0
export PYTORCH_ROCM_ARCH=gfx1030

omnivoice-demo --ip 127.0.0.1 --port 8001
```

## Conclusão

Depois dessa configuração, o OmniVoice passou a utilizar a RX 6600 normalmente no CachyOS. O processo de geração de voz ocorre inteiramente na GPU, proporcionando um desempenho muito superior ao da CPU.

Se você estiver utilizando outra placa RDNA2 ou RDNA3, talvez seja necessário ajustar as variáveis relacionadas à arquitetura (`HSA_OVERRIDE_GFX_VERSION` e `PYTORCH_ROCM_ARCH`), mas para a RX 6600 essa configuração funcionou sem problemas.
