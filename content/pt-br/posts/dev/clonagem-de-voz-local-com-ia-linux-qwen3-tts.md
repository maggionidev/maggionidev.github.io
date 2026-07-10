---
title: Clonagem de voz local com IA (Linux) - Qwen3-TTS
slug: clone-voice-qwen3-tts
description: ''
summary: ''
cover: null
tags:
  - LLMs
  - ia
  - linux
  - cachyos
categories:
  - dev
  - linux
keywords: []
author: Gabriel Maggioni
date: 2026-07-09T23:16:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

O avanço dos modelos de inteligência artificial para áudio permitiu que hoje seja possível executar sistemas de **Text To Speech (TTS)** localmente, sem depender de serviços pagos na nuvem.

O **Qwen3-TTS** é uma família de modelos de síntese de voz capaz de transformar texto em fala, criar vozes personalizadas e realizar clonagem de voz usando um pequeno áudio de referência. O projeto oficial disponibiliza modelos abertos e permite execução local através do pacote Python `qwen-tts`. :contentReference[oaicite:1]{index=1}

Neste guia vamos instalar tudo no **CachyOS**, mas os passos funcionam praticamente iguais no **Arch Linux**.

O objetivo final:

- instalar o ambiente Python isolado;
- baixar o modelo Qwen3-TTS;
- preparar um áudio de referência;
- clonar uma voz;
- gerar um arquivo `.wav`.

---

# Requisitos

## Hardware recomendado

O modelo usado neste guia:

```

Qwen3-TTS-12Hz-1.7B-Base

````

possui aproximadamente 4,5 GB de arquivos de modelo. :contentReference[oaicite:2]{index=2}

Recomendado:

- 16 GB de RAM ou mais;
- GPU dedicada ajuda bastante;
- SSD com espaço livre;
- Python 3.11 ou 3.12.

No caso de placas AMD Radeon, como RX 6000/7000, o suporte depende da configuração PyTorch/ROCm disponível. Em muitos casos o funcionamento via CPU é mais simples.

---

# 1. Instalar dependências no CachyOS/Arch

No Arch Linux e derivados usamos o `pacman`.

```bash
sudo pacman -S git cmake base-devel python311 python-pip ffmpeg sox
````

Esses pacotes fornecem:

* `git`: baixar códigos;
* `cmake`: compilação de dependências;
* `base-devel`: ferramentas de compilação do Arch;
* `python311`: versão isolada do Python;
* `ffmpeg`: conversão e tratamento de áudio;
* `sox`: ferramentas adicionais para áudio.

---

# 2. Criar diretório do projeto

Vamos manter tudo organizado dentro de uma pasta própria.

```bash
mkdir -p ~/Qwen3-TTS

cd ~/Qwen3-TTS
```

A estrutura ficará:

```
~/Qwen3-TTS
```

---

# 3. Criar ambiente virtual Python

Não é recomendado instalar bibliotecas de IA diretamente no sistema.

Criamos um ambiente isolado:

```bash
python3.11 -m venv .venv311
```

Ativar:

```bash
source .venv311/bin/activate
```

O terminal deverá mostrar algo parecido:

```
(.venv311)
```

Agora todas as instalações ficam dentro desse ambiente.

---

# 4. Atualizar ferramentas Python

Atualizamos o gerenciador de pacotes:

```bash
pip install -U pip setuptools wheel
```

---

# 5. Instalar Qwen3-TTS

Instalar o pacote oficial:

```bash
pip install qwen-tts soundfile
```

O pacote `qwen-tts` instala as dependências necessárias para carregar os modelos Qwen3-TTS. ([GitHub][1])

---

# 6. Instalar ferramenta Hugging Face

Os modelos ficam hospedados no Hugging Face.

Instale o cliente:

```bash
pip install -U "huggingface_hub[cli]"
```

Teste:

```bash
huggingface-cli --help
```

---

# 7. Criar pasta dos modelos

```bash
mkdir -p models

cd models
```

---

# 8. Baixar modelo de clonagem de voz

Modelo principal:

```bash
huggingface-cli download Qwen/Qwen3-TTS-12Hz-1.7B-Base \
--local-dir Qwen3-TTS-12Hz-1.7B-Base
```

