---
title: 'Guia Completo: Pacman e Yay'
slug: guia-pacman-yay
description: Aprenda a usar pacman, yay, AUR, atualizar o sistema, resolver problemas comuns e extrair o máximo desempenho do seu Arch
summary: Aprenda a usar pacman, yay, AUR, atualizar o sistema, resolver problemas comuns e extrair o máximo desempenho do seu Arch
tags:
  - pacman
  - yay
  - cachyos
  - aur
categories:
  - cachyos
keywords: []
author: Gabriel Maggioni
date: 2026-06-11T14:48:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

## Introdução: O que é o CachyOS

O CachyOS é uma distribuição Linux baseada no Arch Linux, criada com foco em desempenho extremo, baixa latência e uma experiência mais moderna já pronta para uso.

Enquanto o Arch tradicional entrega um sistema minimalista que exige configuração manual quase do zero, o CachyOS pega essa base poderosa e adiciona uma camada enorme de otimizações. O objetivo é simples: fazer o sistema responder mais rápido, aproveitar melhor o hardware e reduzir várias das dores comuns de configuração do Arch puro.

Uma das características mais conhecidas do CachyOS é o uso de kernels altamente otimizados, compilados com instruções modernas de CPU, schedulers ajustados para responsividade e tweaks agressivos de performance. Em muitos cenários, principalmente em jogos 🎮, compilação de código, multitarefa pesada e uso diário no desktop, a sensação do sistema fica surpreendentemente “leve” e instantânea.

Ele também traz:

- instalador gráfico amigável
- suporte facilitado a drivers NVIDIA
- integração com Btrfs e snapshots
- gerenciadores de pacotes otimizados
- várias opções de kernel
- ferramentas próprias de tuning
- interface refinada e visual moderno

Outro ponto interessante é que o CachyOS mantém a filosofia rolling release do Arch. Isso significa atualizações contínuas, sem precisar reinstalar o sistema a cada nova versão grande. Você recebe kernels novos, drivers recentes e softwares atualizados constantemente.

Na prática, ele fica meio que entre dois mundos:

| Sistema | Perfil |
| --- | --- |
| Arch Linux | controle absoluto e minimalismo |
| CachyOS | Arch otimizado, pronto e mais confortável |
| EndeavourOS | Arch próximo do vanilla |
| Garuda Linux | foco visual e automações pesadas |

O CachyOS acabou ganhando bastante popularidade entre usuários avançados, gamers Linux 🐧 e pessoas que gostam de extrair o máximo possível do hardware sem precisar passar horas configurando tudo manualmente.

***

### Características principais

- **Kernel otimizado:** O CachyOS distribui kernels pré-compilados com patches de performance.
- **Repositórios próprios:** Pacotes recompilados com otimizações para arquiteturas modernas (`x86-64-v3`, `x86-64-v4` para CPUs com AVX512).
- **Baseado em Arch:** Totalmente compatível com o ecossistema Arch, incluindo AUR, pacman e todas as ferramentas.
- **Rolling release:** Atualizações contínuas, sem versões fixas. Você sempre tem o software mais recente.
- **GUI amigável:** Instalador gráfico (Calamares), Hello App para configuração inicial, e gerenciador de pacotes gráfico opcional.

***

## Por que usar o CachyOS?

### ✅ Prós

- **Performance real e mensurável:** Jogos e aplicações respondem melhor por causa do scheduler de kernel otimizado.
- **Pacotes recompilados para sua CPU:** Se você tem uma CPU moderna, os binários do repositório CachyOS são compilados especificamente para ela.
- **Acesso ao AUR:** Todo o repositório de usuários do Arch Linux está disponível.
- **Controle total:** Como qualquer Arch-based, você controla cada pacote instalado no sistema.
- **Comunidade ativa:** Fórum, Discord e Wiki próprios, além de herdar toda a base da ArchWiki.

### ⚠️ Pontos de atenção

- **Rolling release requer atenção:** Atualizações constantes significam que, ocasionalmente, algo pode quebrar.
- **Não é para quem quer "instalar e esquecer":** É preciso manter o sistema, ler os avisos de atualização e ter uma certa intimidade com o terminal.
- **AUR é poderoso, mas não auditado:** Pacotes do AUR são mantidos pela comunidade; sempre revise o `PKGBUILD` antes de instalar.

