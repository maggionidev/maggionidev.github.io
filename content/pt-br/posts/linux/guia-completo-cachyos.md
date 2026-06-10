---
title: guia completo cachyos
slug: ''
description: ''
summary: ''
tags: []
categories: []
keywords: []
author: Gabriel Maggioni
date: 2026-05-06T22:59:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: true
draft: true
---

# 🐧 CachyOS — Guia Completo para o Dia a Dia no Terminal

> **CachyOS** é uma distribuição Arch Linux otimizada com foco em desempenho, usando kernels customizados (BORE, EEVDF, BMQ), pacotes compilados com PGO/LTO e um ecossistema voltado para usuários avançados e gamers. Este guia cobre o uso diário com foco no terminal.

***

## 📑 Índice

1. [Primeiros Passos](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#1-primeiros-passos)
2. [Gerenciamento de Pacotes com pacman e yay](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#2-gerenciamento-de-pacotes)
3. [O Kernel do CachyOS](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#3-o-kernel-do-cachyos)
4. [Gerenciamento de Processos e Performance](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#4-gerenciamento-de-processos-e-performance)
5. [Sistema de Arquivos e Disco](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#5-sistema-de-arquivos-e-disco)
6. [Rede via Terminal](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#6-rede-via-terminal)
7. [Usuários e Permissões](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#7-usu%C3%A1rios-e-permiss%C3%B5es)
8. [Ambiente Shell — Zsh, Fish e Bash](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#8-ambiente-shell)
9. [Ferramentas Modernas de Terminal](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#9-ferramentas-modernas-de-terminal)
10. [Aliases e Customização do Shell](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#10-aliases-e-customiza%C3%A7%C3%A3o-do-shell)
11. [Logs e Diagnóstico do Sistema](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#11-logs-e-diagn%C3%B3stico-do-sistema)
12. [Segurança e Firewall](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#12-seguran%C3%A7a-e-firewall)
13. [Gaming e Performance no Terminal](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#13-gaming-e-performance)
14. [Automação com Scripts](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#14-automa%C3%A7%C3%A3o-com-scripts)
15. [Dicas Avançadas e Truques](https://claude.ai/chat/41adc329-4d39-4131-822c-84de46b84615#15-dicas-avan%C3%A7adas)

***

## 1. Primeiros Passos

### Verificando o sistema após instalação

```plain
# Versão do sistema operacional
cat /etc/os-release

# Kernel em uso
uname -r

# Arquitetura e CPU
lscpu | grep -E "Architecture|Model name|CPU\(s\)"

# Memória RAM disponível
free -h

# Espaço em disco
df -h

# Uptime do sistema
uptime -p

```

### Atualizando o sistema pela primeira vez

```plain
# Atualização completa (pacman + repositórios CachyOS)
sudo pacman -Syyu

# Com yay (inclui AUR)
yay -Syyu

```

### Verificando repositórios do CachyOS

```plain
# Ver repositórios configurados
cat /etc/pacman.conf | grep -A2 "\[cachy"

# Listar todos os repositórios ativos
pacman -Sl | awk '{print $1}' | sort -u

```

***

## 2. Gerenciamento de Pacotes

O CachyOS usa **pacman** como gerenciador padrão e o repositório próprio com pacotes otimizados (compilados com `-march=native`, PGO e LTO).

### pacman — Operações Essenciais

```plain
# ─────────────────────────────────────────────────
# INSTALAÇÃO
# ─────────────────────────────────────────────────

# Instalar pacote
sudo pacman -S nome-do-pacote

# Instalar sem confirmação
sudo pacman -S --noconfirm nome-do-pacote

# Instalar múltiplos pacotes
sudo pacman -S git vim htop curl wget

# ─────────────────────────────────────────────────
# REMOÇÃO
# ─────────────────────────────────────────────────

# Remover pacote (mantém dependências)
sudo pacman -R nome-do-pacote

# Remover pacote + dependências órfãs
sudo pacman -Rs nome-do-pacote

# Remover pacote + deps + arquivos de configuração
sudo pacman -Rns nome-do-pacote

# ─────────────────────────────────────────────────
# BUSCA E INFORMAÇÃO
# ─────────────────────────────────────────────────

# Buscar pacote nos repositórios
pacman -Ss termo-de-busca

# Informações detalhadas de um pacote (remoto)
pacman -Si nome-do-pacote

# Informações de um pacote instalado
pacman -Qi nome-do-pacote

# Listar todos os pacotes instalados
pacman -Q

# Listar pacotes instalados explicitamente
pacman -Qe

# Listar pacotes órfãos
pacman -Qdt

# ─────────────────────────────────────────────────
# MANUTENÇÃO
# ─────────────────────────────────────────────────

# Atualizar sistema
sudo pacman -Syu

# Limpar cache de pacotes (manter últimas 3 versões)
sudo paccache -r

# Limpar todo o cache
sudo pacman -Sc

# Remover pacotes órfãos
sudo pacman -Rns $(pacman -Qtdq)

# Verificar integridade do banco de dados
sudo pacman -Dk

```

### yay — Helpers AUR

```plain
# Instalar do AUR
yay -S nome-do-pacote-aur

# Atualizar tudo (pacman + AUR)
yay -Syu

# Buscar no AUR
yay -Ss termo

# Remover pacote AUR
yay -Rns nome-do-pacote

# Ver pacotes AUR instalados
yay -Qm

# Limpar cache do yay
yay -Sc

```

### paru — Alternativa ao yay

```plain
# Instalar paru (se preferir)
yay -S paru

# Uso básico
paru -S pacote
paru -Syu
paru --fm ranger -S pacote   # abre PKGBUILD no ranger antes de instalar

```

### Repositórios exclusivos do CachyOS

```plain
# Listar pacotes otimizados do repositório CachyOS
pacman -Sl cachyos | less

# Ver quais pacotes do sistema vêm do CachyOS
pacman -Ql | grep cachyos

# Verificar se um pacote tem versão otimizada
pacman -Si firefox | grep Repository

```

***

## 3. O Kernel do CachyOS

O CachyOS oferece vários kernels otimizados. A escolha certa impacta diretamente na latência e throughput.

### Kernels disponíveis

| Kernel | Scheduler | Ideal para |
| --- | --- | --- |
| `linux-cachyos` | BORE | Uso geral, desktop |
| `linux-cachyos-eevdf` | EEVDF | Servidores, multithread |
| `linux-cachyos-bmq` | BMQ | Desktops interativos |
| `linux-cachyos-hardened` | BORE | Segurança máxima |
| `linux-cachyos-lts` | BORE | Estabilidade |
| `linux-cachyos-rt` | RT (Real-Time) | Áudio profissional |
| `linux-cachyos-zen` | Zen | Gaming, responsividade |

### Gerenciando kernels

```plain
# Ver kernel atual
uname -r

# Instalar kernel alternativo
sudo pacman -S linux-cachyos-eevdf linux-cachyos-eevdf-headers

# Listar kernels instalados
ls /boot/vmlinuz-*

# Atualizar GRUB após novo kernel
sudo grub-mkconfig -o /boot/grub/grub.cfg

# Ver parâmetros do kernel em uso
cat /proc/cmdline

```

### Parâmetros de kernel recomendados

Edite `/etc/default/grub` e adicione ao `GRUB_CMDLINE_LINUX_DEFAULT`:

```plain
sudo nano /etc/default/grub

# Parâmetros úteis para adicionar:
# mitigations=off          → desativa mitigações Spectre/Meltdown (mais perf)
# nowatchdog               → desativa watchdog (menos latência)
# split_lock_detect=off    → melhora perf em CPUs Intel
# amd_pstate=active        → driver P-State para AMD (melhor escala de freq)
# intel_pstate=active      → equivalente para Intel

# Após editar:
sudo grub-mkconfig -o /boot/grub/grub.cfg

```

***

## 4. Gerenciamento de Processos e Performance

### Monitoramento em tempo real

```plain
# Monitor de processos clássico
top

# htop — versão melhorada e interativa
htop

# btop — monitor moderno com gráficos
btop

# Processos em árvore
pstree -p

# Ver processos de um usuário específico
ps aux | grep seu-usuario

# Processos ordenados por uso de CPU
ps aux --sort=-%cpu | head -20

# Processos ordenados por uso de memória
ps aux --sort=-%mem | head -20

```

### Controle de processos

```plain
# Matar processo pelo nome
pkill nome-do-processo
killall nome-do-processo

# Matar pelo PID
kill -9 PID

# Pausar processo
kill -STOP PID

# Retomar processo pausado
kill -CONT PID

# Rodar processo em segundo plano
comando &

# Trazer processo do background para frente
fg %1

# Listar jobs em background
jobs

# Desconectar processo do terminal (não morre ao fechar terminal)
nohup comando &
# ou
disown %1

```

### Prioridade de processos (nice/renice)

```plain
# Rodar com menor prioridade (nice de -20 a 19; maior = menor prioridade)
nice -n 10 comando

# Aumentar prioridade de um processo em execução
sudo renice -n -5 -p PID

# Rodar com prioridade de tempo-real (para áudio, etc.)
sudo chrt -r 99 comando

```

### CPU e Frequência

```plain
# Instalar cpupower
sudo pacman -S cpupower

# Ver frequência atual
cpupower frequency-info

# Definir governador de CPU
sudo cpupower frequency-set -g performance    # máx desempenho
sudo cpupower frequency-set -g schedutil      # balanceado (padrão CachyOS)
sudo cpupower frequency-set -g powersave      # economia de energia

# Ver governador atual
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# Ver frequência de todos os núcleos
grep MHz /proc/cpuinfo

```

### Memória RAM

```plain
# Uso de memória
free -h

# Informações detalhadas
cat /proc/meminfo

# Limpar cache de memória (use com cuidado)
sudo sync && echo 3 | sudo tee /proc/sys/vm/drop_caches

# Ver uso de swap
swapon --show

# Estatísticas de memória virtual
vmstat -s -S M

```

***

## 5. Sistema de Arquivos e Disco

### Operações básicas com arquivos

```plain
# Navegar
cd /caminho/para/pasta
cd ~          # home
cd -          # diretório anterior
cd ..         # subir um nível

# Listar arquivos (com exa/eza se instalado)
ls -la
exa -la --icons --git          # moderno
eza -la --icons --git          # fork atual do exa

# Criar diretórios
mkdir -p pasta/subpasta/outra

# Copiar (com barra de progresso)
cp -v arquivo destino
rsync -ah --progress origem destino

# Mover/renomear
mv arquivo novo-nome

# Remover
rm arquivo
rm -rf pasta/   # cuidado!

# Ver tamanho de diretório
du -sh pasta/
du -sh * | sort -rh | head -20   # top 20 maiores

# Encontrar arquivos
find /caminho -name "*.log" -type f
find . -mtime -7    # modificados nos últimos 7 dias
find . -size +100M  # maiores que 100MB

```

### Disco e Partições

```plain
# Visão geral dos discos
lsblk
lsblk -f   # com sistema de arquivos

# Informações de partições
fdisk -l
sudo parted -l

# Uso de disco
df -h

# Montar partição
sudo mount /dev/sdb1 /mnt/pendrive

# Desmontar
sudo umount /mnt/pendrive

# Verificar integridade (ext4)
sudo fsck /dev/sda1

# Velocidade de leitura do disco
sudo hdparm -tT /dev/sda

# Benchmark com dd
dd if=/dev/zero of=/tmp/teste bs=1G count=1 oflag=direct

```

### Btrfs (padrão no CachyOS)

O CachyOS usa **Btrfs** por padrão, aproveitando snapshots, compressão e subvolumes.

```plain
# Ver subvolumes
sudo btrfs subvolume list /

# Criar snapshot manual
sudo btrfs subvolume snapshot / /snapshots/root-$(date +%Y%m%d)

# Listar snapshots
sudo btrfs subvolume list / | grep snapshot

# Deletar snapshot
sudo btrfs subvolume delete /snapshots/nome-do-snapshot

# Ver uso do Btrfs
sudo btrfs filesystem usage /

# Balancear o filesystem (otimização)
sudo btrfs balance start /

# Defrag (em Btrfs é por arquivo/dir)
sudo btrfs filesystem defragment -r /home

# Compressão — verificar se está ativa
compsize /home     # instale: yay -S compsize

```

### Timeshift — Snapshots automáticos

```plain
# Instalar
sudo pacman -S timeshift

# Criar snapshot manual via terminal
sudo timeshift --create --comments "antes da atualização"

# Listar snapshots
sudo timeshift --list

# Restaurar snapshot
sudo timeshift --restore --snapshot '2024-01-15_10-00-00'

# Deletar snapshot
sudo timeshift --delete --snapshot '2024-01-15_10-00-00'

```

***

## 6. Rede via Terminal

### Informações de rede

```plain
# Interfaces de rede
ip a
ip link show

# Tabela de roteamento
ip route

# Conexões ativas
ss -tulnp
netstat -tulnp    # alternativa clássica

# DNS em uso
cat /etc/resolv.conf
resolvectl status

```

### NetworkManager via terminal

```plain
# Status das conexões
nmcli general status
nmcli connection show

# Listar redes Wi-Fi disponíveis
nmcli device wifi list

# Conectar a Wi-Fi
nmcli device wifi connect "Nome-da-Rede" password "senha123"

# Desconectar
nmcli connection down "Nome-da-Rede"

# Verificar estado de uma interface
nmcli device show eth0

```

### Diagnóstico de rede

```plain
# Ping
ping -c 4 google.com
ping -c 4 1.1.1.1   # sem DNS (teste de conectividade pura)

# Traceroute
traceroute google.com
mtr google.com          # versão interativa (instale com: pacman -S mtr)

# DNS lookup
dig google.com
nslookup google.com
host google.com

# Velocidade de download
curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python3 -

# Ou com speedtest-cli
yay -S speedtest-cli
speedtest-cli

```

### Downloads e transferências

```plain
# curl
curl -O https://exemplo.com/arquivo.zip          # baixar arquivo
curl -L -o saida.html https://site.com           # seguir redirects
curl -I https://site.com                          # apenas headers

# wget
wget https://exemplo.com/arquivo.zip
wget -c https://exemplo.com/arquivo.zip          # retomar download
wget -r -np https://site.com/pasta/             # download recursivo

# aria2c — download paralelo (mais rápido)
sudo pacman -S aria2
aria2c -x 16 -s 16 https://exemplo.com/arquivo.iso

```

### SSH

```plain
# Conectar a servidor remoto
ssh usuario@servidor.com
ssh -p 2222 usuario@servidor.com   # porta customizada

# Gerar chave SSH
ssh-keygen -t ed25519 -C "seu@email.com"

# Copiar chave pública para servidor
ssh-copy-id usuario@servidor.com

# Tunnel SSH (port forwarding local)
ssh -L 8080:localhost:80 usuario@servidor.com

# Tunnel reverso
ssh -R 9090:localhost:3000 usuario@servidor.com

# Executar comando remoto
ssh usuario@servidor.com "ls -la /var/log"

# Copiar arquivos via SCP
scp arquivo.txt usuario@servidor.com:/home/usuario/
scp -r pasta/ usuario@servidor.com:/destino/

# rsync via SSH (melhor que SCP)
rsync -avz -e ssh pasta/ usuario@servidor.com:/destino/

```

***

## 7. Usuários e Permissões

### Gerenciamento de usuários

```plain
# Ver usuário atual
whoami
id

# Mudar para root
sudo su -
sudo -i

# Adicionar usuário
sudo useradd -m -G wheel,audio,video novousuario
sudo passwd novousuario

# Remover usuário
sudo userdel -r usuario

# Adicionar usuário a grupo
sudo usermod -aG docker usuario
sudo usermod -aG wheel usuario

# Ver grupos do usuário
groups
groups outro-usuario

# Listar usuários do sistema
cat /etc/passwd | grep -v nologin | grep -v false

```

### Permissões de arquivos

```plain
# Ver permissões
ls -l arquivo

# chmod — alterar permissões
chmod 755 script.sh       # rwxr-xr-x
chmod 644 arquivo.txt     # rw-r--r--
chmod +x script.sh        # adicionar execução
chmod -R 755 pasta/       # recursivo

# chown — alterar dono
sudo chown usuario arquivo
sudo chown usuario:grupo arquivo
sudo chown -R usuario:grupo pasta/

# SUID, SGID, Sticky Bit
chmod u+s arquivo         # SUID
chmod g+s pasta/          # SGID
chmod +t /tmp             # Sticky bit

```

### sudo

```plain
# Editar sudoers com segurança
sudo visudo

# Executar como outro usuário
sudo -u outro-usuario comando

# Ver comandos sudo disponíveis
sudo -l

# Rodar último comando com sudo
sudo !!

# Limpar cache de autenticação sudo
sudo -k

```

***

## 8. Ambiente Shell

### Zsh (padrão no CachyOS)

```plain
# Verificar shell atual
echo $SHELL
cat /etc/shells

# Definir Zsh como padrão
chsh -s /bin/zsh

# Arquivo de configuração
nano ~/.zshrc

# Recarregar configuração
source ~/.zshrc

```

### Oh My Zsh

```plain
# Instalar
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Plugins recomendados (adicionar em ~/.zshrc)
plugins=(
  git
  zsh-autosuggestions
  zsh-syntax-highlighting
  z
  sudo
  colored-man-pages
  extract
  docker
  history
)

# Instalar plugins adicionais
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

```

### Starship — Prompt universal

```plain
# Instalar
sudo pacman -S starship

# Adicionar ao ~/.zshrc ou ~/.bashrc
echo 'eval "$(starship init zsh)"' >> ~/.zshrc

# Criar configuração
mkdir -p ~/.config
nano ~/.config/starship.toml

```

Exemplo de `~/.config/starship.toml`:

```plain
format = """
[╭─](bold green)$username$hostname$directory$git_branch$git_status$cmd_duration
[╰─](bold green)$character"""

[username]
show_always = true
format = "[$user]($style)@"
style_user = "bold cyan"

[hostname]
ssh_only = false
format = "[$hostname]($style) "
style = "bold yellow"

[directory]
truncation_length = 3
format = "[$path]($style)[$read_only]($read_only_style) "
style = "bold blue"

[git_branch]
format = "[$symbol$branch]($style) "
symbol = " "
style = "bold purple"

[cmd_duration]
min_time = 500
format = "⏱ [$duration]($style) "
style = "bold yellow"

```

### Fish Shell (alternativa)

```plain
# Instalar Fish
sudo pacman -S fish

# Definir como padrão
chsh -s /usr/bin/fish

# Abrir configuração interativa
fish_config

# Instalar Fisher (gerenciador de plugins)
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher

# Plugins úteis para Fish
fisher install jorgebucaran/nvm.fish
fisher install PatrickF1/fzf.fish
fisher install IlanCosman/tide@v6

```

***

## 9. Ferramentas Modernas de Terminal

O CachyOS e o AUR oferecem versões modernas de utilitários clássicos. Instale e configure:

### Substituições recomendadas

```plain
# Instalar todas de uma vez
sudo pacman -S bat eza fd ripgrep fzf zoxide dust duf procs bottom tokei hyperfine

# Opcionais via AUR
yay -S gdu-go navi broot

```

### bat — cat melhorado

```plain
# Instalar
sudo pacman -S bat

# Uso básico (com syntax highlighting)
bat arquivo.py
bat --language json dados.json
bat --style=plain arquivo.txt    # sem decoração
bat -n arquivo.txt               # com números de linha

# Como pager
bat /etc/pacman.conf
man pacman | bat   # man pages coloridos

```

### eza — ls moderno

```plain
# Instalar
sudo pacman -S eza

# Uso
eza -la --icons --git
eza -T --level=2 --icons    # árvore de diretórios
eza -la --sort=modified     # ordenar por modificação
eza -la --group-directories-first

# Adicionar aliases no .zshrc
alias ls='eza --icons'
alias ll='eza -la --icons --git'
alias lt='eza -T --icons --level=2'
alias la='eza -la --icons --git --group-directories-first'

```

### fd — find moderno

```plain
# Instalar
sudo pacman -S fd

# Uso
fd nome-do-arquivo           # busca simples
fd -e py                     # arquivos .py
fd -t d nome                 # apenas diretórios
fd -H .zshrc                 # incluir arquivos ocultos
fd -x comando {}             # executar comando em cada resultado
fd -e log --exec rm {}       # deletar todos .log encontrados

```

### ripgrep (rg) — grep moderno

```plain
# Instalar
sudo pacman -S ripgrep

# Uso
rg "padrão"                  # busca recursiva
rg -i "padrão"               # case-insensitive
rg -l "padrão"               # apenas nomes de arquivo
rg -n "padrão"               # com números de linha
rg -t py "def "              # apenas em arquivos .py
rg --hidden "padrão"         # incluir arquivos ocultos
rg -C 3 "erro"               # 3 linhas de contexto

# Buscar e substituir (com sed)
rg -l "antigo" | xargs sed -i 's/antigo/novo/g'

```

### fzf — Fuzzy finder

```plain
# Instalar
sudo pacman -S fzf

# Busca interativa de arquivos
fzf

# Abrir arquivo selecionado no editor
nvim $(fzf)

# Histórico de comandos com fzf (Ctrl+R)
# Configurar no .zshrc:
source /usr/share/fzf/key-bindings.zsh
source /usr/share/fzf/completion.zsh

# Preview ao navegar
fzf --preview 'bat --color=always {}'

# Buscar e matar processo
kill -9 $(ps aux | fzf | awk '{print $2}')

```

### zoxide — cd inteligente

```plain
# Instalar
sudo pacman -S zoxide

# Adicionar ao .zshrc
echo 'eval "$(zoxide init zsh)"' >> ~/.zshrc

# Uso
z projeto        # vai para pasta que contém "projeto"
z doc img        # fuzzy: pasta que contém doc e img
zi               # modo interativo com fzf

```

### dust e duf — Uso de disco

```plain
# dust — du moderno
sudo pacman -S dust
dust /home              # tamanho de diretórios de forma visual
dust -d 2 /             # profundidade máxima 2

# duf — df moderno
sudo pacman -S duf
duf                     # visão completa dos discos montados
duf --only local        # apenas discos locais

```

### btop — Monitor de recursos

```plain
# Instalar
sudo pacman -S btop

# Executar
btop

# Configurações via interface:
# F2 ou 'm' para menu de opções
# 'p' para troca de processo
# 'q' para sair

```

***

## 10. Aliases e Customização do Shell

### Configuração recomendada para `~/.zshrc`

```plain
# ──────────────────────────────────────────────────
# ALIASES — Sistema
# ──────────────────────────────────────────────────
alias update='sudo pacman -Syu && yay -Syu'
alias cleanup='sudo pacman -Rns $(pacman -Qtdq) 2>/dev/null; sudo paccache -r'
alias orphans='pacman -Qtdq'
alias mirrors='sudo reflector --country Brazil,US --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist'

# ──────────────────────────────────────────────────
# ALIASES — Navegação
# ──────────────────────────────────────────────────
alias ls='eza --icons'
alias ll='eza -la --icons --git --group-directories-first'
alias lt='eza -T --icons --level=2'
alias la='eza -la --icons --git'
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'

# ──────────────────────────────────────────────────
# ALIASES — Arquivos
# ──────────────────────────────────────────────────
alias cat='bat'
alias grep='rg'
alias find='fd'
alias du='dust'
alias df='duf'
alias top='btop'
alias cp='cp -iv'
alias mv='mv -iv'
alias rm='rm -iv'

# ──────────────────────────────────────────────────
# ALIASES — Git
# ──────────────────────────────────────────────────
alias gs='git status'
alias ga='git add'
alias gaa='git add -A'
alias gc='git commit -m'
alias gp='git push'
alias gl='git pull'
alias glog='git log --oneline --graph --decorate --all'
alias gdiff='git diff'
alias gco='git checkout'
alias gb='git branch'

# ──────────────────────────────────────────────────
# ALIASES — Rede
# ──────────────────────────────────────────────────
alias myip='curl ifconfig.me'
alias localip="ip route get 1.1.1.1 | awk '{print \$7}'"
alias ports='ss -tulnp'
alias ping='ping -c 5'

# ──────────────────────────────────────────────────
# FUNÇÕES ÚTEIS
# ──────────────────────────────────────────────────

# Extrair qualquer arquivo comprimido
extract() {
    case $1 in
        *.tar.bz2) tar xjf $1 ;;
        *.tar.gz)  tar xzf $1 ;;
        *.tar.xz)  tar xJf $1 ;;
        *.tar.zst) tar --zstd -xf $1 ;;
        *.bz2)     bunzip2 $1 ;;
        *.gz)      gunzip $1 ;;
        *.zip)     unzip $1 ;;
        *.7z)      7z x $1 ;;
        *.rar)     unrar x $1 ;;
        *.zst)     zstd -d $1 ;;
        *)         echo "Formato desconhecido: $1" ;;
    esac
}

# Criar e entrar na pasta
mkcd() { mkdir -p "$1" && cd "$1"; }

# Backup rápido
bak() { cp "$1" "$1.bak.$(date +%Y%m%d_%H%M%S)"; }

# Busca e visualização rápida
fview() { fd "$1" | fzf --preview 'bat --color=always {}' | xargs -r $EDITOR; }

# Verificar qual processo usa uma porta
portcheck() { ss -tulnp | grep ":$1"; }

```

***

## 11. Logs e Diagnóstico do Sistema

### journalctl — Logs do systemd

```plain
# Ver todos os logs
journalctl

# Logs em tempo real (follow)
journalctl -f

# Logs de boot atual
journalctl -b

# Logs do boot anterior
journalctl -b -1

# Logs de um serviço específico
journalctl -u NetworkManager
journalctl -u sddm -f

# Logs de erro e críticos
journalctl -p err
journalctl -p crit

# Logs de hoje
journalctl --since today

# Últimas N linhas
journalctl -n 100

# Filtrar por período
journalctl --since "2024-01-01" --until "2024-01-31"

# Ver logs de um programa específico
journalctl _COMM=pacman
journalctl _COMM=systemd

# Limpar logs antigos
sudo journalctl --vacuum-time=7d    # manter últimos 7 dias
sudo journalctl --vacuum-size=500M  # manter até 500MB

```

### systemctl — Gerenciamento de serviços

```plain
# Ver status de um serviço
systemctl status nome-do-servico

# Iniciar / Parar / Reiniciar
sudo systemctl start servico
sudo systemctl stop servico
sudo systemctl restart servico
sudo systemctl reload servico

# Habilitar / Desabilitar na inicialização
sudo systemctl enable servico
sudo systemctl disable servico
sudo systemctl enable --now servico   # habilitar e iniciar

# Listar serviços ativos
systemctl list-units --type=service --state=active

# Listar serviços com falha
systemctl --failed

# Ver dependências de um serviço
systemctl list-dependencies servico

# Recarregar systemd (após criar unit files)
sudo systemctl daemon-reload

```

### Diagnóstico do hardware

```plain
# Informações do sistema
inxi -Fxz                # completo (yay -S inxi)
lshw -short              # hardware resumido
hwinfo --short           # outro formato

# CPU detalhado
lscpu
cat /proc/cpuinfo

# GPU
lspci -v | grep -i vga
glxinfo | grep "OpenGL renderer"
nvidia-smi               # para NVIDIA

# USB
lsusb
usb-devices

# PCI
lspci
lspci -v | less

# Temperatura
sensors                  # instale: sudo pacman -S lm_sensors
sudo sensors-detect      # detectar sensores
watch -n 1 sensors       # temperatura em tempo real

# SMART (saúde do disco)
sudo smartctl -a /dev/sda
sudo smartctl -t short /dev/sda   # teste rápido

```

***

## 12. Segurança e Firewall

### ufw — Firewall simplificado

```plain
# Instalar e habilitar
sudo pacman -S ufw
sudo systemctl enable --now ufw

# Status
sudo ufw status verbose

# Habilitar/desabilitar
sudo ufw enable
sudo ufw disable

# Regras básicas
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir portas/serviços
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow from 192.168.1.0/24    # rede local

# Negar
sudo ufw deny 23

# Remover regra
sudo ufw delete allow 80/tcp

# Resetar regras
sudo ufw reset

```

### iptables e nftables

```plain
# Ver regras atuais (nftables)
sudo nft list ruleset

# Ver regras iptables
sudo iptables -L -v -n

# Verificar portas abertas
sudo ss -tulnp
sudo netstat -tulnp

```

### Auditoria e segurança

```plain
# Verificar integridade de pacotes
sudo pacman -Qk     # verifica arquivos alterados
sudo pacman -Qkk    # verificação mais rigorosa

# Verificar arquivos SUID
find / -perm /4000 -type f 2>/dev/null

# Ver logins recentes
last
lastb           # tentativas falhas

# Ver quem está logado
who
w

# Checar atualizações de segurança
yay -Pu         # lista pacotes desatualizados

```

***

## 13. Gaming e Performance

### Gerenciamento de performance para jogos

```plain
# Verificar se gamemode está ativo
gamemoded -s

# Instalar gamemode
sudo pacman -S gamemode lib32-gamemode

# Executar jogo com gamemode
gamemoderun %command%    # no Steam: launch options

# Ativar perfil de desempenho
sudo cpupower frequency-set -g performance

# Verificar frequência atual de CPU
watch -n 0.5 "grep MHz /proc/cpuinfo | awk '{print \$4}'"

```

### Wine e Proton

```plain
# Instalar Wine
sudo pacman -S wine wine-mono wine-gecko winetricks

# Instalar Proton-GE (melhor para Steam)
yay -S proton-ge-custom

# Verificar instalações do Proton
ls ~/.steam/root/compatibilitytools.d/

# variáveis de ambiente úteis para jogos
DXVK_HUD=1            # overlay DXVK
MANGOHUD=1            # overlay completo (yay -S mangohud)
PROTON_LOG=1          # habilitar log
PROTON_USE_WINED3D=1  # usar WineD3D ao invés de DXVK

```

### MangoHud — Overlay de desempenho

```plain
# Instalar
yay -S mangohud lib32-mangohud

# Executar com overlay
mangohud jogo
MANGOHUD=1 jogo

# Configuração
mkdir -p ~/.config/MangoHud
nano ~/.config/MangoHud/MangoHud.conf

# Exemplo de configuração
# fps
# cpu_stats
# gpu_stats
# ram
# vram
# frame_timing
# fps_limit=144

```

### Vulkan e drivers

```plain
# Verificar suporte Vulkan
vulkaninfo | grep "GPU id"
vulkaninfo --summary

# Drivers AMD (AMDGPU)
sudo pacman -S mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon

# Drivers NVIDIA
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils nvidia-settings

# Drivers Intel
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel

# Ver GPU em uso
glxinfo | grep "renderer"

```

***

## 14. Automação com Scripts

### Script de manutenção completa

```plain
#!/bin/bash
# Salvar em ~/scripts/manutencao.sh e dar chmod +x

set -euo pipefail

echo "🔄 Atualizando mirrors..."
sudo reflector --country Brazil,US --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

echo "📦 Atualizando sistema..."
sudo pacman -Syu --noconfirm
yay -Syu --noconfirm

echo "🧹 Removendo órfãos..."
orphans=$(pacman -Qtdq 2>/dev/null)
if [ -n "$orphans" ]; then
    sudo pacman -Rns $orphans --noconfirm
fi

echo "💾 Limpando cache..."
sudo paccache -rk2
yay -Sc --noconfirm

echo "📸 Criando snapshot..."
sudo timeshift --create --comments "auto-update-$(date +%Y%m%d)"

echo "✅ Manutenção concluída!"

```

### Agendamento com systemd timers

```plain
# Criar serviço customizado
sudo nano /etc/systemd/system/minha-tarefa.service

```

```plain
[Unit]
Description=Minha tarefa agendada

[Service]
Type=oneshot
ExecStart=/home/usuario/scripts/manutencao.sh
User=usuario

```

```plain
# Criar timer
sudo nano /etc/systemd/system/minha-tarefa.timer

```

```plain
[Unit]
Description=Executar minha tarefa semanalmente

[Timer]
OnCalendar=weekly
Persistent=true

[Install]
WantedBy=timers.target

```

```plain
# Ativar timer
sudo systemctl enable --now minha-tarefa.timer

# Verificar timers ativos
systemctl list-timers

```

### Cron (alternativa)

```plain
# Editar crontab
crontab -e

# Formato: minuto hora dia mês dia-semana comando
# Exemplos:
0 2 * * *     /home/usuario/scripts/backup.sh      # todo dia às 2h
0 0 * * 0     /home/usuario/scripts/manutencao.sh  # domingo meia-noite
*/30 * * * *  /home/usuario/scripts/sync.sh        # a cada 30 min

# Listar tarefas
crontab -l

```

***

## 15. Dicas Avançadas

### Histórico de comandos avançado

```plain
# Ver histórico completo
history

# Executar comando do histórico por número
!123

# Repetir último comando com sudo
sudo !!

# Substituição rápida no último comando
^antigo^novo    # substitui "antigo" por "novo" no último comando

# Configurar histórico longo no .zshrc
HISTSIZE=50000
SAVEHIST=50000
HISTFILE=~/.zsh_history
setopt HIST_IGNORE_DUPS
setopt HIST_IGNORE_SPACE
setopt SHARE_HISTORY

```

### tmux — Multiplexador de terminal

```plain
# Instalar
sudo pacman -S tmux

# Iniciar sessão
tmux
tmux new -s nome-da-sessao

# Atalhos essenciais (prefixo padrão: Ctrl+B)
# Ctrl+B c    → nova janela
# Ctrl+B n    → próxima janela
# Ctrl+B p    → janela anterior
# Ctrl+B %    → dividir verticalmente
# Ctrl+B "    → dividir horizontalmente
# Ctrl+B setas → navegar entre painéis
# Ctrl+B d    → desanexar sessão
# Ctrl+B [    → modo scroll

# Listar sessões
tmux ls

# Reconectar sessão
tmux attach -t nome-da-sessao

# Matar sessão
tmux kill-session -t nome-da-sessao

```

Exemplo de `~/.tmux.conf`:

```plain
# Prefixo: Ctrl+A (como screen)
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# Mouse habilitado
set -g mouse on

# Indexar janelas a partir de 1
set -g base-index 1

# Cores e visual
set -g default-terminal "tmux-256color"
set -ag terminal-overrides ",xterm-256color:RGB"

# Barra de status
set -g status-bg '#1e1e2e'
set -g status-fg '#cdd6f4'
set -g status-right '#[fg=#a6e3a1] %d/%m/%Y %H:%M'

# Recarregar config
bind r source-file ~/.tmux.conf \; display "Config recarregada!"

```

### Variáveis de ambiente

```plain
# Ver todas as variáveis
env
printenv

# Ver uma variável específica
echo $PATH
printenv HOME

# Definir variável temporária (apenas sessão atual)
export MINHA_VAR="valor"

# Definir permanentemente (adicionar ao .zshrc)
echo 'export EDITOR=nvim' >> ~/.zshrc
echo 'export BROWSER=firefox' >> ~/.zshrc

# PATH: adicionar diretório
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc

```

### Redirecionamento e Pipes

```plain
# Redirecionar saída para arquivo
comando > arquivo.txt         # sobrescreve
comando >> arquivo.txt        # acrescenta

# Redirecionar erros
comando 2> erros.txt
comando > saida.txt 2>&1      # stdout e stderr juntos

# Descartar saída
comando > /dev/null 2>&1

# Pipes
ls -la | grep ".txt" | wc -l
cat /var/log/pacman.log | grep "upgraded" | tail -20
ps aux | grep firefox | awk '{print $2}' | xargs kill

# xargs — passar saída como argumento
find . -name "*.tmp" | xargs rm
echo "arquivo.txt" | xargs cat
ls *.jpg | xargs -I {} convert {} {}.png

```

### Compressão e Arquivamento

```plain
# tar
tar czf arquivo.tar.gz pasta/          # comprimir com gzip
tar cJf arquivo.tar.xz pasta/          # comprimir com xz
tar --zstd -cf arquivo.tar.zst pasta/  # comprimir com zstd (mais rápido)

tar xzf arquivo.tar.gz                 # extrair gzip
tar xJf arquivo.tar.xz                 # extrair xz
tar --zstd -xf arquivo.tar.zst        # extrair zstd

tar tzf arquivo.tar.gz                 # listar conteúdo

# zip / unzip
zip -r arquivo.zip pasta/
unzip arquivo.zip
unzip -l arquivo.zip    # listar sem extrair

# 7z (melhor compressão)
sudo pacman -S p7zip
7z a arquivo.7z pasta/
7z x arquivo.7z
7z l arquivo.7z

```

### Neovim — Editor de texto avançado

```plain
# Instalar
sudo pacman -S neovim

# Configuração completa com LazyVim
git clone https://github.com/LazyVim/starter ~/.config/nvim

# Atalhos essenciais do Neovim
# i       → modo inserção
# Esc     → modo normal
# :w      → salvar
# :q      → sair
# :wq     → salvar e sair
# :q!     → sair sem salvar
# /texto  → buscar
# n/N     → próximo/anterior resultado
# yy      → copiar linha
# dd      → deletar linha
# p       → colar
# u       → desfazer
# Ctrl+R  → refazer

```

***

## 📌 Referência Rápida — Comandos do Dia a Dia

```plain
# ──── SISTEMA ────────────────────────────────────
sudo pacman -Syu && yay -Syu          # atualizar tudo
sudo pacman -Rns $(pacman -Qtdq)      # limpar órfãos
sudo paccache -r                      # limpar cache
systemctl --failed                    # ver serviços com falha
journalctl -p err -b                  # erros do boot atual

# ──── ARQUIVOS ───────────────────────────────────
eza -la --icons --git                 # listar arquivos
fd -e conf                            # buscar arquivos .conf
rg "erro" /var/log/                   # buscar texto
bat arquivo.conf                      # visualizar com syntax
dust /home                            # uso de disco

# ──── REDE ───────────────────────────────────────
nmcli device wifi list                # listar Wi-Fi
curl ifconfig.me                      # IP externo
ss -tulnp                             # portas abertas
mtr google.com                        # traceroute interativo

# ──── PROCESSOS ──────────────────────────────────
btop                                  # monitor gráfico
ps aux --sort=-%cpu | head -10        # top CPU
ps aux --sort=-%mem | head -10        # top RAM
pkill nome                            # matar por nome

# ──── PACOTES ────────────────────────────────────
pacman -Ss termo                      # buscar pacote
pacman -Qi pacote                     # info do instalado
pacman -Ql pacote                     # arquivos do pacote
yay -Qm                               # pacotes AUR instalados

```

***

## 🔗 Recursos Adicionais

| Recurso | Link |
| --- | --- |
| Wiki CachyOS | https://wiki.cachyos.org |
| Arch Wiki | https://wiki.archlinux.org |
| AUR | https://aur.archlinux.org |
| r/cachyos | https://reddit.com/r/cachyos |
| Discord CachyOS | https://discord.gg/cachyos |
| GitHub CachyOS | https://github.com/cachyos |

***

> 💡 **Dica final:** O CachyOS é Arch Linux com melhorias de performance. Toda a documentação do **Arch Wiki** se aplica diretamente. É uma das wikis mais completas de Linux — consulte-a sempre!

***

_Guia criado para CachyOS — Atualizado para as versões mais recentes dos pacotes._