Esse modelo é responsável pela geração baseada em referência de voz. ([GitHub][1])

---

# 9. Baixar tokenizer de áudio

O tokenizer é usado pelo modelo para processar a representação do áudio.

```bash
huggingface-cli download Qwen/Qwen3-TTS-Tokenizer-12Hz \
--local-dir Qwen3-TTS-Tokenizer-12Hz
```

---

Voltar para o projeto:

```bash
cd ~/Qwen3-TTS
```

---

# 10. Preparar áudio de referência

Para clonagem de voz, utilize um áudio limpo.

Recomendado:

* 5 a 30 segundos;
* pouca música ou ruído;
* voz clara;
* uma única pessoa falando.

Exemplo:

```
~/Qwen3-TTS/minha_voz.wav
```

---

## Converter áudio para formato ideal

O modelo trabalha melhor com áudio mono em 16 kHz.

Converter:

```bash
ffmpeg -i minha_voz.wav \
-ar 16000 \
-ac 1 \
minha_voz_convertida.wav
```

Resultado:

```
minha_voz_convertida.wav
```

---

# 11. Criar script de clonagem

Criar arquivo:

```bash
nano teste_qwen.py
```

Cole:

```python
from qwen_tts import Qwen3TTSModel
import soundfile as sf


model = Qwen3TTSModel.from_pretrained(
    "./models/Qwen3-TTS-12Hz-1.7B-Base"
)


wavs, sr = model.generate_voice_clone(
    text="Olá, este é um teste da minha voz clonada usando inteligência artificial.",
    language="Portuguese",
    ref_audio="minha_voz_convertida.wav",
    ref_text="TRANSCRIÇÃO EXATA DO TEXTO FALADO NO ÁUDIO"
)


sf.write(
    "teste_saida.wav",
    wavs[0],
    sr
)


print("Áudio criado: teste_saida.wav")
```

Salvar:

```
CTRL + O
ENTER
CTRL + X
```

---

# 12. Executar clonagem

Com o ambiente virtual ativo:

```bash
python teste_qwen.py
```

Após finalizar:

```
Áudio criado: teste_saida.wav
```

---

# 13. Ouvir resultado

Usando FFmpeg:

```bash
ffplay teste_saida.wav
```

Ou qualquer player:

```bash
mpv teste_saida.wav
```

---

# Estrutura final do projeto

Após tudo instalado:

```
~/Qwen3-TTS

├── .venv311

├── models
│
│   ├── Qwen3-TTS-12Hz-1.7B-Base
│   │
│   └── Qwen3-TTS-Tokenizer-12Hz

├── minha_voz.wav

├── minha_voz_convertida.wav

├── teste_qwen.py

└── teste_saida.wav
```

---

# Como usar novamente depois de reiniciar

Sempre que abrir um novo terminal:

```bash
cd ~/Qwen3-TTS

source .venv311/bin/activate
```

Depois execute:

```bash
python teste_qwen.py
```

---

# Melhorando desempenho no Linux

## Verificar uso da GPU

Para NVIDIA:

```bash
nvidia-smi
```

Para AMD:

```bash
radeontop
```

ou:

```bash
watch -n1 cat /sys/kernel/debug/dri/0/amdgpu_pm_info
```

---

# Possíveis problemas

## Erro: comando huggingface-cli não encontrado

Instale:

```bash
pip install -U "huggingface_hub[cli]"
```

---

## Erro de memória RAM

O modelo 1.7B pode consumir bastante memória.

Alternativa:

usar modelo menor:

```
Qwen3-TTS-12Hz-0.6B-Base
```

---

## Voz ficou diferente

Normalmente acontece quando:

* áudio de referência possui ruído;
* transcrição está errada;
* áudio é muito curto;
* há música ao fundo.

Melhor resultado:

* grave em ambiente silencioso;
* use microfone próximo;
* forneça uma transcrição exatamente igual.

---

# Usos possíveis

Com Qwen3-TTS local é possível criar:

* narradores para vídeos;
* dublagem;
* personagens para jogos;
* assistentes offline;
* audiobooks;
* NPCs com voz própria;
* sistemas de acessibilidade.

---