***

## Gerenciadores de Pacotes

O CachyOS usa dois sistemas principais:

| Ferramenta | Repositório | Quem mantém |
| --- | --- | --- |
| `pacman` | Arch oficial + CachyOS | Arch / CachyOS Team |
| `yay` | AUR (Arch User Repository) | Comunidade (PKGBUILDs) |

> **Regra de ouro:** Prefira sempre `pacman` para pacotes dos repositórios oficiais. Use `yay` somente quando o pacote não está nos repositórios oficiais.

***

## Pacman - Guia Completo

O `pacman` é o gerenciador de pacotes nativo do Arch Linux e por extensão do CachyOS.

### Sintaxe geral

```bash
pacman <operação> [opções] [pacotes]
```

As principais operações são:

- `-S` → Sincronizar (instalar/atualizar)
- `-R` → Remover
- `-Q` → Consultar (pacotes locais)
- `-F` → Procurar em arquivos
- `-D` → Banco de dados (alterar metadados)
- `-U` → Atualizar/instalar de arquivo local

***

### 📦 Instalar pacotes

```bash
# Instalar um pacote
sudo pacman -S nome-do-pacote

# Instalar múltiplos pacotes
sudo pacman -S pacote1 pacote2 pacote3

# Instalar sem confirmar (assume yes)
sudo pacman -S --noconfirm nome-do-pacote

# Reinstalar um pacote já instalado
sudo pacman -S nome-do-pacote  # Pacman detecta e oferece reinstalar

# Instalar de um arquivo .pkg.tar.zst local
sudo pacman -U /caminho/para/pacote.pkg.tar.zst

# Instalar de URL
sudo pacman -U https://exemplo.com/pacote.pkg.tar.zst
```

***

### 🔄 Atualizar o sistema

```bash
# Sincronizar repositórios e atualizar tudo (USE ISSO REGULARMENTE)
sudo pacman -Syu

# Forçar re-sincronização dos repositórios antes de atualizar
sudo pacman -Syyu

# Atualizar apenas um pacote específico
sudo pacman -S nome-do-pacote  # já atualiza se houver nova versão

# Ver o que seria atualizado sem instalar (simulação)
sudo pacman -Syu --print

# Atualizar ignorando um pacote específico
sudo pacman -Syu --ignore nome-do-pacote

# Ignorar múltiplos pacotes
sudo pacman -Syu --ignore pacote1,pacote2
```

***

### 🗑️ Remover pacotes

```bash
# Remover um pacote (mantém dependências)
sudo pacman -R nome-do-pacote

# Remover pacote + dependências não usadas por outros pacotes
sudo pacman -Rs nome-do-pacote

# Remover pacote + dependências + arquivos de configuração
sudo pacman -Rns nome-do-pacote

# Remover pacote ignorando dependências (PERIGOSO - use com cuidado)
sudo pacman -Rdd nome-do-pacote

# Remover orfãos (dependências sem "pai") - manutenção essencial
sudo pacman -Rns $(pacman -Qtdq)
```

***

### 🔍 Buscar e consultar pacotes

```bash
# Buscar pacote nos repositórios (por nome ou descrição)
pacman -Ss nome-ou-termo

# Buscar pacote instalado localmente
pacman -Qs nome-do-pacote

# Ver informações detalhadas de um pacote nos repositórios
pacman -Si nome-do-pacote

# Ver informações de um pacote instalado
pacman -Qi nome-do-pacote

# Listar todos os pacotes instalados
pacman -Q

# Listar apenas pacotes instalados explicitamente (não como dependência)
pacman -Qe

# Listar pacotes instalados como dependências
pacman -Qd

# Listar pacotes orfãos (instalados como deps, mas sem "pai")
pacman -Qdt

# Ver todos os arquivos de um pacote instalado
pacman -Ql nome-do-pacote

# Descobrir a qual pacote um arquivo pertence
pacman -Qo /caminho/para/arquivo

# Descobrir qual pacote fornece um arquivo (repositório)
pacman -F nome-do-arquivo

# Buscar todos os pacotes do grupo X
pacman -Sg nome-do-grupo

# Listar pacotes de um repositório específico
pacman -Sl cachyos
pacman -Sl extra
```

