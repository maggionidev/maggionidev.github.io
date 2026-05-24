---
title: Como treinar IA com LoRa na RX 6600 CachyOS
slug: ''
description: ''
summary: ''
tags: []
categories: []
keywords: []
author: Gabriel Maggioni
date: 2026-05-19T08:39:00
lastmod: ''
showToc: true
TocOpen: false
draft: true
---

# Treino Local de IA com LoRA + Exportação GGUF

## CachyOS (Arch) · Ryzen 7 5600X · RX 6600 (8 GB VRAM)

> **Guia verificado em maio de 2026.** Todas as versões, URLs e comandos foram conferidos nas fontes oficiais (ROCm docs, PyTorch.org, llama.cpp GitHub, ArchWiki).

***

## Índice

1. [Avisos Importantes](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#1-avisos-importantes)
2. [Atualizar o Sistema](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#2-atualizar-o-sistema)
3. [Instalar ROCm no CachyOS](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#3-instalar-rocm-no-cachyos)
4. [Configurar Variáveis de Ambiente](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#4-configurar-vari%C3%A1veis-de-ambiente)
5. [Verificar GPU](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#5-verificar-gpu)
6. [Criar Ambiente Python](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#6-criar-ambiente-python)
7. [Instalar PyTorch com ROCm](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#7-instalar-pytorch-com-rocm)
8. [Instalar Bibliotecas de LoRA](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#8-instalar-bibliotecas-de-lora)
9. [Treinar com LoRA](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#9-treinar-com-lora)
10. [Mesclar o Adapter LoRA no Modelo Base](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#10-mesclar-o-adapter-lora-no-modelo-base)
11. [Compilar llama.cpp com Suporte ROCm](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#11-compilar-llamacpp-com-suporte-rocm)
12. [Converter para GGUF](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#12-converter-para-gguf)
13. [Quantizar o GGUF](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#13-quantizar-o-gguf)
14. [Testar o Modelo GGUF](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#14-testar-o-modelo-gguf)
15. [Integrar com Ollama (Opcional)](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#15-integrar-com-ollama-opcional)
16. [Monitoramento e Dicas](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#16-monitoramento-e-dicas)
17. [Solução de Problemas](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#17-solu%C3%A7%C3%A3o-de-problemas)
18. [Fluxo Resumido](https://claude.ai/chat/6d6e3247-0b77-4efd-aebb-8f0cdfab2599#18-fluxo-resumido)

***

## 1. Avisos Importantes

### ⚠️ RX 6600 — Suporte ROCm Não Oficial

A RX 6600 usa a arquitetura **RDNA 2, chip Navi 23 (gfx1032)**. O ROCm **não suporta oficialmente** este chip, mas é possível fazê-lo funcionar forçando um override de versão de GPU, pois o LLVM trata os alvos `gfx1030–gfx1036` de forma idêntica — gerando o mesmo código executável para todos eles.

A variável de ambiente necessária é:

```plain
HSA_OVERRIDE_GFX_VERSION=10.3.0
```

Isso instrui o ROCm a tratar a sua GPU como `gfx1030`, que é suportada oficialmente.

### ⚠️ CachyOS / Arch — Não é plataforma oficial do ROCm

A AMD só oferece suporte oficial para Ubuntu/RHEL. No Arch/CachyOS, os pacotes ROCm estão disponíveis nos repositórios oficiais do Arch e no AUR. Funciona bem, mas exige atenção com as versões dos pacotes.

***

## 2. Atualizar o Sistema

```plain
sudo pacman -Syu
```

Reinicie se houver atualização de kernel:

```plain
sudo reboot
```

***

## 3. Instalar ROCm no CachyOS

Os pacotes ROCm mais importantes estão nos repositórios oficiais do Arch. Use o `paru` (gerenciador de AUR recomendado no CachyOS) para o restante.

### 3.1 Instalar pacotes ROCm essenciais via pacman

```plain
sudo pacman -S --needed \
  rocm-hip-sdk \
  rocm-opencl-sdk \
  rocminfo \
  rocm-smi-lib \
  hip-runtime-amd \
  rocm-llvm
```

> **Nota:** No CachyOS o `paru` já vem disponível. Se não estiver, instale com:sudo pacman -S paru
> 

### 3.2 Adicionar usuário aos grupos necessários

```plain
sudo usermod -aG render,video $USER
```

**Faça logout e login novamente** (ou reinicie) para que as mudanças de grupo entrem em vigor:

```plain
# Verificar se os grupos foram aplicados
groups $USER
# Deve aparecer "render" e "video" na lista
```

***

## 4. Configurar Variáveis de Ambiente

Adicione as variáveis ao seu shell de login. Edite `~/.bashrc` (bash) ou `~/.zshrc` (zsh):

```plain
# === ROCm / RX 6600 (gfx1032 → override para gfx1030) ===
export HSA_OVERRIDE_GFX_VERSION=10.3.0
export ROCR_VISIBLE_DEVICES=0
export HIP_VISIBLE_DEVICES=0
export PYTORCH_HIP_ALLOC_CONF=max_split_size_mb:512

# Caminhos ROCm (Arch coloca em /opt/rocm)
export ROCM_PATH=/opt/rocm
export PATH="$ROCM_PATH/bin:$PATH"
export LD_LIBRARY_PATH="$ROCM_PATH/lib:$ROCM_PATH/lib64:$LD_LIBRARY_PATH"
```

Recarregue o shell:

```plain
source ~/.bashrc   # ou source ~/.zshrc
```

***

## 5. Verificar GPU

```plain
# Verificar se ROCm detecta a GPU
rocminfo

# Deve mostrar algo como:
# Name:                    gfx1032
# ou (com override ativo): gfx1030

# Verificar status da GPU
rocm-smi

# Testar acesso ao dispositivo
ls -la /dev/kfd /dev/dri/renderD128
```

Se a GPU aparecer na saída do `rocminfo`, o ambiente está configurado corretamente.

***

## 6. Criar Ambiente Python

Use um ambiente virtual isolado para evitar conflitos de pacotes:

```plain
# Instalar Python e virtualenv
sudo pacman -S --needed python python-pip

# Criar e ativar o ambiente virtual
python -m venv ~/lora-env
source ~/lora-env/bin/activate

# Atualizar pip
pip install --upgrade pip wheel setuptools
```

> **Importante:** Sempre ative o ambiente antes de trabalhar:source \~/lora-env/bin/activate
> 

***

## 7. Instalar PyTorch com ROCm

Use o wheel **ROCm 6.4**, que é a versão estável mais recente com suporte para `gfx1030` (nosso override):

```plain
pip install torch==2.9.1 torchvision==0.24.1 torchaudio==2.9.1 \
  --index-url https://download.pytorch.org/whl/rocm6.4
```

> **Versões verificadas em maio de 2026** via pytorch.org/get-started/previous-versions/. O PyTorch 2.9.x com ROCm 6.4 é o mais recente suporte estável disponível para `gfx1030/gfx1032`.

### Testar instalação

```plain
python -c "
import torch
print('PyTorch:', torch.__version__)
print('GPU disponível:', torch.cuda.is_available())
if torch.cuda.is_available():
    print('GPU:', torch.cuda.get_device_name(0))
    print('VRAM total:', round(torch.cuda.get_device_properties(0).total_memory / 1e9, 1), 'GB')
"
```

**Saída esperada:**

```plain
PyTorch: 2.9.1+rocm6.4
GPU disponível: True
GPU: AMD Radeon RX 6600
VRAM total: 8.0 GB
```

Se `GPU disponível` retornar `False`, confirme que `HSA_OVERRIDE_GFX_VERSION=10.3.0` está exportado na sessão atual.

***

## 8. Instalar Bibliotecas de LoRA

```plain
pip install \
  transformers \
  accelerate \
  peft \
  datasets \
  trl \
  safetensors \
  sentencepiece \
  protobuf
```

> **Sobre `bitsandbytes`:** O suporte ROCm do `bitsandbytes` (necessário para quantização int4/int8 durante o treino) é instável na RX 6600. **Não o instale.** Use `fp16` como dtype de treino — funciona perfeitamente dentro dos 8 GB de VRAM.

***

## 9. Treinar com LoRA

### 9.1 Escolha do modelo base

Para 8 GB de VRAM com `fp16`, use modelos com até \~3B parâmetros:

| Modelo | Parâmetros | VRAM estimada (fp16) |
| --- | --- | --- |
| `microsoft/phi-2` | 2.7B | \~6 GB ✅ |
| `Qwen/Qwen1.5-1.8B` | 1.8B | \~4 GB ✅ |
| `TinyLlama/TinyLlama-1.1B-Chat-v1.0` | 1.1B | \~3 GB ✅ |
| `mistralai/Mistral-7B-v0.1` | 7B | \~16 GB ❌ muito grande |

### 9.2 Script de treino (`train_lora.py`)

```plain
# train_lora.py
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments
from peft import LoraConfig, get_peft_model, TaskType
from trl import SFTTrainer, SFTConfig
from datasets import load_dataset
import torch

# ============================================================
# CONFIGURAÇÃO
# ============================================================
MODEL_NAME   = "microsoft/phi-2"   # Modelo base
ADAPTER_DIR  = "./lora-adapter"    # Onde salvar o adapter treinado
DATASET_NAME = "timdettmers/openassistant-guanaco"  # Substitua pelo seu dataset
# ============================================================

# Carregar tokenizer e modelo
print(f"Carregando modelo: {MODEL_NAME}")
tokenizer = AutoTokenizer.from_pretrained(
   MODEL_NAME,
   trust_remote_code=True
)
tokenizer.pad_token = tokenizer.eos_token  # necessário para batching

model = AutoModelForCausalLM.from_pretrained(
   MODEL_NAME,
   torch_dtype=torch.float16,    # fp16 — cabe nos 8 GB da RX 6600
   device_map="cuda",
   trust_remote_code=True
)

# Configurar LoRA
lora_config = LoraConfig(
   task_type=TaskType.CAUSAL_LM,
   r=16,               # rank: controla o número de parâmetros treináveis
   lora_alpha=32,      # escala do LoRA (geralmente = 2*r)
   lora_dropout=0.05,
   bias="none",
   target_modules=["q_proj", "v_proj"]  # camadas de atenção alvo
)


# Exemplo de saída: trainable params: 2,359,296 || all params: 2,781,278,208 (0.08%)

# Carregar dataset
print("Carregando dataset...")
dataset = load_dataset(DATASET_NAME, split="train[:50]")  # ajuste o tamanho

# Configurar treino
training_args = SFTConfig(
   output_dir="./checkpoints",
   num_train_epochs=1,
   per_device_train_batch_size=2,
   gradient_accumulation_steps=8,
   learning_rate=2e-4,
   fp16=True,
   logging_steps=10,
   save_steps=200,
   save_total_limit=2,
   warmup_steps=50,           # warmup_ratio foi depreciado também
   lr_scheduler_type="cosine",
   report_to="none",
   max_length=512,        # vai aqui agora
   dataset_text_field="text", # vai aqui agora
)

# Inicializar trainer
trainer = SFTTrainer(
   model=model,
   train_dataset=dataset,
   args=training_args,
   peft_config=lora_config,
)

# Iniciar treino
print("Iniciando treino...")
trainer.train()

# Salvar adapter
print(f"Salvando adapter em: {ADAPTER_DIR}")
trainer.model.save_pretrained(ADAPTER_DIR)
tokenizer.save_pretrained(ADAPTER_DIR)
print("Treino concluído!")
```

### 9.3 Executar o treino

**Sempre exporte o override antes de rodar:**

```plain
export HSA_OVERRIDE_GFX_VERSION=10.3.0
python train_lora.py
```

Ou na mesma linha:

```plain
HSA_OVERRIDE_GFX_VERSION=10.3.0 python train_lora.py
```

### 9.4 Monitorar uso de GPU durante o treino

Em outro terminal:

```plain
watch -n 1 rocm-smi
```

***

## 10. Mesclar o Adapter LoRA no Modelo Base

Antes de converter para GGUF, o adapter LoRA precisa ser **fundido (merged)** ao modelo base. O GGUF precisa de um modelo completo, não de um adapter separado.

Crie o arquivo `merge_lora.py`:

```plain
# merge_lora.py
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import PeftModel
import torch

MODEL_NAME   = "microsoft/phi-2"   # mesmo modelo do treino
ADAPTER_DIR  = "./lora-adapter"    # pasta gerada pelo treino
MERGED_DIR   = "./modelo-merged"   # destino do modelo fundido

print("Carregando modelo base (na CPU para economizar VRAM)...")
model = AutoModelForCausalLM.from_pretrained(
    MODEL_NAME,
    torch_dtype=torch.float16,
    device_map="cpu",              # CPU é mais seguro para o merge
    trust_remote_code=True
)

print("Carregando adapter LoRA...")
model = PeftModel.from_pretrained(model, ADAPTER_DIR)

print("Fundindo pesos LoRA no modelo base...")
model = model.merge_and_unload()   # funde e remove a estrutura PEFT

print(f"Salvando modelo fundido em: {MERGED_DIR}")
model.save_pretrained(MERGED_DIR)

tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME, trust_remote_code=True)
tokenizer.save_pretrained(MERGED_DIR)

print("Merge concluído! Estrutura salva:")
import os
for f in os.listdir(MERGED_DIR):
    print(f"  {MERGED_DIR}/{f}")
```

Executar:

```plain
python merge_lora.py
```

A pasta `./modelo-merged/` deve conter os arquivos `config.json`, `tokenizer.json`, `tokenizer_config.json` e os pesos (`.safetensors` ou `.bin`).

***

## 11. Compilar llama.cpp com Suporte ROCm

O llama.cpp é necessário tanto para converter o modelo para GGUF quanto para rodá-lo depois.

### 11.1 Instalar dependências

```plain
sudo pacman -S --needed cmake git base-devel curl libcurl-devel
```

### 11.2 Clonar o repositório

```plain
git clone https://github.com/ggml-org/llama.cpp ~/llama.cpp
cd ~/llama.cpp
```

### 11.3 Compilar com suporte HIP/ROCm para gfx1032

A documentação oficial do llama.cpp (verificada em 2025) inclui `gfx1032` na lista de alvos suportados:

```plain
cd ~/llama.cpp

# Exportar configuração HIP
export HIPCXX="$(hipconfig -l)/clang"
export HIP_PATH="$(hipconfig -R)"

# Compilar com suporte à RX 6600 (gfx1032)
cmake -S . -B build \
  -DGGML_HIP=ON \
  -DAMDGPU_TARGETS="gfx1030,gfx1032" \
  -DCMAKE_BUILD_TYPE=Release

cmake --build build --config Release -j$(nproc)
```

> **Se der erro de "ROCm device library not found"**, tente:HIP_DEVICE_LIB_PATH=$(find "$HIP_PATH" -name "oclc_abi_version_400.bc" -exec dirname {} ; | head -n 1)
> export HIP_DEVICE_LIB_PATH
> # Depois rode o cmake novamente
> 

### 11.4 Alternativa: compilar sem GPU (só CPU)

Se a compilação com ROCm falhar, compile somente para CPU — ainda funciona para converter e rodar modelos menores:

```plain
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j$(nproc)
```

### 11.5 Instalar dependências Python do llama.cpp

```plain
source ~/lora-env/bin/activate
pip install -r ~/llama.cpp/requirements.txt
```

Verifique que os binários foram criados:

```plain
ls ~/llama.cpp/build/bin/
# Deve listar: llama-cli, llama-quantize, llama-server, etc.
```

***

## 12. Converter para GGUF

Use o script `convert_hf_to_gguf.py` incluído no llama.cpp:

```plain
cd ~/llama.cpp

python convert_hf_to_gguf.py \
  ~/modelo-merged \
  --outfile ~/modelo-merged/modelo-f16.gguf \
  --outtype f16
```

> \*\*Parâmetros `--outtype`:\*\*ValorPrecisãoTamanhoRecomendação`f32`100%Muito grandeNão recomendado`f16`\~100%Grande✅ **Use para converter primeiro**`bf16`\~100%GrandeAlternativa ao f16`q8_0`AltaMédioBom se não quiser quantizar depois

**Recomendação:** Sempre converta primeiro para `f16` e depois quantize com `llama-quantize` — isso dá mais controle e flexibilidade.

Confirme que o arquivo foi criado:

```plain
ls -lh ~/modelo-merged/modelo-f16.gguf
```

***

## 13. Quantizar o GGUF

A quantização reduz significativamente o tamanho do arquivo e o uso de VRAM/RAM, com impacto mínimo na qualidade para os formatos `K` (k-quant).

```plain
cd ~/llama.cpp

./build/bin/llama-quantize \
  ~/modelo-merged/modelo-f16.gguf \
  ~/modelo-merged/modelo-q4km.gguf \
  Q4_K_M
```

### Tabela de quantizações disponíveis

| Formato | Bits | Tamanho relativo | Qualidade | Recomendação |
| --- | --- | --- | --- | --- |
| `Q2_K` | \~2 | Mínimo | Baixa | Só em extrema necessidade |
| `Q4_0` | 4 | Pequeno | Boa | Rápido, mas K-quants são melhores |
| `Q4_K_M` | 4 | Pequeno | Boa ✅ | **Melhor custo-benefício** |
| `Q5_K_M` | 5 | Médio | Muito boa ✅ | Boa se tiver RAM disponível |
| `Q6_K` | 6 | Grande | Excelente | Próximo ao original |
| `Q8_0` | 8 | Maior | Quase original | Se não se importar com tamanho |

**Para a RX 6600 com 8 GB, `Q4_K_M` é o formato ideal** — maximiza a quantidade de camadas que cabem na VRAM.

Você pode criar múltiplos formatos:

```plain
# Q4_K_M — uso diário
./build/bin/llama-quantize \
  ~/modelo-merged/modelo-f16.gguf \
  ~/modelo-merged/modelo-q4km.gguf Q4_K_M

# Q5_K_M — melhor qualidade
./build/bin/llama-quantize \
  ~/modelo-merged/modelo-f16.gguf \
  ~/modelo-merged/modelo-q5km.gguf Q5_K_M
```

***

## 14. Testar o Modelo GGUF

### 14.1 Teste direto via llama-cli

```plain
cd ~/llama.cpp

HSA_OVERRIDE_GFX_VERSION=10.3.0 \
./build/bin/llama-cli \
  -m ~/modelo-merged/modelo-q4km.gguf \
  -p "Explique o que é LoRA em machine learning:" \
  -n 300 \
  --gpu-layers 32   # número de camadas na GPU; aumente até encher a VRAM
```

> **Ajuste `--gpu-layers`:** Comece com 20, aumente gradualmente enquanto `rocm-smi` mostrar VRAM disponível. Para modelos de \~3B com Q4_K_M, você provavelmente consegue offlodar todas as camadas.

### 14.2 Servidor HTTP (API OpenAI-compatível)

```plain
HSA_OVERRIDE_GFX_VERSION=10.3.0 \
./build/bin/llama-server \
  -m ~/modelo-merged/modelo-q4km.gguf \
  --host 0.0.0.0 \
  --port 8080 \
  --gpu-layers 32
```

Acesse `http://localhost:8080` no navegador para a interface web, ou use a API:

```plain
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "meu-modelo",
    "messages": [{"role": "user", "content": "Olá! O que você sabe sobre LoRA?"}]
  }'
```

### 14.3 Usar o GGUF em Python (via llama-cpp-python)

```plain
pip install llama-cpp-python
```

```plain
from llama_cpp import Llama

llm = Llama(
    model_path="/home/seu_usuario/modelo-merged/modelo-q4km.gguf",
    n_gpu_layers=32,   # camadas na GPU
    n_ctx=2048         # contexto
)

output = llm(
    "Explique LoRA em uma frase:",
    max_tokens=200,
    echo=True
)
print(output["choices"][0]["text"])
```

***

## 15. Integrar com Ollama (Opcional)

O Ollama oferece uma interface mais amigável para gerenciar e rodar modelos GGUF:

### 15.1 Instalar Ollama

```plain
# Via AUR
paru -S ollama-rocm
```

### 15.2 Configurar o serviço com o override

Edite o serviço systemd do Ollama:

```plain
sudo systemctl edit ollama.service
```

Adicione:

```plain
[Service]
Environment="HSA_OVERRIDE_GFX_VERSION=10.3.0"
Environment="ROCR_VISIBLE_DEVICES=0"
```

Reinicie:

```plain
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

### 15.3 Registrar o modelo GGUF no Ollama

Crie um `Modelfile`:

```plain
cat > ~/Modelfile << 'EOF'
FROM /home/seu_usuario/modelo-merged/modelo-q4km.gguf

PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER num_gpu 32

SYSTEM """Você é um assistente útil e preciso."""
EOF
```

Registrar e rodar:

```plain
ollama create meu-modelo-lora -f ~/Modelfile
ollama run meu-modelo-lora
```

***

## 16. Monitoramento e Dicas

### Monitorar GPU em tempo real

```plain
# Opção 1: rocm-smi (básico, vem com ROCm)
watch -n 1 rocm-smi

# Opção 2: amdgpu_top (mais detalhado)
paru -S amdgpu_top
amdgpu_top
```

### Dicas para economizar VRAM (8 GB)

| Situação | Solução |
| --- | --- |
| `RuntimeError: out of memory` | Reduza `per_device_train_batch_size` para 1 |
| Treino muito lento | Aumente `gradient_accumulation_steps` para 16 |
| Modelo não cabe | Use modelo menor (Phi-2, Qwen-1.5-1.8B, TinyLlama) |
| Inferência lenta | Reduza `--gpu-layers` para liberar VRAM para o KV cache |
| Erro de bitsandbytes | Não instale; use `fp16=True` no treino |

### Ajuste fino dos parâmetros LoRA

| Parâmetro | Valor baixo | Valor alto | Recomendação para 8 GB |
| --- | --- | --- | --- |
| `r` (rank) | Menos params, mais rápido | Mais params, mais lento | `r=8` ou `r=16` |
| `lora_alpha` | — | — | `lora_alpha = 2 * r` |
| `max_seq_length` | Menos VRAM | Mais VRAM | `512` ou `1024` |
| `batch_size` | Menos VRAM | Mais VRAM | `1` ou `2` |

***

## 17. Solução de Problemas

### GPU não detectada pelo PyTorch

```plain
# Verificar se o override está ativo
echo $HSA_OVERRIDE_GFX_VERSION
# Deve retornar: 10.3.0

# Verificar grupos do usuário
groups
# Deve incluir: render video

# Verificar dispositivos
ls -la /dev/kfd /dev/dri/renderD*
```

### Erro "hipErrorNoBinaryForGpu"

O override não está sendo aplicado. Certifique-se de que `HSA_OVERRIDE_GFX_VERSION=10.3.0` é exportado **antes** de qualquer chamada Python ou de ROCm.

### Erro na compilação do llama.cpp — "cannot find ROCm device library"

```plain
# Encontrar o diretório correto
find "$HIP_PATH" -name "oclc_abi_version_400.bc" 2>/dev/null

# Exportar o caminho encontrado
export HIP_DEVICE_LIB_PATH=/caminho/encontrado/acima

# Recompilar
cmake -S . -B build -DGGML_HIP=ON -DAMDGPU_TARGETS="gfx1030,gfx1032" \
  -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j$(nproc)
```

### Erro na conversão GGUF — "Model architecture not supported"

Nem todos os modelos HuggingFace são suportados pelo `convert_hf_to_gguf.py`. Os modelos suportados incluem LLaMA, Mistral, Phi, Qwen, Gemma, entre outros. Verifique a lista atualizada no [repositório do llama.cpp](https://github.com/ggml-org/llama.cpp).

### Treino muito lento (sem GPU)

```plain
# Verificar se o PyTorch está usando a GPU
python -c "
import torch
print(torch.cuda.is_available())
x = torch.tensor([1.0]).cuda()
print(x.device)  # Deve retornar: cuda:0
"
```

***

## 18. Fluxo Resumido

```plain
┌─────────────────────────────────────────────────────────────────┐
│                        PIPELINE COMPLETO                        │
└─────────────────────────────────────────────────────────────────┘

  [1] TREINO LoRA
      train_lora.py
      ↓
      lora-adapter/
        ├── adapter_config.json
        └── adapter_model.safetensors

  [2] MERGE
      merge_lora.py
      ↓
      modelo-merged/
        ├── config.json
        ├── tokenizer.json
        └── model.safetensors

  [3] CONVERSÃO GGUF
      convert_hf_to_gguf.py
      ↓
      modelo-merged/
        └── modelo-f16.gguf        (~5 GB para Phi-2)

  [4] QUANTIZAÇÃO
      llama-quantize
      ↓
      modelo-merged/
        ├── modelo-q4km.gguf       (~1.6 GB para Phi-2) ✅ recomendado
        └── modelo-q5km.gguf       (~2.0 GB para Phi-2)

  [5] USO
      ├── llama-cli (terminal)
      ├── llama-server (API HTTP)
      ├── ollama run (interface amigável)
      └── llama-cpp-python (código Python)
```

***

## Referências

- [ROCm Documentation — PyTorch Install](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/3rd-party/pytorch-install.html)
- [PyTorch — Previous Versions (ROCm wheels)](https://pytorch.org/get-started/previous-versions/)
- [llama.cpp — Build Documentation](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md)
- [llama.cpp — ROCm Installation (ROCm Docs)](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/3rd-party/llama-cpp-install.html)
- [ROCm for Arch Linux — AUR/GitHub](https://github.com/rocm-arch/rocm-arch)
- [HSA_OVERRIDE_GFX_VERSION — ROCm/ROCm Issue #1698](https://github.com/ROCm/ROCm/issues/1698)
- [Ollama — AMD GPU Overrides](https://github.com/ollama/ollama/blob/main/docs/gpu.md)
- [HuggingFace PEFT — LoRA Docs](https://huggingface.co/docs/peft/conceptual_guides/lora)

***

_Guia compilado com informações verificadas em fontes oficiais em maio de 2026._ _Versões principais: ROCm 6.4 · PyTorch 2.9.1 · llama.cpp master · PEFT/TRL mais recentes via pip._