***

### 🧹 Limpeza e manutenção

```bash
# Limpar cache de pacotes baixados (mantém versões atuais)
sudo pacman -Sc

# Limpar TODO o cache (versões antigas e atuais)
sudo pacman -Scc

# Ver tamanho do cache
du -sh /var/cache/pacman/pkg/

# Remover entradas de banco de dados sem instalação correspondente
sudo pacman -Rns $(pacman -Qtdq)
```

***

### 🔒 Downgrade de pacotes

```bash
# Instalar versão específica do cache local
sudo pacman -U /var/cache/pacman/pkg/nome-do-pacote-versao-x86_64.pkg.tar.zst

# Ver versões disponíveis no cache
ls /var/cache/pacman/pkg/ | grep nome-do-pacote

# Usar downgrade (ferramenta do AUR, mais fácil)
sudo downgrade nome-do-pacote
```

***

### ⚙️ Configuração do pacman

O arquivo de configuração fica em `/etc/pacman.conf`:

```bash
sudo nano /etc/pacman.conf
```

**Opções úteis para ativar:**

```bash
# Habilitar downloads paralelos (muito mais rápido)
ParallelDownloads = 5

# Habilitar cores no terminal
Color

# Mostrar barra de progresso estilo Pacman 🕹️
ILoveCandy

# Manter N versões no cache (recomendo 2 ou 3 para downgrades)
# (configurado no pacman-contrib com paccache)
```

***

### Mirrorlist - escolhendo os espelhos mais rápidos

```bash
# Ver mirrors atuais
cat /etc/pacman.d/mirrorlist

# Atualizar mirrors com reflector (instale se não tiver)
sudo pacman -S reflector

# Gerar nova mirrorlist com os 10 mirrors mais rápidos do Brasil
sudo reflector --country Brazil --latest 10 --sort rate --save /etc/pacman.d/mirrorlist

# Gerar com múltiplos países
sudo reflector --country Brazil,Germany --latest 15 --sort rate --save /etc/pacman.d/mirrorlist
```

***

## Yay - AUR Helper Completo

O `yay` (Yet Another Yogurt) é um AUR helper escrito em Go que se comporta exatamente como o `pacman`, mas também resolve pacotes do AUR (Arch User Repository).

> ⚠️ **Aviso:** Nunca use `sudo yay`. O yay precisa de permissão de usuário para clonar PKGBUILDs e usa `sudo` internamente apenas quando necessário.

### Instalando o yay (já vem no CachyOS)

```bash
# Se por algum motivo não estiver instalado:
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

***

### 📦 Instalar pacotes com yay

```bash
# Instalar do AUR ou dos repositórios (yay busca nos dois)
yay -S nome-do-pacote

# Instalar sem confirmar nada (cuidado com isso!)
yay -S --noconfirm nome-do-pacote

# Instalar múltiplos pacotes
yay -S pacote1 pacote2

# Forçar rebuild de um pacote AUR
yay -S nome-do-pacote --rebuildall

# Instalar apenas de repositórios oficiais (ignora AUR)
yay -S --aur=false nome-do-pacote
```

***

### 🔄 Atualizar com yay

```bash
# Atualizar tudo: repositórios oficiais + AUR
yay -Syu

# Atualizar apenas pacotes do AUR
yay -Sua

# Atualizar apenas pacotes dos repositórios (equivalente ao pacman -Syu)
yay -Syu --aur=false

# Ver o que seria atualizado (simulação)
yay -Syu --dryrun

# Atualizar ignorando um pacote
yay -Syu --ignore nome-do-pacote
```

***

### 🔍 Buscar com yay

```bash
# Buscar nos repositórios e no AUR
yay -Ss nome-ou-termo

# Buscar apenas no AUR
yay -Ss nome --aur

# Ver informações de um pacote AUR
yay -Si nome-do-pacote

# Ver informações de pacote instalado
yay -Qi nome-do-pacote
```

***

### 🗑️ Remover com yay

O yay usa a mesma sintaxe do pacman para remoção:

```bash
# Remover pacote + dependências não usadas
yay -Rns nome-do-pacote

# Remover orfãos
yay -Yc

# Ver orfãos sem remover
yay -Qdt
```

***

### 📋 Revisando PKGBUILDs antes de instalar

Isso é uma boa prática de segurança:

```bash
# Ao instalar, yay perguntará se deseja revisar o PKGBUILD
yay -S nome-do-pacote
# → Aparecerá: "View PKGBUILD? [Y/n]" - pressione Y e revise!

# Para que yay SEMPRE mostre o diff das mudanças
yay --editmenu -S nome-do-pacote

# Configurar yay para sempre pedir revisão
yay --save --editmenu
```

***

### ⚙️ Configuração do yay

```bash
# Ver configurações atuais
yay --show --config

# Configurar editor para revisão de PKGBUILDs
yay --save --editor nvim

# Desabilitar confirmação do PKGBUILD (não recomendado)
yay --save --nocleanmenu --nodiffmenu

# Habilitar a limpeza automática depois de builds
yay --save --cleanafter

# Ver estatísticas de uso do yay
yay -Ps
```

***

### 🔐 Verificar pacotes AUR manualmente

```bash
# Clonar e revisar manualmente antes de instalar
git clone https://aur.archlinux.org/nome-do-pacote.git
cd nome-do-pacote
cat PKGBUILD     # Revise este arquivo com atenção
makepkg -si      # Compila e instala
```

***

## Workflow Diário

### Rotina semanal recomendada

```bash
# 1. Sincronizar repositórios e atualizar tudo
yay -Syu

# 2. Remover orfãos
yay -Yc

# 3. Limpar cache antigo (mantém 2 versões recentes)
sudo paccache -rk2

# 4. Verificar integridade do banco de dados
sudo pacman -Dk  # Verifica dependências
```

### Fluxo completo de instalação responsável

```bash
# 1. Buscar o pacote
yay -Ss firefox

# 2. Ver detalhes antes de instalar
yay -Si firefox

# 3. Instalar (revisando o PKGBUILD se for AUR)
yay -S firefox

# 4. Verificar instalação
yay -Qi firefox

# 5. Ver arquivos instalados
yay -Ql firefox
```

***

## 🆘 O que fazer quando algo dá errado

### Erro durante atualização

**Sintoma:** `sudo pacman -Syu` retorna erro de conflito de arquivos ou dependências

```bash
# Ver o erro completo
sudo pacman -Syu 2>&1 | tee /tmp/pacman-update.log

# Erro de "file conflict" - checar qual pacote possui o arquivo
pacman -Qo /caminho/do/arquivo/em/conflito

# Forçar sobrescrever arquivo em conflito (use com cuidado)
sudo pacman -Syu --overwrite "/caminho/do/arquivo"

# Conflito de dependências - tentar resolver instalando dependência primeiro
sudo pacman -S dependencia-faltante
```

***

### Banco de dados corrompido

```bash
# Sintoma: "could not open file /var/lib/pacman/sync/*.db"

# Remover e re-sincronizar
sudo rm /var/lib/pacman/sync/*.db
sudo pacman -Syyu
```

***

### Lock do pacman (outro processo usando)

```bash
# Sintoma: "error: could not lock database"

# Verificar se pacman ou yay está rodando
ps aux | grep -E "pacman|yay"

# Se não estiver rodando, remover o lock manualmente
sudo rm /var/lib/pacman/db.lck
```

***

### Sistema não inicializa após atualização de kernel

```bash
# Na tela do GRUB, selecione uma entrada mais antiga
# Depois de inicializar:

# Ver kernels disponíveis
pacman -Q | grep linux

# Instalar kernel estável como fallback
sudo pacman -S linux linux-headers

# Gerar nova configuração do GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

***

### Reverter uma atualização problemática

```bash
# Ver histórico de operações do pacman
less /var/log/pacman.log

# Filtrar apenas instalações/upgrades recentes
grep -E "\[ALPM\] (upgraded|installed)" /var/log/pacman.log | tail -50

# Fazer downgrade de um pacote pelo cache
ls /var/cache/pacman/pkg/ | grep nome-do-pacote
sudo pacman -U /var/cache/pacman/pkg/nome-do-pacote-versao-anterior.pkg.tar.zst

# Usando a ferramenta downgrade (mais prática)
sudo pacman -S downgrade  # instala do AUR via yay
sudo downgrade nome-do-pacote
```

***

### Pacote corrompido ou incompleto

```bash
# Verificar integridade de todos os pacotes instalados
sudo pacman -Qkk 2>&1 | grep -v " 0 altered"

# Reinstalar um pacote específico
sudo pacman -S nome-do-pacote

# Reinstalar todos os pacotes com arquivos alterados
sudo pacman -Qkk 2>&1 | grep " altered" | awk '{print $1}' | xargs sudo pacman -S
```

***

### Pacote AUR falhou ao compilar

```bash
# Ver o erro completo de compilação
yay -S nome-do-pacote 2>&1 | tee /tmp/yay-build.log

# Tentar recompilar limpando o cache de build
yay -S nome-do-pacote --rebuild

# Compilar manualmente para ver erros detalhados
git clone https://aur.archlinux.org/nome-do-pacote.git
cd nome-do-pacote
makepkg -si --noconfirm
```

***

### Resolver conflitos de chaves GPG

```bash
# Erro: "invalid or corrupted package (PGP signature)"

# Atualizar chaveiro
sudo pacman-key --refresh-keys

# Inicializar chaveiro (se nunca foi feito)
sudo pacman-key --init

# Importar chave específica
sudo pacman-key --recv-keys CHAVE_ID
sudo pacman-key --lsign-key CHAVE_ID

# Reinstalar chaveiro do Arch
sudo pacman -S archlinux-keyring
```

***

## 👁️ Ver o que vai mudar ANTES de atualizar

Esta é uma das habilidades mais importantes para um usuário de rolling release.

### Ver pacotes que serão atualizados

```bash
# Listar atualizações disponíveis sem instalar
checkupdates

# Com AUR incluído
yay -Qu

# Com detalhes de versão (de X para Y)
yay -Syu --print

# Forma verbosa
pacman -Syu --print-format "%n: %v -> %l"
```

***

### Ler os news do Arch ANTES de atualizar

**Este é o passo mais importante para evitar problemas!**

```bash
# Instalar arch-news (exibe news do Arch direto no terminal)
yay -S arch-news

# Ver notícias do Arch
arch-news

# Alternativa: ver direto no site
# https://archlinux.org/news/
```

> 💡 **Dica crítica:** O site [https://archlinux.org/news/](https://archlinux.org/news/) publica **avisos de ações manuais necessárias** antes de grandes atualizações. Se você atualizar sem ler, pode quebrar o sistema.

***

### CachyOS Changelog e anúncios

```bash
# Ver o repositório do CachyOS no GitHub
# https://github.com/CachyOS

# Discord do CachyOS (anúncios de updates importantes)
# https://discord.gg/cachyos-862292009423470592

# Fórum do CachyOS
# https://discuss.cachyos.org/
```

***

### Simular uma atualização completa

```bash
# Ver tudo que seria instalado, atualizado ou removido - sem executar
sudo pacman -Syu --print

# Checar dependências sem instalar
sudo pacman -Sp nome-do-pacote  # lista URL dos pacotes necessários

# Ver conflitos antes de instalar
sudo pacman -S nome-do-pacote --print
```

***

### Antes de atualizar - checklist

```bash
✅ 1. Ler https://archlinux.org/news/ - avisos de ações manuais
✅ 2. Rodar `checkupdates` - ver o que vai mudar
✅ 3. Ter um snapshot/backup (Timeshift, Btrfs snapshot, etc.)
✅ 4. Não atualizar antes de uma apresentação ou trabalho importante
✅ 5. Ter acesso ao TTY caso a interface gráfica quebre (Ctrl+Alt+F2)
```

***

## 📚 Como ler a documentação

### ArchWiki - a melhor documentação Linux

A ArchWiki é válida para o CachyOS em praticamente tudo que não é específico do CachyOS:

```bash
https://wiki.archlinux.org/
```

**Páginas essenciais para marcar:**

- [Pacman](https://wiki.archlinux.org/title/Pacman) - documentação completa do pacman
- [AUR](https://wiki.archlinux.org/title/Arch_User_Repository) - como funciona o AUR
- [System maintenance](https://wiki.archlinux.org/title/System_maintenance) - guia de manutenção de sistema rolling
- [Pacman/Rosetta](https://wiki.archlinux.org/title/Pacman/Rosetta) - comparação de comandos com apt, dnf, etc.
- [General recommendations](https://wiki.archlinux.org/title/General_recommendations) - após instalação

***

### Wiki do CachyOS

```bash
https://wiki.cachyos.org/
```

**Tópicos específicos do CachyOS:**

- Kernels disponíveis e como trocar
- Repositórios CachyOS (`cachyos`, `cachyos-extra`, `cachyos-v3`, `cachyos-v4`)
- BORE scheduler e como configurar
- Hardware específico

***

### Man pages - documentação offline

```bash
# Ver manual do pacman
man pacman

# Ver manual do yay
yay --help
man yay   # se disponível

# Buscar na man page
man pacman
# Dentro do man, use /termo para buscar, n para próxima ocorrência
```

***

### Verificar logs do sistema para diagnóstico

```bash
# Ver log completo do sistema (systemd journal)
journalctl -xe

# Ver logs de boot
journalctl -b

# Ver logs do boot anterior (quando o sistema travou)
journalctl -b -1

# Filtrar por serviço específico
journalctl -u nome-do-servico

# Ver logs em tempo real
journalctl -f

# Log do pacman (todas as operações já realizadas)
cat /var/log/pacman.log
less /var/log/pacman.log

# Filtrar log do pacman por data
grep "2024-05" /var/log/pacman.log
```

***

## 🌟 Extras e Dicas Avançadas

### Kernels disponíveis no CachyOS

O CachyOS é famoso por seus kernels otimizados. Você pode trocar facilmente:

```bash
# Ver kernels disponíveis
pacman -Ss linux-cachyos

# Instalar kernel com scheduler BORE+EEVDF (padrão recomendado)
sudo pacman -S linux-cachyos linux-cachyos-headers

# Kernel RT (Real-Time) para áudio profissional
sudo pacman -S linux-cachyos-rt linux-cachyos-rt-headers

# Kernel com suporte a Hardened (segurança)
sudo pacman -S linux-cachyos-hardened

# Kernel puro do Arch (fallback estável)
sudo pacman -S linux linux-headers

# Após instalar novo kernel, atualizar GRUB
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

***

### Btrfs + Snapper - Snapshots automáticos

Se você instalou com Btrfs (padrão no CachyOS), você pode configurar snapshots automáticos:

```bash
# Instalar snapper e snap-pac (snapshots automáticos no pacman)
sudo pacman -S snapper snap-pac

# Criar configuração para raiz
sudo snapper -c root create-config /

# Ver snapshots
sudo snapper -c root list

# Criar snapshot manual antes de uma atualização arriscada
sudo snapper -c root create --description "antes-de-atualizar"

# Reverter para snapshot (em caso de desastre)
# Inicialize pelo live USB, monte a partição btrfs e:
sudo btrfs subvolume list /
# Renomeie subvolumes conforme necessário
```

***

### Timeshift - backup e restauração simplificada

```bash
# Instalar
sudo pacman -S timeshift

# Criar snapshot agora
sudo timeshift --create --comments "estado estável"

# Listar snapshots
sudo timeshift --list

# Restaurar snapshot
sudo timeshift --restore
```

***

### paccache - limpeza inteligente de cache

```bash
# Instalar pacman-contrib (vem com paccache)
sudo pacman -S pacman-contrib

# Manter 3 versões de cada pacote no cache
sudo paccache -rk3

# Manter 1 versão (economiza espaço)
sudo paccache -rk1

# Remover apenas versões não instaladas
sudo paccache -ruk0

# Habilitar limpeza automática semanal
sudo systemctl enable --now paccache.timer
```

***

### Aliases úteis para o .bashrc ou .zshrc

Adicione ao seu `~/.bashrc`, `~/.zshrc` ou `/.config/fish/config.fish`:
(use `echo $SHELL` caso não souber qual o seu shell);

```bash
# Atualização completa
alias update='yay -Syu'

# Limpeza total
alias cleanup='yay -Yc && sudo paccache -rk2'

# Ver atualizações disponíveis
alias updates='checkupdates && yay -Qua'

# Log do pacman resumido
alias paclog='grep -E "\[ALPM\] (upgraded|installed|removed)" /var/log/pacman.log | tail -20'

# Busca rápida
alias search='yay -Ss'

# Informações de pacote
alias info='yay -Si'

# Remover orfãos
alias orphans='sudo pacman -Rns $(pacman -Qtdq) 2>/dev/null || echo "Nenhum orfão."'
```

> não esqueça de recarregar as settings do shell com `source /.config/fish/config.fish` ou equivalente.

***

### Gerenciar serviços com systemctl

```bash
# Ver status de um serviço
systemctl status nome-do-servico

# Iniciar serviço
sudo systemctl start nome-do-servico

# Habilitar na inicialização
sudo systemctl enable nome-do-servico

# Habilitar e iniciar de uma vez
sudo systemctl enable --now nome-do-servico

# Reiniciar serviço
sudo systemctl restart nome-do-servico

# Listar todos os serviços ativos
systemctl list-units --type=service --state=active

# Ver serviços com falha
systemctl --failed
```

***

### Verificar o hardware detectado

```bash
# Informações de CPU
lscpu
cat /proc/cpuinfo | grep "model name" | head -1

# Memória
free -h

# Dispositivos PCI (placas de vídeo, rede, etc.)
lspci

# Dispositivos USB
lsusb

# Discos e partições
lsblk
df -h

# Informações de GPU
lspci | grep -i vga
glxinfo | grep "OpenGL renderer"

# Temperatura e sensores
sensors  # requer lm_sensors: sudo pacman -S lm_sensors
```

***

### Recursos online essenciais

| Recurso | URL | Para que serve |
| --- | --- | --- |
| ArchWiki | https://wiki.archlinux.org | Documentação principal |
| CachyOS Wiki | https://wiki.cachyos.org | Específico do CachyOS |
| Arch News | https://archlinux.org/news/ | Avisos de atualizações |
| AUR | https://aur.archlinux.org | Buscar pacotes AUR |
| CachyOS GitHub | https://github.com/CachyOS | Código-fonte e issues |
| CachyOS Forum | https://discuss.cachyos.org | Comunidade |
| r/cachyos | https://reddit.com/r/cachyos | Comunidade Reddit |

***

## 🧠 Resumo rápido - Cheatsheet

```bash
# ATUALIZAR
yay -Syu                        # Atualizar tudo
checkupdates                    # Ver o que vai atualizar (sem instalar)

# INSTALAR
yay -S pacote                   # Instalar (repositórios + AUR)
sudo pacman -S pacote           # Instalar (apenas repositórios oficiais)

# REMOVER
sudo pacman -Rns pacote         # Remover + deps + configs
yay -Yc                         # Remover orfãos

# BUSCAR
yay -Ss termo                   # Buscar em tudo
pacman -Si pacote               # Informações de pacote

# DIAGNÓSTICO
journalctl -xe                  # Logs do sistema
cat /var/log/pacman.log         # Histórico do pacman
pacman -Qkk                     # Verificar integridade dos pacotes

# EMERGÊNCIA
sudo rm /var/lib/pacman/db.lck  # Remover lock travado
sudo pacman -Syyu               # Forçar re-sync e atualizar
sudo pacman -U /var/cache/...   # Downgrade pelo cache
```

***

> **Última dica:** No mundo do rolling release, o maior aliado não é o Google - é a ArchWiki. Antes de perguntar qualquer coisa em fóruns, procure na wiki. 90% das respostas já estão lá, com exemplos e explicações detalhadas.

***
