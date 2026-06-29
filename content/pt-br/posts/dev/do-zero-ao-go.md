---
title: Do zero ao Go! Aprenda Golang em um post!
slug: from-zero-to-golang
description: Um guia completo, progressivo e profundo sobre Go - do Hello World ao runtime internamente, passando por concorrência, arquitetura, performance e projetos reais
summary: Um guia completo, progressivo e profundo sobre Go - do Hello World ao runtime internamente, passando por concorrência, arquitetura, performance e projetos reais
tags:
  - Go
  - dev
categories:
  - dev
keywords: []
author: Gabriel Maggioni
date: 2026-05-25T15:53:00
lastmod: 2026-05-25T15:53:00
showToc: true
TocOpen: false
draft: false
---

***

Vamos do zero ao fundo do poço.

***

# Introdução

## O que é Go

Go (ou Golang, como é frequentemente chamada fora da comunidade oficial) é uma linguagem compilada, estaticamente tipada, com garbage collector, desenvolvida na Google e lançada publicamente em 2009. Ela produz binários nativos, tem suporte de primeira classe a concorrência e foi projetada com uma filosofia quase filosófica de que complexidade é o inimigo principal do software.

Se você vem de Python ou JavaScript, Go vai parecer verbosa e rígida no começo. Se você vem de C++ ou Java, vai parecer estranhamente simples. Essa sensação é intencional.

## História da Linguagem

Go foi criada por Robert Griesemer, Rob Pike e Ken Thompson - três gigantes da computação. Ken Thompson co-criou o Unix e a linguagem C. Rob Pike trabalhou no Plan 9 (o sucessor conceitual do Unix) e no UTF-8. Griesemer trabalhou no V8, o engine JavaScript do Chrome.

Eles estavam frustrados. Na Google de 2007, compilar grandes projetos em C++ levava minutos. A linguagem acumulava décadas de complexidade. Python era rápido de escrever mas lento de executar. Java carregava um ecossistema pesado.

A pergunta que eles fizeram foi: _"Se projetássemos uma linguagem hoje, em 2007, sabendo tudo que sabemos, o que faríamos diferente?"_

A resposta foi Go.

## Filosofia do Go

Go tem uma filosofia explícita e bastante opiniosa, que pode ser resumida em alguns princípios:

**Clareza é mais importante que esperteza.** Em Go, o código óbvio é preferível ao código "elegante". Se você precisa pensar muito para entender um trecho de código, algo está errado.

**Menos features, não mais.** Go deliberadamente não tem herança de classes, não tem sobrecarga de operadores, não tem generics por muitos anos (até Go 1.18), não tem exceções no sentido tradicional. Cada feature que não existe é uma feature que não precisa ser aprendida, debugada ou mal utilizada.

**Compilação rápida é um requisito, não um bônus.** A arquitetura de imports do Go foi projetada para que o compilador nunca precise processar o mesmo arquivo duas vezes.

**Concorrência como cidadã de primeira classe.** Go foi construída num mundo de CPUs multi-core. Goroutines e channels são parte da linguagem, não uma biblioteca.

**Erros são valores.** Não existem exceções em Go. Erros são retornados explicitamente como valores, tornando o fluxo de controle transparente.

## Por que Go Existe

A maioria das linguagens foi projetada por cientistas da computação interessados em problemas computacionais. Go foi projetada por engenheiros de sistemas interessados em problemas de produção: servidores que precisam responder em milissegundos, sistemas que precisam escalar para milhões de conexões, equipes de centenas de desenvolvedores trabalhando no mesmo codebase.

O contexto importa. Na Google, existiam binários C++ que levavam 45 minutos para compilar. Existiam codebases onde ninguém sabia mais o que era seguro mudar. Existiam sistemas de CI que custavam fortunas só em tempo de compilação.

Go resolve esses problemas específicos muito bem. Isso explica tanto seus pontos fortes quanto suas limitações.

## Problemas que Go Tenta Resolver

1. **Velocidade de compilação**: um projeto Go de tamanho médio compila em segundos
2. **Legibilidade em escala**: `gofmt` garante que todo código Go tem a mesma formatação
3. **Gerenciamento de dependências**: `go mod` é simples e reproduzível
4. **Concorrência segura**: o modelo de goroutines + channels reduz classes inteiras de bugs
5. **Deploy simples**: um binário estático que roda em qualquer máquina Linux, sem JVM, sem interpretador
6. **Cross-compilation trivial**: compilar para Windows estando no Linux é uma variável de ambiente

***

# Instalando Go

## Linux

```bash
# Baixe a versão mais recente em https://go.dev/dl/
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz

# Remove instalação anterior (se houver) e descompacta
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz

# Adicione ao seu .bashrc ou .zshrc
export PATH=$PATH:/usr/local/go/bin
```

Recarregue o shell e verifique:

```bash
go version
# go version go1.22.0 linux/amd64
```

## macOS

A forma mais limpa no Mac é via Homebrew:

```bash
brew install go
go version
```

Ou baixe o instalador `.pkg` diretamente em `go.dev/dl`.

## Windows

Baixe o instalador `.msi` em `go.dev/dl`. Ele configura o PATH automaticamente. Após a instalação, abra um novo terminal e verifique:

```powershell
go version
```

## GOPATH e GOROOT

Dois conceitos que confundem bastante no começo:

**GOROOT** é onde Go está instalado. Você raramente precisa se preocupar com isso. É `/usr/local/go` no Linux por padrão.

**GOPATH** era onde seus projetos e dependências ficavam antes do Go Modules (antes do Go 1.11). Era a grande fonte de confusão da época: todo código precisava estar em `$GOPATH/src/github.com/usuario/projeto`. Hoje, com módulos, você pode colocar seu projeto em qualquer diretório.

O GOPATH ainda existe e ainda é usado para guardar binários instalados via `go install`. Por padrão é `~/go`. Você pode verificar tudo com:

```bash
go env
```

Isso lista todas as variáveis de ambiente que Go usa. Algumas importantes:

```plain
GOROOT=/usr/local/go        # onde Go está instalado
GOPATH=/home/user/go        # diretório de trabalho
GOOS=linux                  # sistema operacional alvo
GOARCH=amd64               # arquitetura alvo
GOMODCACHE=/home/user/go/pkg/mod  # cache de módulos
```

***

# Primeiro Programa

## Hello World

Crie um diretório, inicialize um módulo, escreva o código:

```bash
mkdir hello && cd hello
go mod init github.com/seunome/hello
```

Crie o arquivo `main.go`:

```go
// Todo programa Go executável começa com o package main.
// Sem isso, o compilador não sabe que esse é um ponto de entrada.
package main

// import traz pacotes externos ou da biblioteca padrão.
// "fmt" significa "formatted I/O" - é o pacote de formatação básica.
import "fmt"

// A função main() é o ponto de entrada do programa.
// Não recebe argumentos aqui (argumentos de linha de comando
// ficam em os.Args).
func main() {
    fmt.Println("Hello, World!")
}
```

Para rodar:

```bash
go run main.go
# Hello, World!
```

Para compilar:

```bash
go build -o hello main.go
./hello
# Hello, World!
```

## Como a Compilação Funciona

Quando você roda `go build`, acontece uma sequência interessante:

1. O compilador lê seu código fonte e os imports
2. Resolve o grafo de dependências (cada pacote importado tem seus próprios imports)
3. Compila cada pacote exatamente uma vez (essa é a chave da velocidade de compilação do Go)
4. Liga tudo em um único binário estático

A palavra "estático" aqui é crucial. O binário resultante contém:

- Seu código compilado
- Todo código de biblioteca que você usou
- O runtime do Go (scheduler, garbage collector, etc.)

Isso significa que você pode copiar o binário para qualquer máquina com a mesma arquitetura e ele vai rodar. Sem instalar Go. Sem instalar dependências. É o sonho do deploy.

## Cross Compilation

Uma das features mais práticas do Go. Para compilar para um sistema diferente do seu:

```bash
# Compila para Windows, mesmo estando no Linux
GOOS=windows GOARCH=amd64 go build -o hello.exe main.go

# Compila para Linux ARM (Raspberry Pi)
GOOS=linux GOARCH=arm64 go build -o hello-arm main.go

# Compila para macOS
GOOS=darwin GOARCH=amd64 go build -o hello-mac main.go
```

Isso funciona sem nenhuma configuração adicional porque o compilador Go inclui suporte a múltiplas arquiteturas por padrão. Em C/C++, cross-compilation é um pesadelo de toolchains. Em Go, é uma variável de ambiente.

***

# Fundamentos da Linguagem

## Variáveis

Go tem algumas formas de declarar variáveis, e a escolha entre elas não é arbitrária:

```go
package main

import "fmt"

func main() {
    // Forma longa: var nome tipo = valor
    // Use quando você quer ser explícito sobre o tipo,
    // especialmente em variáveis de package (fora de funções).
    var idade int = 30

    // Forma curta: inferência de tipo
    // Use dentro de funções para a maioria dos casos.
    // O compilador infere que "nome" é string.
    nome := "Alice"

    // Declaração sem valor inicial: zero value
    // Em Go, toda variável tem um valor padrão. Não existe "undefined".
    var ativo bool   // false
    var contador int // 0
    var texto string // ""

    fmt.Println(nome, idade, ativo, contador, texto)

    // Declaração múltipla com var
    var (
        x, y int = 10, 20
        z        = 30.5 // float64 inferido
    )
    fmt.Println(x, y, z)

    // Múltipla atribuição curta
    a, b := 1, 2
    fmt.Println(a, b)

    // Swap idiomático - sem variável temporária
    a, b = b, a
    fmt.Println(a, b) // 2, 1
}
```

**Regra prática:** use `:=` dentro de funções para quase tudo. Use `var` quando precisar de zero value explícito, quando precisar especificar o tipo explicitamente, ou em declarações de nível de pacote.

## Tipos Básicos

```go
// Inteiros com tamanho explícito
var i8 int8   // -128 a 127
var i16 int16 // -32768 a 32767
var i32 int32 // -2 bilhões a 2 bilhões
var i64 int64 // enorme

// int e uint: tamanho depende da arquitetura (32 ou 64 bits)
// Na prática, use sempre "int" para inteiros de propósito geral
var n int = 42

// Ponto flutuante
var f32 float32 // ~7 dígitos de precisão
var f64 float64 // ~15 dígitos de precisão - use esse por padrão

// Booleano
var ok bool = true

// String: imutável, sequência de bytes UTF-8
var s string = "Olá, mundo"

// byte = alias para uint8 (um byte)
// rune = alias para int32 (um codepoint Unicode)
var b byte = 'A'
var r rune = '🚀'

// Para saber o tamanho de um tipo:
import "unsafe"
fmt.Println(unsafe.Sizeof(int64(0))) // 8 bytes
```

A escolha entre `int32` e `int64` importa em contextos de performance e serialização, mas no código de aplicação comum, use `int` e `float64` e não pense mais nisso.

## Inferência de Tipos

O compilador Go infere tipos em tempo de compilação, não em tempo de execução como Python. Isso significa zero custo de runtime:

```go
x := 42        // int (não int8, não int32 - sempre int)
y := 3.14      // float64 (não float32)
z := "texto"   // string
w := true      // bool

// Cuidado: a inferência pode surpreender com constantes numéricas
a := 1         // int
b := 1.0       // float64
c := int32(1)  // int32 explícito
```

## Constantes

Constantes em Go são mais poderosas do que parecem:

```go
// Constante tipada
const Pi float64 = 3.14159265358979

// Constante não tipada - tem "precisão arbitrária"
// pode ser usada com qualquer tipo numérico compatível
const MaxItems = 1000

// iota: gerador de constantes inteiras sequenciais
// Muito usado para enumerações
type DiaDaSemana int

const (
    Domingo  DiaDaSemana = iota // 0
    Segunda                     // 1
    Terca                       // 2
    Quarta                      // 3
    Quinta                      // 4
    Sexta                       // 5
    Sabado                      // 6
)

// iota com expressões
const (
    _  = iota             // ignora o primeiro valor (0)
    KB = 1 << (10 * iota) // 1 << 10 = 1024
    MB                    // 1 << 20
    GB                    // 1 << 30
    TB                    // 1 << 40
)
```

Constantes em Go são avaliadas em tempo de compilação. Isso significa que `KB * 1024` numa constante é calculado pelo compilador, não pelo programa em execução.

## Zero Values

Esta é uma das features mais subestimadas de Go. Toda variável declarada tem um valor inicial definido, nunca lixo de memória:

```go
var i int       // 0
var f float64   // 0.0
var b bool      // false
var s string    // ""
var p *int      // nil
var sl []int    // nil (slice nil, não slice vazio)
var m map[string]int // nil
var fn func()   // nil
```

Isso parece pequeno, mas elimina toda uma classe de bugs que em C aparecem como comportamentos aleatórios ("funcionou no meu computador"). Em Go, o comportamento de um valor não inicializado é 100% previsível.

**Por que isso importa em structs:**

```go
type Servidor struct {
    Host string
    Port int
    TLS  bool
}

// Funciona perfeitamente - todos os campos têm zero values
var s Servidor
fmt.Println(s.Port) // 0
fmt.Println(s.TLS)  // false
```

## Conversão de Tipos

Go não faz conversão implícita de tipos. Nunca. Isso é intencional:

```go
var i int = 42
var f float64 = float64(i) // conversão explícita obrigatória
var u uint = uint(f)       // explícita novamente

// Isso NÃO compila:
// var f float64 = i  // erro: cannot use i (type int) as type float64

// String para/de bytes
s := "hello"
b := []byte(s)    // converte string para slice de bytes
s2 := string(b)   // converte slice de bytes para string

// rune (Unicode codepoint) para string
r := '🚀'
sr := string(r) // "🚀"
```

A ausência de conversão implícita parece irritante no início, mas elimina bugs sutis de overflow e perda de precisão que em outras linguagens passam silenciosamente.

## Escopo

Go usa escopo léxico com blocos definidos por `{}`:

```go
package main

import "fmt"

// Variável de pacote: visível em todo o pacote
var versao = "1.0.0"

func main() {
    x := 10 // escopo: função main

    if x > 5 {
        y := 20         // escopo: bloco if
        fmt.Println(y)  // ok
    }
    // fmt.Println(y) // erro: y não existe aqui

    // Variável declarada no if é visível apenas dentro dele
    // Mas há uma forma útil de declarar no próprio if:
    if resultado, err := algumaFuncao(); err != nil {
        // resultado e err existem apenas aqui
        fmt.Println("erro:", err)
    } else {
        // resultado ainda existe no bloco else
        fmt.Println("ok:", resultado)
    }
    // resultado e err não existem aqui
}

func algumaFuncao() (int, error) {
    return 42, nil
}
```

***

# Como Memória Funciona

Esta seção é onde muitos tutoriais falham: ensinam a sintaxe mas não explicam o que acontece embaixo. Entender memória em Go vai te ajudar a escrever código mais eficiente e a entender por que certas práticas idiomáticas existem.

## RAM, Stack e Heap

Pense na memória de um processo rodando como dois grandes espaços com propósitos diferentes:

```plain
Memória do processo
┌────────────────────────────┐
│          Stack             │ ← rápido, tamanho limitado, automático
│  (cresce para baixo ↓)    │
├────────────────────────────┤
│                            │
│         (espaço)           │
│                            │
├────────────────────────────┤
│           Heap             │ ← mais lento, tamanho "ilimitado", gerenciado
│  (cresce para cima ↑)     │
└────────────────────────────┘
```

**Stack (pilha):** cada goroutine tem sua própria stack. Variáveis locais vivem na stack. Quando uma função é chamada, um "frame" é empurrado na stack com todas as variáveis locais daquela função. Quando a função retorna, o frame é descartado instantaneamente - sem garbage collection, sem malloc, sem free. É simplesmente um ajuste de um ponteiro (o stack pointer). Por isso é tão rápido.

A stack tem tamanho limitado (no Go, começa pequena - 2KB ou 8KB - e cresce dinamicamente, mais sobre isso adiante).

**Heap:** quando um objeto precisa sobreviver além da função que o criou, ou quando é grande demais para a stack, ele vai para o heap. O heap é gerenciado pelo garbage collector. Alocar no heap é mais custoso porque envolve encontrar espaço, potencialmente rodar o GC, etc.

## Ponteiros

Ponteiro é uma variável que guarda um endereço de memória, não um valor diretamente:

```go
package main

import "fmt"

func main() {
    // x é um int na stack, com valor 42
    x := 42

    // p é um ponteiro para int - guarda o ENDEREÇO de x
    // & é o operador "address of"
    p := &x

    fmt.Println(x)  // 42  - o valor
    fmt.Println(p)  // 0xc000018058 - o endereço (vai variar)
    fmt.Println(*p) // 42  - dereferenciando: "o valor no endereço p"

    // Modificar através do ponteiro modifica o original
    *p = 100
    fmt.Println(x) // 100
}
```

Por que ponteiros existem? Para dois propósitos principais:

1. **Compartilhar dados sem copiar:** se você tem uma struct de 10KB e passa por valor para uma função, Go copia 10KB. Se você passa um ponteiro, copia 8 bytes (o endereço).
2. **Modificar o original:** funções recebem cópias dos argumentos. Para modificar a variável original, você precisa do ponteiro.

```go
// Esta função não modifica o original - recebe uma CÓPIA
func dobrarErrado(n int) {
    n = n * 2 // modifica apenas a cópia local
}

// Esta função modifica o original - recebe o ENDEREÇO
func dobrarCerto(n *int) {
    *n = *n * 2 // modifica o valor no endereço recebido
}

func main() {
    x := 10
    dobrarErrado(x)
    fmt.Println(x) // 10 - não mudou

    dobrarCerto(&x)
    fmt.Println(x) // 20 - mudou
}
```

## Escape Analysis

Esta é a parte que a maioria dos tutoriais ignora completamente.

O compilador Go decide automaticamente se uma variável vive na stack ou no heap através de um processo chamado **escape analysis**. Você não precisa gerenciar isso manualmente (diferente de C), mas entender como funciona te ajuda a escrever código mais eficiente.

**Regra geral:** se uma variável "escapa" do escopo onde foi criada - ou seja, alguma referência a ela pode ser usada depois que a função retornar - ela precisa ir para o heap.

```go
// Exemplo 1: fica na stack
func semEscape() int {
    x := 42   // x fica na stack
    return x  // retorna o VALOR, não o ponteiro
}             // frame da função é descartado, x some

// Exemplo 2: escapa para o heap
func comEscape() *int {
    x := 42   // x PRECISA ir para o heap!
    return &x // retorna o ENDEREÇO de x
}             // a função retorna, mas alguém tem o endereço de x
              // Go não pode descartá-la - ela vai para o heap
```

No segundo caso, a variável `x` "escapa" para o heap porque seu endereço é retornado e pode ser usado depois que a função terminar.

Para ver o que o compilador decidiu, use a flag de análise:

```bash
go build -gcflags='-m' main.go

# Output típico:
# ./main.go:9:2: moved to heap: x
```

Algumas situações que causam escape para o heap:

- Retornar um ponteiro para variável local
- Armazenar um ponteiro em uma interface
- Passar para `interface{}` (porque o compilador perde rastreabilidade do tipo)
- Closures que capturam variáveis
- Slices que crescem além do tamanho inicial (com `append`)

**Por que isso importa para performance?** Alocações no heap têm custo duplo: o custo da alocação em si e o custo do GC para eventualmente coletar. Código de alta performance em Go tenta minimizar alocações no heap.

## Garbage Collector do Go

O GC do Go usa um algoritmo chamado **tri-color mark-and-sweep** com suporte a concorrência. Em termos práticos, o que você precisa saber:

**Como funciona em alto nível:**

1. **Mark phase (fase de marcação):** o GC começa pelas "raízes" (variáveis globais, stacks de goroutines, registradores) e percorre o grafo de objetos, marcando tudo que é alcançável como "vivo"
2. **Sweep phase (fase de varredura):** tudo que não foi marcado é considerado lixo e a memória é liberada

O algoritmo tri-color usa três conjuntos:

- **Branco:** ainda não visitado (candidato a coleta)
- **Cinza:** visitado, mas ainda tem filhos para processar
- **Preto:** visitado e todos os filhos processados (definitivamente vivo)

**O que torna o GC do Go especial:** ele roda concorrentemente com o programa. O "stop the world" (pausar todas as goroutines) é extremamente curto - sub-milissegundo na maioria dos casos. O trabalho pesado da marcação acontece em paralelo com a execução do programa.

Para um serviço web, latências de GC raramente são perceptíveis. Para sistemas de trading de alta frequência ou jogos em tempo real, você precisa ser mais cuidadoso com alocações.

**Como reduzir pressão no GC:**

```go
// Ruim: cria uma nova slice em cada chamada
func processarItens(ids []int) []Resultado {
    resultados := make([]Resultado, 0) // alocação
    for _, id := range ids {
        resultados = append(resultados, processar(id))
    }
    return resultados
}

// Melhor: pré-aloca com tamanho conhecido
func processarItens(ids []int) []Resultado {
    resultados := make([]Resultado, 0, len(ids)) // capacidade pré-alocada
    for _, id := range ids {
        resultados = append(resultados, processar(id))
    }
    return resultados
}

// Melhor ainda em hot paths: reutiliza com sync.Pool (ver seção de performance)
```

***

# Controle de Fluxo

## if

```go
// if básico - sem parênteses (diferente de C/Java/JS)
if x > 10 {
    fmt.Println("grande")
}

// if-else
if x > 10 {
    fmt.Println("grande")
} else if x > 5 {
    fmt.Println("médio")
} else {
    fmt.Println("pequeno")
}

// Forma idiomática: declaração + condição no mesmo if
// Muito comum para verificar erros
if err := fazerAlgo(); err != nil {
    return fmt.Errorf("ao fazer algo: %w", err)
}

// A variável err existe apenas dentro do if
// Isso mantém o escopo limpo
if usuario, err := buscarUsuario(id); err != nil {
    return nil, err
} else {
    // usuario existe aqui também
    return usuario, nil
}
```

## switch

O `switch` em Go é bem mais poderoso e elegante que em C:

```go
// switch com valor
switch status {
case "ativo":
    fmt.Println("usuário ativo")
case "inativo", "suspenso": // múltiplos valores numa case
    fmt.Println("usuário não pode acessar")
default:
    fmt.Println("status desconhecido")
}
// Sem break! Go não faz fallthrough por padrão.
// Se quiser fallthrough explícito, use a keyword `fallthrough`

// switch sem valor: funciona como if-else encadeado
switch {
case x < 0:
    fmt.Println("negativo")
case x == 0:
    fmt.Println("zero")
case x > 0:
    fmt.Println("positivo")
}

// type switch: extremamente útil com interfaces
func descrever(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("inteiro: %d\n", v)
    case string:
        fmt.Printf("string: %q\n", v)
    case bool:
        fmt.Printf("bool: %t\n", v)
    default:
        fmt.Printf("tipo desconhecido: %T\n", v)
    }
}
```

## for

Go tem apenas um tipo de loop: `for`. Mas ele cobre todos os casos:

```go
// Loop clássico estilo C
for i := 0; i < 10; i++ {
    fmt.Println(i)
}

// Loop "while" (apenas condição)
n := 1
for n < 1000 {
    n *= 2
}

// Loop infinito
for {
    // só sai com break ou return
    if condicao {
        break
    }
}

// range: itera sobre slices, arrays, maps, strings, channels
// Com slice/array: retorna índice e valor
numeros := []int{10, 20, 30}
for i, v := range numeros {
    fmt.Println(i, v) // 0 10, 1 20, 2 30
}

// Se não precisa do índice, use _
for _, v := range numeros {
    fmt.Println(v)
}

// Se não precisa do valor
for i := range numeros {
    fmt.Println(i)
}

// range em map: ordem NÃO é garantida
m := map[string]int{"a": 1, "b": 2}
for k, v := range m {
    fmt.Println(k, v) // ordem aleatória
}

// range em string: itera por rune (codepoint Unicode), não byte
// Importante para strings com caracteres multibyte
for i, r := range "olá" {
    fmt.Printf("%d: %c (%d)\n", i, r, r)
    // 0: o (111)
    // 1: l (108)
    // 2: á (225)  <- byte index 2, não 3, porque 'á' é 2 bytes
}
```

**Uma armadilha clássica com range:**

```go
// ARMADILHA: o range faz CÓPIA do valor
type Pessoa struct{ Nome string }

pessoas := []Pessoa{{"Alice"}, {"Bob"}}
for _, p := range pessoas {
    p.Nome = "modificado" // modifica a CÓPIA, não o original!
}
fmt.Println(pessoas[0].Nome) // "Alice" - não mudou!

// CORRETO: use índice para acessar o original
for i := range pessoas {
    pessoas[i].Nome = "modificado" // modifica o original
}

// OU: use ponteiro na slice
pessoas2 := []*Pessoa{{"Alice"}, {"Bob"}}
for _, p := range pessoas2 {
    p.Nome = "modificado" // p é um ponteiro - modifica o original
}
```

## defer

`defer` agenda uma chamada de função para executar quando a função atual retornar. É uma das features mais elegantes de Go:

```go
func lerArquivo(caminho string) (string, error) {
    f, err := os.Open(caminho)
    if err != nil {
        return "", err
    }
    defer f.Close() // vai executar quando lerArquivo retornar
                    // independente de como retornar (normal ou com erro)

    // ... lê o arquivo ...
    return conteudo, nil
}
```

Sem `defer`, você precisaria chamar `f.Close()` em cada ponto de retorno. Com múltiplos caminhos de erro, isso é propenso a esquecimento.

**Defers são empilhados (LIFO):**

```go
func exemploDefers() {
    defer fmt.Println("terceiro") // executado por último
    defer fmt.Println("segundo")
    defer fmt.Println("primeiro") // executado primeiro
    fmt.Println("durante")
}
// Output:
// durante
// primeiro
// segundo
// terceiro
```

**defer avalia os argumentos imediatamente:**

```go
// Armadilha: o valor de i é capturado no momento do defer
for i := 0; i < 3; i++ {
    defer fmt.Println(i) // imprime 2, 1, 0 - não o valor futuro
}

// Para capturar o valor futuro, use closure
for i := 0; i < 3; i++ {
    i := i // cria nova variável i local ao loop
    defer func() {
        fmt.Println(i) // captura a variável do closure
    }()
}
```

**defer com named returns para limpeza de erros:**

```go
func transacao(db *sql.DB) (err error) {
    tx, err := db.Begin()
    if err != nil {
        return
    }
    defer func() {
        if err != nil {
            tx.Rollback() // faz rollback se houve erro
        } else {
            err = tx.Commit() // faz commit, e se commit falhar,
                              // o erro é propagado pelo named return
        }
    }()

    // ... executa operações ...
    return nil
}
```

***

# Funções

## Múltiplos Retornos

Esta é uma das features que mais define o estilo de código Go:

```go
// Função com múltiplos retornos
func dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("divisão por zero")
    }
    return a / b, nil
}

func main() {
    resultado, err := dividir(10, 2)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println(resultado) // 5

    // Use _ para descartar valores que não precisa_
_    resultado2, _ := dividir(10, 4)
    fmt.Println(resultado2) // 2.5
}
```

**Named returns (retornos nomeados):**

```go
// Os valores de retorno têm nomes - são variáveis declaradas
func minMax(arr []int) (min, max int) {
    // min e max são inicializadas com zero value
    if len(arr) == 0 {
        return // "naked return" - retorna min e max com seus valores atuais
    }
    min, max = arr[0], arr[0]
    for _, v := range arr[1:] {
        if v < min {
            min = v
        }
        if v > max {
            max = v
        }
    }
    return // retorna min e max
}
```

Use named returns com cuidado. Eles melhoram a legibilidade quando os nomes são descritivos, mas "naked returns" em funções longas tornam o código opaco.

## Closures

Uma closure é uma função que "fecha" sobre variáveis do escopo externo:

```go
// Gerador de IDs sequenciais
func novoGerador() func() int {
    id := 0 // esta variável é capturada pelo closure
    return func() int {
        id++ // modifica a variável capturada
        return id
    }
}

func main() {
    gerarID := novoGerador()
    fmt.Println(gerarID()) // 1
    fmt.Println(gerarID()) // 2
    fmt.Println(gerarID()) // 3

    // Outro gerador tem sua própria variável id independente
    outroGerador := novoGerador()
    fmt.Println(outroGerador()) // 1
}
```

Closures são fundamentais para alguns padrões em Go, como decoradores de funções, callbacks e geração lazy de valores.

**Armadilha clássica com closures em loops:**

```go
// PROBLEMA: todas as goroutines capturam a MESMA variável i
funcs := make([]func(), 3)
for i := 0; i < 3; i++ {
    funcs[i] = func() {
        fmt.Println(i) // quando executar, i vale 3 (valor final do loop)
    }
}
for _, f := range funcs {
    f() // imprime 3, 3, 3
}

// SOLUÇÃO 1: crie uma cópia de i dentro do loop
for i := 0; i < 3; i++ {
    i := i // shadowing: nova variável i para cada iteração
    funcs[i] = func() {
        fmt.Println(i) // captura a variável LOCAL, não a do loop
    }
}

// SOLUÇÃO 2: passe como argumento
for i := 0; i < 3; i++ {
    funcs[i] = func(n int) func() {
        return func() { fmt.Println(n) }
    }(i) // chama imediatamente com o valor atual de i
}
```

## Funções Variádicas

```go
// ... antes do tipo indica variádica
// args é uma slice dentro da função
func soma(args ...int) int {
    total := 0
    for _, v := range args {
        total += v
    }
    return total
}

func main() {
    fmt.Println(soma(1, 2, 3))       // 6
    fmt.Println(soma(1, 2, 3, 4, 5)) // 15

    // Expandir uma slice para argumentos variádicos
    numeros := []int{1, 2, 3, 4}
    fmt.Println(soma(numeros...)) // 10 - o ... expande a slice
}
```

`fmt.Println` é variádica (`func Println(a ...interface{}) (n int, err error)`). Esse é o padrão para funções que aceitam número variável de argumentos homogêneos.

## Funções como Valores

Em Go, funções são valores de primeira classe:

```go
// Tipo de função
type Transformador func(int) int

// Função que recebe função como argumento (higher-order function)
func aplicar(numeros []int, fn Transformador) []int {
    resultado := make([]int, len(numeros))
    for i, v := range numeros {
        resultado[i] = fn(v)
    }
    return resultado
}

func dobrar(n int) int { return n * 2 }
func quadrado(n int) int { return n * n }

func main() {
    numeros := []int{1, 2, 3, 4, 5}

    dobrados := aplicar(numeros, dobrar)
    fmt.Println(dobrados) // [2 4 6 8 10]

    quadrados := aplicar(numeros, quadrado)
    fmt.Println(quadrados) // [1 4 9 16 25]

    // Função anônima inline
    triplicados := aplicar(numeros, func(n int) int { return n * 3 })
    fmt.Println(triplicados) // [3 6 9 12 15]
}
```

***

# Structs e Modelagem

## Structs

Struct é a forma de criar tipos compostos em Go. Não existe classe. Não existe herança. Só structs e composição.

```go
// Definição de struct
type Usuario struct {
    ID    int
    Nome  string
    Email string
    Ativo bool
}

// Inicialização
u1 := Usuario{
    ID:    1,
    Nome:  "Alice",
    Email: "alice@exemplo.com",
    Ativo: true,
}

// Inicialização posicional (não recomendada - frágil a mudanças)
u2 := Usuario{1, "Bob", "bob@exemplo.com", false}

// Zero value de struct
var u3 Usuario // todos os campos com zero values
```

**Comparação com classes em outras linguagens:**

Em Java ou Python, você teria uma classe com construtor, campos privados, getters/setters. Em Go, você tem uma struct e funções. O encapsulamento é por pacote, não por objeto: campos e funções com letra minúscula são privados ao pacote.

```go
// Convenção Go: campos exportados começam com maiúscula
// campos não exportados começam com minúscula
type conta struct {
    ID    int    // exportado
    saldo float64 // não exportado (privado ao pacote)
}
```

## Métodos

Método é uma função com um receiver (receptor):

```go
type Retangulo struct {
    Largura float64
    Altura  float64
}

// Método com value receiver
// Recebe UMA CÓPIA do Retangulo
func (r Retangulo) Area() float64 {
    return r.Largura * r.Altura
}

// Método com pointer receiver
// Recebe o PONTEIRO para o Retangulo - pode modificar
func (r *Retangulo) Escalar(fator float64) {
    r.Largura *= fator
    r.Altura *= fator
}

func main() {
    ret := Retangulo{Largura: 10, Altura: 5}
    fmt.Println(ret.Area())    // 50

    ret.Escalar(2)
    fmt.Println(ret.Area())    // 200
}
```

## Value Receiver vs Pointer Receiver

Esta escolha tem implicações de performance e semântica:

**Use pointer receiver quando:**

- O método precisa modificar o receiver
- O struct é grande (evita cópia desnecessária)
- Por consistência: se algum método precisa de pointer receiver, todos deveriam usar

**Use value receiver quando:**

- O struct é pequeno (int, float, struct com 2-3 campos primitivos)
- O método não precisa modificar o receiver
- Você quer que o receiver seja imutável (o método recebe uma cópia)

```go
// Convenção: seja consistente
// Se um método usa pointer receiver, todos devem usar
type Ponto struct{ X, Y float64 }

// INCONSISTENTE (não faça isso):
func (p Ponto) String() string { ... }    // value
func (p *Ponto) Mover(dx, dy float64) { ... } // pointer

// CONSISTENTE:
func (p *Ponto) String() string { ... }   // pointer
func (p *Ponto) Mover(dx, dy float64) { ... } // pointer
```

## Composição

Go não tem herança. Tem embedding (incorporação) e composição, que é mais flexível:

```go
// Animal base
type Animal struct {
    Nome string
}

func (a Animal) Respirar() {
    fmt.Printf("%s está respirando\n", a.Nome)
}

// Cachorro embeds Animal
type Cachorro struct {
    Animal        // embedding - não é herança, é composição
    Raca   string
}

// Cachorro tem um método próprio
func (c Cachorro) Latir() {
    fmt.Printf("%s: Au au!\n", c.Nome) // acessa Nome do Animal embedded
}

func main() {
    c := Cachorro{
        Animal: Animal{Nome: "Rex"},
        Raca:   "Labrador",
    }

    c.Respirar() // método promovido do Animal
    c.Latir()    // método próprio do Cachorro
    c.Animal.Respirar() // acesso explícito também funciona
}
```

A diferença entre embedding e herança é sutil mas importante:

- **Herança** cria uma relação "é-um" (Dog is-an Animal)
- **Embedding** cria uma relação "tem-um" com promoção de métodos (Dog has-an Animal, e os métodos de Animal são acessíveis diretamente)

Embedding não cria um tipo pai/filho. Você não pode usar um `Cachorro` onde um `Animal` é esperado (a menos que seja através de uma interface que ambos satisfaçam).

***

# Interfaces Profundamente

Interfaces em Go são diferentes de quase toda outra linguagem que as tem. Entender isso é crucial.

## Duck Typing e Interfaces Implícitas

Em Java, para implementar uma interface você declara explicitamente:

```java
// Java
class Cachorro implements Animal { ... }
```

Em Go, uma interface é satisfeita implicitamente:

```go
// Define a interface
type Barulhento interface {
    FazerBarulho() string
}

// Cachorro não declara que implementa Barulhento
// mas implementa o método FazerBarulho
type Cachorro struct{ Nome string }
func (c Cachorro) FazerBarulho() string { return "Au!" }

// Gato também, sem declarar nada
type Gato struct{ Nome string }
func (g Gato) FazerBarulho() string { return "Miau!" }

// Qualquer tipo com FazerBarulho() string satisfaz Barulhento
func fazerBarulhos(animais []Barulhento) {
    for _, a := range animais {
        fmt.Println(a.FazerBarulho())
    }
}

func main() {
    animais := []Barulhento{
        Cachorro{Nome: "Rex"},
        Gato{Nome: "Mimi"},
    }
    fazerBarulhos(animais) // Au!, Miau!
}
```

**Por que isso é poderoso?** Porque você pode criar interfaces para tipos que você não controla. Um tipo em outra biblioteca que tem o método certo automaticamente satisfaz sua interface.

```go
// A interface io.Writer da biblioteca padrão é apenas:
type Writer interface {
    Write(p []byte) (n int, err error)
}

// Qualquer coisa que implementa Write() satisfaz io.Writer:
// os.File satisfaz
// bytes.Buffer satisfaz
// net.Conn satisfaz
// http.ResponseWriter satisfaz
// Seus próprios tipos também podem satisfazer
```

## Interfaces Pequenas

A comunidade Go tem uma preferência forte por interfaces pequenas. A famosa frase de Rob Pike:

> "The bigger the interface, the weaker the abstraction."

Interfaces com um ou dois métodos são mais reutilizáveis porque mais tipos as satisfazem:

```go
// Ruim: interface gigante - poucos tipos satisfazem isso
type Repositorio interface {
    Criar(u Usuario) error
    Buscar(id int) (Usuario, error)
    Listar() ([]Usuario, error)
    Atualizar(u Usuario) error
    Deletar(id int) error
    BuscarPorEmail(email string) (Usuario, error)
    // ... mais 10 métodos
}

// Melhor: interfaces focadas
type Criador interface {
    Criar(u Usuario) error
}

type Buscador interface {
    Buscar(id int) (Usuario, error)
}

type RepositorioCompleto interface {
    Criador
    Buscador
    // ...
}
// A função que só precisa criar aceita Criador - mais testável, mais flexível
```

## Interface Vazia e any

```go
// interface{} aceita qualquer tipo - é o "any" do Go
// A partir do Go 1.18, "any" é um alias para interface{}
func imprimirQualquer(v interface{}) {
    fmt.Printf("tipo: %T, valor: %v\n", v, v)
}

// Ou com any (preferível no Go moderno)
func imprimirQualquer(v any) {
    fmt.Printf("tipo: %T, valor: %v\n", v, v)
}

imprimirQualquer(42)       // tipo: int, valor: 42
imprimirQualquer("hello")  // tipo: string, valor: hello
imprimirQualquer([]int{1}) // tipo: []int, valor: [1]
```

**Cuidado com interface vazia:** quando você aceita `any`, você perde a verificação de tipos em compile time. É útil em casos como serialização, containers genéricos (antes de Go ter generics), e código de infraestrutura. Não use no código de domínio.

## Type Assertion e Type Switch

```go
// Type assertion: extrai o valor concreto de uma interface
var i interface{} = "hello"

// Forma "segura" - retorna valor e ok
s, ok := i.(string)
if ok {
    fmt.Println(s) // "hello"
}

// Forma "unsafe" - panic se o tipo não bater
s2 := i.(string) // ok
n := i.(int)     // panic! i não é int

// Type switch: pattern mais idiomático para múltiplos tipos
func processar(v interface{}) string {
    switch val := v.(type) {
    case nil:
        return "nulo"
    case int:
        return fmt.Sprintf("inteiro: %d", val)
    case float64:
        return fmt.Sprintf("float: %.2f", val)
    case string:
        return fmt.Sprintf("string: %q", val)
    case []int:
        return fmt.Sprintf("slice de int com %d elementos", len(val))
    default:
        return fmt.Sprintf("tipo desconhecido: %T", val)
    }
}
```

## Interface Nil: Uma Armadilha Sutil

```go
// ARMADILHA CLÁSSICA
type MeuErro struct{ Msg string }
func (e *MeuErro) Error() string { return e.Msg }

func podeRetornarNil() error {
    var err *MeuErro = nil // ponteiro nil para MeuErro
    return err             // retorna como interface error
    // MAS: a interface retornada NÃO é nil!
    // Ela tem tipo (*MeuErro) com valor nil
}

func main() {
    err := podeRetornarNil()
    if err != nil {
        fmt.Println("erro!") // isso EXECUTA - err não é nil!
    }
}
```

Por que? Uma interface em Go internamente tem dois campos: o tipo e o valor. Uma interface só é `nil` quando **ambos** são `nil`. Quando você retorna `(*MeuErro)(nil)` como `error`, o tipo é `*MeuErro` (não nil) mesmo que o valor seja nil.

**Solução:** sempre retorne `nil` diretamente, não um ponteiro tipado nil:

```go
func podeRetornarNil() error {
    // ...
    if semErro {
        return nil // retorna interface nil diretamente
    }
    return &MeuErro{"algo deu errado"}
}
```

***

# Arrays, Slices e Maps

## Arrays

Arrays em Go têm tamanho fixo e fazem parte do tipo:

```go
var a [5]int          // array de 5 ints - zero value
b := [3]string{"a", "b", "c"}
c := [...]int{1, 2, 3, 4} // ... infere o tamanho (4)

fmt.Println(len(b)) // 3

// [3]string e [4]string são TIPOS DIFERENTES
// Não são compatíveis entre si

// Arrays são copiados quando atribuídos ou passados para funções
x := [3]int{1, 2, 3}
y := x   // cópia completa
y[0] = 99
fmt.Println(x[0]) // 1 - não mudou
```

Na prática, você raramente usa arrays diretamente em Go. Slices são a estrutura dominante.

## Slices: O Coração do Go

Slice é uma abstração sobre array. Entender a estrutura interna de um slice é fundamental:

```plain
Slice internamente:
┌──────────────────────┐
│ ptr → array subjacente│  ponteiro para o array
│ len = comprimento     │  quantos elementos são visíveis
│ cap = capacidade      │  quantos elementos o array tem no total
└──────────────────────┘
```

```go
// Criando slices
s1 := []int{1, 2, 3}         // slice literal
s2 := make([]int, 5)          // len=5, cap=5, zeros
s3 := make([]int, 3, 10)      // len=3, cap=10

fmt.Println(len(s1), cap(s1)) // 3, 3
fmt.Println(len(s2), cap(s2)) // 5, 5
fmt.Println(len(s3), cap(s3)) // 3, 10

// Slicing: cria um novo slice que aponta para o mesmo array
a := []int{1, 2, 3, 4, 5}
b := a[1:3]  // b = [2, 3], len=2, cap=4 (do índice 1 ao fim do array)
b[0] = 99
fmt.Println(a) // [1, 99, 3, 4, 5] - MODIFICOU O ORIGINAL!
```

**Por que modificar o sub-slice modifica o original?** Porque ambos apontam para o mesmo array subjacente. Essa é a armadilha mais comum com slices.

```go
// Para fazer uma cópia independente:
c := make([]int, len(b))
copy(c, b)
// ou mais conciso:
c := append([]int(nil), b...)
```

## append e Crescimento de Capacidade

```go
s := make([]int, 0, 3) // len=0, cap=3

// append adiciona elementos
s = append(s, 1)    // len=1, cap=3 (ainda cabe)
s = append(s, 2, 3) // len=3, cap=3 (cheio)
s = append(s, 4)    // len=4, cap=6 (dobrou!)
// Quando cap está cheio, Go aloca um novo array maior
// e copia todos os elementos para ele
// A capacidade geralmente dobra (mas o algoritmo é mais complexo em versões recentes)
```

**Implications de performance:**

Cada realocação é uma alocação de heap + cópia de todos os elementos. Para slices grandes, isso pode ser custoso. Se você sabe o tamanho final, pré-aloque:

```go
// Ruim: muitas realocações
resultado := []int{}
for i := 0; i < 1000; i++ {
    resultado = append(resultado, i)
}
// Realoca ~10 vezes (3, 6, 12, 24, 48, 96, 192, 384, 768, 1536)

// Bom: uma única alocação
resultado := make([]int, 0, 1000)
for i := 0; i < 1000; i++ {
    resultado = append(resultado, i)
}
// Nenhuma realocação
```

**Armadilha de memória com sub-slices:**

```go
// Lê um arquivo de 1GB em memória
dados := lerArquivoGrande() // []byte, 1GB

// Extrai os primeiros 100 bytes
header := dados[:100]

// PROBLEMA: header ainda referencia o array de 1GB!
// O GC não pode liberar o 1GB enquanto header existir.
dados = nil // isso NÃO libera - header ainda aponta para o array

// SOLUÇÃO: copie o que precisa
header = append([]byte(nil), dados[:100]...)
dados = nil // agora o 1GB pode ser coletado
```

## Maps

Maps em Go são tabelas hash implementadas com uma estrutura de buckets:

```go
// Criação
m1 := map[string]int{
    "alice": 30,
    "bob":   25,
}

m2 := make(map[string]int) // map vazio, pronto para uso

// Operações básicas
m1["carol"] = 35         // inserção/atualização
idade := m1["alice"]      // leitura (30)
delete(m1, "bob")         // remoção

// IMPORTANTE: leitura de chave inexistente retorna zero value
// Não retorna erro, não causa panic
x := m1["ninguem"] // x = 0 (zero value de int)

// Para saber se a chave existe:
valor, ok := m1["alice"]
if ok {
    fmt.Println("alice tem", valor, "anos")
} else {
    fmt.Println("alice não encontrada")
}
```

**Ordem de iteração é aleatória por design:**

```go
m := map[string]int{"c": 3, "a": 1, "b": 2}
for k, v := range m {
    fmt.Println(k, v) // ordem não determinística
}
// Isso é intencional: evita que código dependa de ordem acidental
// Para ordem determinística, ordene as chaves:
keys := make([]string, 0, len(m))
for k := range m {
    keys = append(keys, k)
}
sort.Strings(keys)
for _, k := range keys {
    fmt.Println(k, m[k])
}
```

**Maps não são thread-safe.** Leituras concorrentes são ok, mas leituras e escritas concorrentes causam panic:

```go
// PANIC: concurrent map read and map write
m := map[string]int{}
go func() { m["a"] = 1 }()
go func() { _ = m["a"] }()
// Use sync.Map ou sync.RWMutex para acesso concorrente
```

**Internamente, como maps funcionam:**

Um map em Go é implementado como um array de buckets. Cada bucket guarda até 8 pares chave-valor. Quando você acessa uma chave, o hash da chave determina qual bucket verificar. Com muitas colisões ou muitas entradas, os buckets transbordam para buckets extras encadeados. Quando o fator de carga excede \~6.5, o map é redimensionado e todos os elementos são redistribuídos. Esse redimensionamento é incremental para evitar pauses longas.

***

# Concorrência em Go

Esta é a seção mais importante da linguagem. Go foi projetada com concorrência no centro.

## Goroutines

Uma goroutine é uma thread leve gerenciada pelo runtime do Go. A analogia comum é: goroutines são para o Go o que threads são para o OS, mas ordens de magnitude mais leves.

```go
// Iniciar uma goroutine é trivial: keyword go + chamada de função
func fazerAlgo() {
    fmt.Println("fazendo algo em paralelo")
}

func main() {
    go fazerAlgo() // inicia goroutine - retorna imediatamente

    // goroutine anônima
    go func() {
        fmt.Println("goroutine anônima")
    }()

    time.Sleep(time.Millisecond) // espera as goroutines (jeito errado)
    fmt.Println("fim")
}
```

**Por que goroutines são tão baratas?**

Uma thread do OS começa com \~1-8MB de stack. Uma goroutine começa com \~2KB de stack. O runtime do Go pode ter centenas de milhares de goroutines ativas ao mesmo tempo.

A stack de goroutines também é **segmentada e dinâmica**: ela começa pequena e cresce conforme necessário (até um limite configurável, padrão 1GB). Não há desperdício de memória por goroutines que nunca usam muita stack.

## O Scheduler do Go (M:N Threading)

O runtime do Go usa um modelo M:N: M goroutines mapeadas para N threads do OS. Isso é gerenciado pelo scheduler do Go, que implementa o algoritmo GOMAXPROCS.

```plain
Modelo M:N do Go:

Goroutines (leves, milhares)
    G1  G2  G3  G4  G5  G6  G7  G8
     \  |  /     \  |  /     \ | /
      [ P1 ]      [ P2 ]      [ P3 ]
      (fila)      (fila)      (fila)
         \           |           /
          \          |          /
        [Thread OS] [Thread OS] [Thread OS]
              (M1)     (M2)      (M3)
```

**Os três componentes principais:**

- **G (Goroutine):** a unidade de concorrência - tem stack, estado, código a executar
- **M (Machine/Thread):** thread do OS - executa goroutines
- **P (Processor):** contexto de execução - tem uma fila de goroutines prontas para rodar. `GOMAXPROCS` define quantos P existem (padrão = número de CPUs)

**O scheduler funciona assim:**

1. Cada P tem uma fila local de goroutines prontas
2. M executa goroutines da fila do seu P
3. Quando uma goroutine faz uma operação bloqueante (I/O, sleep, channel op), ela é suspensa e M pode pegar outra goroutine
4. Se um P fica sem trabalho, ele tenta "roubar" goroutines de outros P (work stealing)

Isso é cooperativo-preemptivo: goroutines cedem voluntariamente em pontos de preempção (function calls, channel ops, etc.), mas o scheduler também pode preemptá-las em pontos seguros desde Go 1.14.

```go
import "runtime"

func main() {
    // Quantas CPUs usar (padrão: todas)
    runtime.GOMAXPROCS(4)

    // Número de goroutines ativas
    fmt.Println(runtime.NumGoroutine())
}
```

## Channels

Channels são o mecanismo de comunicação entre goroutines. O lema do Go:

> "Do not communicate by sharing memory; instead, share memory by communicating."

```go
// Channel unidirecional de ints
ch := make(chan int)

// Channel bufferizado
buffered := make(chan int, 10) // buffer de 10 elementos

// Envio (bloqueia se o channel estiver cheio)
ch <- 42

// Recebimento (bloqueia se o channel estiver vazio)
v := <-ch

// Fechar um channel
close(ch)

// Receber com verificação de fechamento
v, ok := <-ch
if !ok {
    fmt.Println("channel fechado")
}
```

**Channel não bufferizado vs bufferizado:**

```plain
Channel não bufferizado (make(chan int)):
- Envio bloqueia até alguém receber
- Recebimento bloqueia até alguém enviar
- Sincronização "ponto a ponto"

Channel bufferizado (make(chan int, N)):
- Envio bloqueia apenas quando buffer está cheio
- Recebimento bloqueia apenas quando buffer está vazio
- N operações podem acontecer sem sincronização
```

**Exemplo prático: producer-consumer**

```go
func produtor(ch chan<- int, n int) {
    // chan<- : channel somente de envio (restrição de tipo)
    for i := 0; i < n; i++ {
        ch <- i
        fmt.Printf("produziu: %d\n", i)
    }
    close(ch) // sinaliza que não há mais valores
}

func consumidor(ch <-chan int, done chan<- bool) {
    // <-chan : channel somente de recebimento
    for v := range ch { // range em channel: recebe até o channel fechar
        fmt.Printf("consumiu: %d\n", v)
    }
    done <- true
}

func main() {
    ch := make(chan int, 5) // buffer de 5
    done := make(chan bool)

    go produtor(ch, 10)
    go consumidor(ch, done)

    <-done // espera consumidor terminar
    fmt.Println("pronto")
}
```

## select

`select` é para channels o que `switch` é para valores:

```go
// select espera em múltiplos channels e executa o primeiro que estiver pronto
func multiplosChannels(ch1, ch2 <-chan string) {
    for {
        select {
        case msg1 := <-ch1:
            fmt.Println("do ch1:", msg1)
        case msg2 := <-ch2:
            fmt.Println("do ch2:", msg2)
        }
    }
}

// select com timeout
func comTimeout(ch <-chan int) {
    select {
    case v := <-ch:
        fmt.Println("recebeu:", v)
    case <-time.After(5 * time.Second):
        fmt.Println("timeout!")
    }
}

// select com default: não bloqueia
func naoBloqueia(ch <-chan int) {
    select {
    case v := <-ch:
        fmt.Println("recebeu:", v)
    default:
        fmt.Println("channel vazio, continuando...")
    }
}
```

## Deadlocks

Um deadlock ocorre quando todas as goroutines estão bloqueadas esperando umas pelas outras:

```go
// DEADLOCK clássico
func main() {
    ch := make(chan int)
    ch <- 42 // bloqueia - ninguém está recebendo!
    // O runtime detecta isso: "all goroutines are asleep - deadlock!"
}

// DEADLOCK com dois channels
func main() {
    ch1 := make(chan int)
    ch2 := make(chan int)

    go func() {
        v := <-ch1    // espera ch1
        ch2 <- v
    }()

    go func() {
        v := <-ch2    // espera ch2
        ch1 <- v      // ch2 nunca tem valor porque ch1 está esperando ch2
    }()

    select {} // espera para sempre - deadlock
}
```

O runtime Go detecta deadlocks quando **todas** as goroutines estão dormindo. Mas se uma goroutine está em loop infinito ou há goroutines de sistema rodando (como o servidor HTTP), o runtime não detecta.

## Race Conditions

Race condition ocorre quando duas goroutines acessam a mesma memória concorrentemente e ao menos uma está escrevendo:

```go
// RACE CONDITION clássica
contador := 0

var wg sync.WaitGroup
for i := 0; i < 1000; i++ {
    wg.Add(1)
    go func() {
        defer wg.Done()
        contador++ // RACE! Leitura + incremento + escrita não são atômicas
    }()
}
wg.Wait()
fmt.Println(contador) // Resultado imprevisível: pode ser qualquer número abaixo de 1000
```

**Detecte com o race detector:**

```bash
go run -race main.go
# ou
go test -race ./...
```

O race detector do Go é extraordinariamente bom - detecta races em tempo de execução adicionando instrumentação. O overhead é \~5-10x de CPU e \~5-10x de memória, mas para testes é essencial.

## Mutex

```go
import "sync"

type ContadorSeguro struct {
    mu    sync.Mutex // protege o campo abaixo
    valor int
}

func (c *ContadorSeguro) Incrementar() {
    c.mu.Lock()         // adquire o lock
    defer c.mu.Unlock() // libera quando a função retornar
    c.valor++
}

func (c *ContadorSeguro) Valor() int {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.valor
}

// RWMutex: múltiplas leituras, escrita exclusiva
type Cache struct {
    mu   sync.RWMutex
    data map[string]string
}

func (c *Cache) Get(key string) (string, bool) {
    c.mu.RLock()         // múltiplos leitores podem entrar simultaneamente
    defer c.mu.RUnlock()
    v, ok := c.data[key]
    return v, ok
}

func (c *Cache) Set(key, value string) {
    c.mu.Lock()         // escritor tem exclusividade total
    defer c.mu.Unlock()
    c.data[key] = value
}
```

**Convenção importante:** o mutex deve ser declarado próximo ao dado que protege. Adicione um comentário indicando o que o mutex protege.

## sync.WaitGroup

```go
// WaitGroup: espere N goroutines terminarem
var wg sync.WaitGroup

for i := 0; i < 10; i++ {
    wg.Add(1) // incrementa o contador
    go func(id int) {
        defer wg.Done() // decrementa quando terminar
        fmt.Printf("goroutine %d trabalhando\n", id)
        time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
    }(i)
}

wg.Wait() // bloqueia até contador chegar a 0
fmt.Println("todas as goroutines terminaram")
```

## Worker Pool

Pattern fundamental para limitar concorrência:

```go
// Worker pool: N workers processando uma fila de tarefas
func workerPool(numWorkers int, tarefas <-chan int, resultados chan<- int) {
    var wg sync.WaitGroup

    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        go func(workerID int) {
            defer wg.Done()
            for tarefa := range tarefas { // recebe até o channel fechar
                // processa a tarefa
                resultado := tarefa * tarefa // exemplo: calcula quadrado
                resultados <- resultado
            }
        }(i)
    }

    // Quando todos os workers terminarem, fecha o channel de resultados
    go func() {
        wg.Wait()
        close(resultados)
    }()
}

func main() {
    tarefas := make(chan int, 100)
    resultados := make(chan int, 100)

    // Inicia o pool com 5 workers
    workerPool(5, tarefas, resultados)

    // Envia tarefas
    go func() {
        for i := 0; i < 20; i++ {
            tarefas <- i
        }
        close(tarefas) // sinaliza que não há mais tarefas
    }()

    // Coleta resultados
    for r := range resultados {
        fmt.Println(r)
    }
}
```

## Pipeline

Pattern de encadeamento de goroutines:

```go
// Cada estágio do pipeline recebe de um channel e envia para outro

// Estágio 1: gera números
func gerador(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

// Estágio 2: calcula quadrados
func quadrados(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}

// Estágio 3: filtra números maiores que N
func filtrar(in <-chan int, minimo int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            if n > minimo {
                out <- n
            }
        }
        close(out)
    }()
    return out
}

func main() {
    // Pipeline: gerar → quadrar → filtrar
    nums := gerador(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    sq := quadrados(nums)
    filtered := filtrar(sq, 25)

    for v := range filtered {
        fmt.Println(v) // 36, 49, 64, 81, 100
    }
}
```

## Concorrência vs Paralelismo

Rob Pike tem uma fala famosa sobre isso: _"Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once."_

Em Go:

- **Concorrência** é sobre estrutura: você escreve código com goroutines que podem se intercalar
- **Paralelismo** é sobre execução: múltiplas goroutines realmente rodando ao mesmo tempo em CPUs diferentes

Com `GOMAXPROCS=1`, seu código Go é concorrente mas não paralelo (apenas um CPU). Com `GOMAXPROCS=8`, o scheduler pode executar até 8 goroutines verdadeiramente em paralelo.

***

# Context Package

O pacote `context` resolve um problema real: como cancelar operações encadeadas de forma limpa.

Imagine: uma requisição HTTP chega. Ela inicia uma query no banco de dados, que inicia uma chamada para um serviço externo. Se o cliente desconectar, você quer cancelar tudo isso. O `context.Context` propaga esse cancelamento.

```go
import "context"

// Contexto básico - raiz de todos os contextos
ctx := context.Background() // nunca é cancelado, raiz
ctx2 := context.TODO()      // placeholder - não use em produção

// Contexto com cancelamento
ctx, cancel := context.WithCancel(context.Background())
defer cancel() // sempre faça isso para liberar recursos

// Contexto com timeout
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

// Contexto com deadline absoluto
deadline := time.Now().Add(30 * time.Second)
ctx, cancel := context.WithDeadline(context.Background(), deadline)
defer cancel()

// Contexto com valor (use com moderação)
type chaveUsuario string
ctx = context.WithValue(ctx, chaveUsuario("userID"), 42)
userID := ctx.Value(chaveUsuario("userID")).(int)
```

**Uso em servidores HTTP:**

```go
func handler(w http.ResponseWriter, r *http.Request) {
    // r.Context() já contém o contexto da requisição
    // É cancelado quando o cliente desconecta
    ctx := r.Context()

    resultado, err := buscarDados(ctx, "query")
    if err != nil {
        if errors.Is(err, context.Canceled) {
            // cliente cancelou - sem log de erro
            return
        }
        http.Error(w, err.Error(), 500)
        return
    }
    // ...
}

func buscarDados(ctx context.Context, query string) (string, error) {
    // Cria query no banco respeitando o contexto
    rows, err := db.QueryContext(ctx, "SELECT ...", query)
    if err != nil {
        return "", fmt.Errorf("ao buscar dados: %w", err)
    }
    // ...

    // Verifica se o contexto foi cancelado antes de continuar
    select {
    case <-ctx.Done():
        return "", ctx.Err()
    default:
        // continua
    }
    // ...
}
```

**Regras do context:**

1. Sempre passe `ctx` como **primeiro** argumento de funções
2. Nunca guarde `ctx` em structs (passe como argumento)
3. Sempre chame `cancel()` quando criar um contexto cancelável (geralmente com `defer`)
4. Use `context.WithValue` com parcimônia - apenas para dados transversais como request IDs, não para parâmetros funcionais

***

# Tratamento de Erros

O tratamento de erros em Go é o tópico que mais divide opiniões. Vamos entender o design e as melhores práticas.

## Errors são Valores

```go
// A interface error tem apenas um método
type error interface {
    Error() string
}

// errors.New cria um erro simples
import "errors"
err := errors.New("algo deu errado")

// fmt.Errorf cria um erro formatado
err2 := fmt.Errorf("falha ao processar id %d: %w", 42, err)
// %w envolve o erro original (wrapping) - disponível desde Go 1.13
```

## Criando Tipos de Erro Customizados

```go
// Erro simples como variável (sentinel error)
var ErrNaoEncontrado = errors.New("não encontrado")

// Tipo de erro rico com contexto
type ErroValidacao struct {
    Campo   string
    Motivo  string
}

func (e *ErroValidacao) Error() string {
    return fmt.Sprintf("campo %q inválido: %s", e.Campo, e.Motivo)
}

// Uso
func validarEmail(email string) error {
    if !strings.Contains(email, "@") {
        return &ErroValidacao{
            Campo:  "email",
            Motivo: "deve conter @",
        }
    }
    return nil
}
```

## errors.Is e errors.As

Desde Go 1.13, há um sistema de unwrapping de erros:

```go
// errors.Is: verifica se um erro (ou algum na cadeia) é específico
err := fmt.Errorf("operação falhou: %w", ErrNaoEncontrado)

if errors.Is(err, ErrNaoEncontrado) {
    fmt.Println("recurso não encontrado") // imprime isso
}

// errors.As: extrai um tipo específico da cadeia de erros
var errValidacao *ErroValidacao
if errors.As(err, &errValidacao) {
    fmt.Println("campo com erro:", errValidacao.Campo)
}
```

**O padrão de wrapping:**

```go
// Sempre adicione contexto ao propagar erros
func buscarUsuario(id int) (*Usuario, error) {
    u, err := db.QueryOne("SELECT ...", id)
    if err != nil {
        // %w "envolve" o erro original
        return nil, fmt.Errorf("ao buscar usuário %d: %w", id, err)
    }
    return u, nil
}

func processarRequisicao(userID int) error {
    usuario, err := buscarUsuario(userID)
    if err != nil {
        return fmt.Errorf("ao processar requisição: %w", err)
    }
    // ...
}

// No topo da pilha de chamadas, você tem um contexto rico:
// "ao processar requisição: ao buscar usuário 42: sql: no rows in result set"
```

## panic e recover

`panic` é para situações verdadeiramente excepcionais - não para controle de fluxo normal:

```go
// Use panic para:
// - Erros de programação (índice fora dos limites, nil pointer que não deveria ser nil)
// - Invariantes violados que indicam bug, não condição de erro

func assertNaoNulo(v interface{}, msg string) {
    if v == nil {
        panic("invariante violada: " + msg)
    }
}

// recover captura um panic em andamento
// Só funciona chamado diretamente de um defer
func seguro(fn func()) (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("panic capturado: %v", r)
        }
    }()
    fn()
    return nil
}

// Servidores HTTP da stdlib já fazem recover em cada handler
// para que um panic num handler não derrube o servidor inteiro
```

**Regra prática:** se você se encontra usando `panic` para controle de fluxo, algo está errado no design. Erros previsíveis (usuário não encontrado, conexão falhou, input inválido) devem ser retornados como `error`. Panics são para condições que nunca deveriam acontecer em produção.

## Padrão de Tratamento Idiomático

```go
// Padrão mais comum: tratar erro imediatamente
func processarArquivo(caminho string) error {
    f, err := os.Open(caminho)
    if err != nil {
        return fmt.Errorf("ao abrir arquivo: %w", err)
    }
    defer f.Close()

    scanner := bufio.NewScanner(f)
    for scanner.Scan() {
        linha := scanner.Text()
        if err := processarLinha(linha); err != nil {
            return fmt.Errorf("ao processar linha %q: %w", linha, err)
        }
    }

    if err := scanner.Err(); err != nil {
        return fmt.Errorf("ao ler arquivo: %w", err)
    }

    return nil
}
```

Parece verboso comparado ao try/catch de Java. Mas cada tratamento de erro é explícito - você sabe exatamente o que pode falhar em cada ponto. Não há "surpresas" de exceções pulando várias camadas.

***

# Organização de Projetos

## go mod

Go Modules são o sistema de gerenciamento de dependências desde Go 1.11 (estável em Go 1.16):

```bash
# Inicializa um módulo
go mod init github.com/seunome/meumodulo

# go.mod criado:
# module github.com/seunome/meumodulo
#
# go 1.22
```

```bash
# Adiciona uma dependência
go get github.com/gin-gonic/gin@v1.9.0

# Atualiza todas as dependências para versões menores/patches
go get -u ./...

# Remove dependências não usadas, adiciona faltantes
go mod tidy

# go.sum: arquivo de verificação de integridade
# Não edite manualmente - é gerenciado automaticamente
```

**go.mod e go.sum:**

```plain
# go.mod - define o módulo e suas dependências
module github.com/seunome/servidor

go 1.22

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/jackc/pgx/v5 v5.5.0
)

# go.sum - checksums criptográficos de cada dependência
# Garante reprodutibilidade: mesma build em qualquer máquina
```

## Estrutura de Projeto Moderna

Não existe uma estrutura oficial obrigatória, mas a comunidade convergiu para alguns padrões:

```plain
meu-projeto/
├── cmd/                    # pontos de entrada (binários)
│   ├── api/
│   │   └── main.go         # servidor API
│   └── worker/
│       └── main.go         # worker de background
├── internal/               # código privado - não importável por módulos externos
│   ├── domain/             # entidades e regras de negócio
│   │   ├── usuario.go
│   │   └── pedido.go
│   ├── repository/         # acesso a dados
│   │   ├── usuario_repo.go
│   │   └── pedido_repo.go
│   ├── service/            # lógica de aplicação
│   │   ├── usuario_service.go
│   │   └── pedido_service.go
│   └── handler/            # HTTP handlers
│       ├── usuario_handler.go
│       └── middleware.go
├── pkg/                    # código público reutilizável (use com cautela)
│   └── validator/
│       └── validator.go
├── migrations/             # migrations de banco de dados
├── scripts/                # scripts de build, deploy
├── configs/                # arquivos de configuração
├── go.mod
├── go.sum
├── Makefile
└── README.md
```

**Por que `internal`?** O diretório `internal` tem semântica especial no Go: apenas código no módulo pai pode importar pacotes dentro de `internal`. Isso cria uma fronteira forte: `internal/repository` só pode ser usado por código dentro de `meu-projeto`, nunca por dependentes externos.

**cmd/ é para binários:** cada subdiretório de `cmd/` tem seu próprio `main.go` e produz um binário separado. Um projeto pode ter múltiplos binários: a API, um worker, uma ferramenta de CLI.

***

# JSON e APIs

## encoding/json

O pacote `encoding/json` da stdlib é poderoso e bem projetado:

```go
import "encoding/json"

type Usuario struct {
    ID    int    `json:"id"`
    Nome  string `json:"nome"`
    Email string `json:"email"`
    Senha string `json:"-"`       // ignora este campo
    Ativo bool   `json:"ativo,omitempty"` // omite se false (zero value)
    Dados *Extra `json:"dados,omitempty"` // omite se nil
}

type Extra struct {
    Preferencias []string `json:"preferencias"`
}

// Serialização (struct → JSON)
u := Usuario{ID: 1, Nome: "Alice", Email: "alice@ex.com", Ativo: true}
data, err := json.Marshal(u)
// {"id":1,"nome":"Alice","email":"alice@ex.com","ativo":true}

// Com indentação (útil para debug e APIs)
data, err = json.MarshalIndent(u, "", "  ")

// Desserialização (JSON → struct)
jsonStr := `{"id":2,"nome":"Bob","email":"bob@ex.com"}`
var u2 Usuario
err = json.Unmarshal([]byte(jsonStr), &u2)
```

**Usando json.Decoder para streams (HTTP):**

```go
// Melhor para HTTP requests - evita ler tudo na memória
func decodificarRequisicao(r *http.Request, destino interface{}) error {
    decoder := json.NewDecoder(r.Body)
    decoder.DisallowUnknownFields() // rejeita campos desconhecidos
    return decoder.Decode(destino)
}
```

## Servidor HTTP

```go
package main

import (
    "encoding/json"
    "log"
    "net/http"
)

type Resposta struct {
    Status  string `json:"status"`
    Mensagem string `json:"mensagem"`
}

// Handler básico
func healthHandler(w http.ResponseWriter, r *http.Request) {
    // Configura headers antes de escrever o body
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)

    resp := Resposta{Status: "ok", Mensagem: "servidor funcionando"}
    json.NewEncoder(w).Encode(resp)
}

// Handler com contexto e logging
func criarUsuarioHandler(w http.ResponseWriter, r *http.Request) {
    if r.Method != http.MethodPost {
        http.Error(w, "método não permitido", http.StatusMethodNotAllowed)
        return
    }

    var usuario UsuarioInput
    if err := json.NewDecoder(r.Body).Decode(&usuario); err != nil {
        http.Error(w, "body inválido", http.StatusBadRequest)
        return
    }

    // ... processa ...
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusCreated)
    json.NewEncoder(w).Encode(usuario)
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/health", healthHandler)
    mux.HandleFunc("/usuarios", criarUsuarioHandler)

    servidor := &http.Server{
        Addr:         ":8080",
        Handler:      mux,
        ReadTimeout:  15 * time.Second,
        WriteTimeout: 15 * time.Second,
        IdleTimeout:  60 * time.Second,
    }

    log.Println("servidor iniciando em :8080")
    if err := servidor.ListenAndServe(); err != nil && err != http.ErrServerClosed {
        log.Fatal(err)
    }
}
```

## Middleware

```go
// Middleware é uma função que envolve um handler
type Middleware func(http.Handler) http.Handler

// Logger middleware
func loggerMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        inicio := time.Now()
        // Chama o próximo handler
        next.ServeHTTP(w, r)
        log.Printf("%s %s %v", r.Method, r.URL.Path, time.Since(inicio))
    })
}

// Auth middleware
func authMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        token := r.Header.Get("Authorization")
        if token == "" {
            http.Error(w, "não autorizado", http.StatusUnauthorized)
            return
        }
        // valida token...
        next.ServeHTTP(w, r)
    })
}

// Encadeando middlewares
func encadear(handler http.Handler, middlewares ...Middleware) http.Handler {
    for i := len(middlewares) - 1; i >= 0; i-- {
        handler = middlewares[i](handler)
    }
    return handler
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/api/usuarios", criarUsuarioHandler)

    // Aplica middlewares: logger → auth → handler
    handler := encadear(mux, loggerMiddleware, authMiddleware)

    http.ListenAndServe(":8080", handler)
}
```

***

# Banco de Dados

## database/sql

A interface `database/sql` é uma abstração que funciona com qualquer driver:

```go
import (
    "database/sql"
    _ "github.com/lib/pq" // driver PostgreSQL - side-effects import
)

func conectar() (*sql.DB, error) {
    dsn := "postgres://user:pass@localhost/meubanco?sslmode=disable"
    db, err := sql.Open("postgres", dsn)
    if err != nil {
        return nil, fmt.Errorf("ao abrir conexão: %w", err)
    }

    // Configurações do pool de conexões
    db.SetMaxOpenConns(25)               // máximo de conexões abertas
    db.SetMaxIdleConns(10)               // máximo de conexões idle
    db.SetConnMaxLifetime(5 * time.Minute) // tempo máximo de uma conexão

    // Verifica a conexão
    if err := db.Ping(); err != nil {
        return nil, fmt.Errorf("ao pingar banco: %w", err)
    }

    return db, nil
}
```

**CRUD básico:**

```go
// INSERT
func criarUsuario(db *sql.DB, ctx context.Context, u *Usuario) error {
    query := `INSERT INTO usuarios (nome, email) VALUES ($1, $2) RETURNING id`
    return db.QueryRowContext(ctx, query, u.Nome, u.Email).Scan(&u.ID)
}

// SELECT
func buscarUsuario(db *sql.DB, ctx context.Context, id int) (*Usuario, error) {
    query := `SELECT id, nome, email FROM usuarios WHERE id = $1`
    u := &Usuario{}
    err := db.QueryRowContext(ctx, query, id).Scan(&u.ID, &u.Nome, &u.Email)
    if errors.Is(err, sql.ErrNoRows) {
        return nil, ErrNaoEncontrado
    }
    if err != nil {
        return nil, fmt.Errorf("ao buscar usuário: %w", err)
    }
    return u, nil
}

// SELECT múltiplos
func listarUsuarios(db *sql.DB, ctx context.Context) ([]*Usuario, error) {
    query := `SELECT id, nome, email FROM usuarios ORDER BY nome`
    rows, err := db.QueryContext(ctx, query)
    if err != nil {
        return nil, fmt.Errorf("ao listar usuários: %w", err)
    }
    defer rows.Close() // SEMPRE feche rows

    var usuarios []*Usuario
    for rows.Next() {
        u := &Usuario{}
        if err := rows.Scan(&u.ID, &u.Nome, &u.Email); err != nil {
            return nil, fmt.Errorf("ao escanear usuário: %w", err)
        }
        usuarios = append(usuarios, u)
    }

    // Verifica erros de iteração
    if err := rows.Err(); err != nil {
        return nil, fmt.Errorf("ao iterar usuários: %w", err)
    }

    return usuarios, nil
}
```

**Transações:**

```go
func transferir(db *sql.DB, ctx context.Context, deID, paraID int, valor float64) error {
    tx, err := db.BeginTx(ctx, nil)
    if err != nil {
        return fmt.Errorf("ao iniciar transação: %w", err)
    }
    defer func() {
        if err != nil {
            tx.Rollback() // rollback em erro
        }
    }()

    // Debita
    _, err = tx.ExecContext(ctx,
        `UPDATE contas SET saldo = saldo - $1 WHERE id = $2 AND saldo >= $1`,
        valor, deID)
    if err != nil {
        return fmt.Errorf("ao debitar: %w", err)
    }

    // Credita
    _, err = tx.ExecContext(ctx,
        `UPDATE contas SET saldo = saldo + $1 WHERE id = $2`,
        valor, paraID)
    if err != nil {
        return fmt.Errorf("ao creditar: %w", err)
    }

    // Commit
    if err = tx.Commit(); err != nil {
        return fmt.Errorf("ao commitar transação: %w", err)
    }

    return nil
}
```

## ORMs vs SQL Puro

A comunidade Go é mais dividida nessa questão do que em outras linguagens. Os prós e contras reais:

**SQL puro com `database/sql` ou `sqlx`:**

- Controle total da query
- Fácil de otimizar
- Erros claros de banco de dados
- Nenhuma mágica implícita
- Mais verboso

**ORMs como GORM:**

- Produtividade inicial maior
- Queries geradas podem ser ineficientes
- Migrations automáticas (útil, perigoso em produção)
- Abstrai detalhes importantes
- Community love/hate intenso

**sqlc** (abordagem moderna): você escreve SQL, ele gera código Go tipado. O melhor dos dois mundos para muitos casos.

***

# Testing

## Unit Tests

```go
// usuario_test.go - mesmo pacote para testar internos
// ou usuario_test.go com package usuario_test para teste externo

package usuario

import "testing"

func TestValidarEmail(t *testing.T) {
    err := ValidarEmail("alice@exemplo.com")
    if err != nil {
        t.Errorf("esperava nil, obteve %v", err)
    }

    err = ValidarEmail("invalido")
    if err == nil {
        t.Error("esperava erro para email inválido")
    }
}

// Rodar: go test ./...
// Com verbose: go test -v ./...
// Test específico: go test -run TestValidarEmail ./...
```

## Table Driven Tests

O padrão mais idiomático para testes em Go:

```go
func TestValidarEmail(t *testing.T) {
    casos := []struct {
        nome  string
        email string
        erro  bool
    }{
        {"email válido", "alice@ex.com", false},
        {"sem arroba", "invalido", true},
        {"sem domínio", "alice@", true},
        {"vazio", "", true},
        {"com subdomínio", "a@sub.ex.com", false},
    }

    for _, c := range casos {
        // t.Run cria um sub-teste - é a forma correta
        t.Run(c.nome, func(t *testing.T) {
            err := ValidarEmail(c.email)
            if c.erro && err == nil {
                t.Errorf("esperava erro para %q, obteve nil", c.email)
            }
            if !c.erro && err != nil {
                t.Errorf("não esperava erro para %q, obteve %v", c.email, err)
            }
        })
    }
}
```

## Testando com Interfaces (Mocks)

A forma idiomática de mockar em Go é via interfaces, não frameworks de mock complexos:

```go
// Interface do repositório
type RepositorioUsuario interface {
    Buscar(id int) (*Usuario, error)
    Criar(u *Usuario) error
}

// Service que depende da interface
type UsuarioService struct {
    repo RepositorioUsuario
}

// Mock para testes
type mockRepositorio struct {
    usuarios map[int]*Usuario
}

func (m *mockRepositorio) Buscar(id int) (*Usuario, error) {
    if u, ok := m.usuarios[id]; ok {
        return u, nil
    }
    return nil, ErrNaoEncontrado
}

func (m *mockRepositorio) Criar(u *Usuario) error {
    m.usuarios[u.ID] = u
    return nil
}

// Teste usando o mock
func TestBuscarUsuario(t *testing.T) {
    mock := &mockRepositorio{
        usuarios: map[int]*Usuario{
            1: {ID: 1, Nome: "Alice"},
        },
    }
    svc := &UsuarioService{repo: mock}

    usuario, err := svc.Buscar(1)
    if err != nil {
        t.Fatal(err)
    }
    if usuario.Nome != "Alice" {
        t.Errorf("esperava Alice, obteve %s", usuario.Nome)
    }
}
```

## Benchmarks

```go
// Benchmark: nome deve começar com Benchmark
func BenchmarkValidarEmail(b *testing.B) {
    // b.N é definido automaticamente para produzir resultados estáveis
    for i := 0; i < b.N; i++ {
        ValidarEmail("alice@exemplo.com")
    }
}

// Rodar: go test -bench=. -benchmem ./...
// Output:
// BenchmarkValidarEmail-8    5000000    230 ns/op    48 B/op    2 allocs/op
//                            ^número    ^tempo        ^memória  ^alocações por op
```

`-benchmem` é essencial: mostra quantas alocações de heap acontecem por operação. Reduzir alocações é frequentemente a forma mais eficaz de melhorar performance em Go.

## Profiling com pprof

```go
import _ "net/http/pprof" // ativa endpoints de pprof

func main() {
    // Endpoint de profiling disponível em /debug/pprof/
    go http.ListenAndServe(":6060", nil)

    // ... resto do servidor ...
}
```

```bash
# CPU profiling: 30 segundos
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30

# Memory profiling
go tool pprof http://localhost:6060/debug/pprof/heap

# Goroutine profiling
go tool pprof http://localhost:6060/debug/pprof/goroutine

# Dentro do pprof interativo:
(pprof) top10      # top 10 funções por consumo
(pprof) web        # abre visualização gráfica no browser
(pprof) list NomeDaFuncao  # detalha função específica
```

Para visualização interativa, você precisa do Graphviz instalado. O comando `web` abre um grafo de call stack com cores indicando onde o tempo é gasto.

***

# Performance em Go

## Entendendo Alocações

O custo de alocação no heap não é apenas o malloc. É:

1. O tempo da alocação em si
2. O trabalho do GC para eventualmente coletar
3. Pressão de cache: objetos no heap são espalhados na memória

```go
// Ruim: aloca em todo loop
func processar(items []string) []string {
    var resultados []string          // alocação de slice nil
    for _, item := range items {
        resultados = append(resultados, transformar(item)) // possível realocação
    }
    return resultados
}

// Melhor: pré-aloca
func processar(items []string) []string {
    resultados := make([]string, 0, len(items)) // tamanho final conhecido
    for _, item := range items {
        resultados = append(resultados, transformar(item))
    }
    return resultados
}
```

## sync.Pool

Para objetos frequentemente alocados e descartados (buffers, parsers, etc.):

```go
var bufferPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

func processarRequisicao(data []byte) []byte {
    // Pega um buffer do pool (ou cria novo se pool estiver vazio)
    buf := bufferPool.Get().(*bytes.Buffer)
    buf.Reset() // limpa o buffer antes de usar
    defer bufferPool.Put(buf) // devolve ao pool quando terminar

    // Usa o buffer sem alocar
    buf.Write(data)
    // ...
    return buf.Bytes()
}
```

`sync.Pool` é limpo pelo GC entre cada ciclo, então não é adequado para cache de longa duração - apenas para reduzir pressão de alocação em código de alta frequência.

## Strings e []byte

```go
// RUIM: muitas alocações de string por concatenação
func construirURL(partes []string) string {
    resultado := ""
    for _, p := range partes {
        resultado += p // nova alocação a cada iteração
    }
    return resultado
}

// MELHOR: strings.Builder não aloca por concatenação
func construirURL(partes []string) string {
    var sb strings.Builder
    for _, p := range partes {
        sb.WriteString(p)
    }
    return sb.String() // uma única alocação no final
}

// AINDA MELHOR quando as partes são conhecidas:
return strings.Join(partes, "")
```

## Otimizações Idiomáticas

```go
// Usar structs em slices, não ponteiros, quando possível
// (locality de memória - CPU cache)
// Ruim para dados grandes e sparse, bom para dados pequenos e densos:
type PontoRuim struct{ X, Y float64 }
pontosRuim := []*PontoRuim{...}  // ponteiros espalhados no heap

type Ponto struct{ X, Y float64 }
pontos := []Ponto{...}           // dados contíguos - melhor cache locality

// Pré-computar comprimentos em loops
// (o compilador já faz isso, mas é boa prática)
n := len(slice)
for i := 0; i < n; i++ { ... }

// Usar io.Writer ao invés de acumular strings
// (especialmente para HTTP handlers)
func renderizar(w io.Writer, dados Dados) error {
    return template.Execute(w, dados) // escreve diretamente no ResponseWriter
}
```

***

# Padrões Idiomáticos

## Functional Options

Pattern para configuração de structs complexas:

```go
type Servidor struct {
    host    string
    porta   int
    timeout time.Duration
    maxConn int
}

// Opção é uma função que modifica o servidor
type OpcaoServidor func(*Servidor)

func ComHost(host string) OpcaoServidor {
    return func(s *Servidor) {
        s.host = host
    }
}

func ComPorta(porta int) OpcaoServidor {
    return func(s *Servidor) {
        s.porta = porta
    }
}

func ComTimeout(t time.Duration) OpcaoServidor {
    return func(s *Servidor) {
        s.timeout = t
    }
}

func NovoServidor(opcoes ...OpcaoServidor) *Servidor {
    s := &Servidor{
        host:    "localhost",   // defaults
        porta:   8080,
        timeout: 30 * time.Second,
        maxConn: 100,
    }
    for _, opt := range opcoes {
        opt(s)
    }
    return s
}

// Uso limpo e extensível
s := NovoServidor(
    ComHost("0.0.0.0"),
    ComPorta(9090),
    ComTimeout(60 * time.Second),
)
```

Este padrão é usado amplamente na stdlib do Go e em bibliotecas populares como `grpc-go`.

## Repository Pattern

```go
// Interface define o contrato
type RepositorioUsuario interface {
    Criar(ctx context.Context, u *Usuario) error
    Buscar(ctx context.Context, id int) (*Usuario, error)
    Listar(ctx context.Context, filtro Filtro) ([]*Usuario, error)
    Atualizar(ctx context.Context, u *Usuario) error
    Deletar(ctx context.Context, id int) error
}

// Implementação PostgreSQL
type postgresUsuarioRepo struct {
    db *sql.DB
}

func NovoPostgresUsuarioRepo(db *sql.DB) RepositorioUsuario {
    return &postgresUsuarioRepo{db: db}
}

func (r *postgresUsuarioRepo) Buscar(ctx context.Context, id int) (*Usuario, error) {
    // implementação...
}

// Service depende da interface, não da implementação
type UsuarioService struct {
    repo RepositorioUsuario
    log  *slog.Logger
}

func NovoUsuarioService(repo RepositorioUsuario, log *slog.Logger) *UsuarioService {
    return &UsuarioService{repo: repo, log: log}
}
```

## Graceful Shutdown

Todo servidor de produção precisa de graceful shutdown:

```go
func main() {
    srv := &http.Server{Addr: ":8080"}

    // Inicia servidor em goroutine
    go func() {
        if err := srv.ListenAndServe(); err != http.ErrServerClosed {
            log.Fatal(err)
        }
    }()

    // Espera sinal de término (SIGTERM para Kubernetes, SIGINT para Ctrl+C)
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    log.Println("desligando servidor...")

    // Dá 30 segundos para requests em andamento terminarem
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()

    if err := srv.Shutdown(ctx); err != nil {
        log.Fatal("erro no shutdown:", err)
    }

    log.Println("servidor parado")
}
```

***

# Projeto Real Completo

Vamos construir uma API REST para gerenciamento de tarefas com todos os conceitos aplicados.

## Estrutura

```plain
todo-api/
├── cmd/api/main.go
├── internal/
│   ├── domain/tarefa.go
│   ├── repository/tarefa_repo.go
│   ├── service/tarefa_service.go
│   └── handler/tarefa_handler.go
├── go.mod
└── Makefile
```

## domain/tarefa.go

```go
package domain

import (
    "errors"
    "time"
)

var (
    ErrNaoEncontrado = errors.New("não encontrado")
    ErrTituloVazio   = errors.New("título não pode ser vazio")
)

// Status da tarefa - tipo com validação implícita via constantes
type Status string

const (
    StatusPendente    Status = "pendente"
    StatusEmAndamento Status = "em_andamento"
    StatusConcluida   Status = "concluida"
)

// Tarefa é a entidade central do domínio
type Tarefa struct {
    ID        int       `json:"id"`
    Titulo    string    `json:"titulo"`
    Descricao string    `json:"descricao"`
    Status    Status    `json:"status"`
    CriadoEm time.Time `json:"criado_em"`
}

// Validate verifica invariantes de negócio
func (t *Tarefa) Validar() error {
    if t.Titulo == "" {
        return ErrTituloVazio
    }
    return nil
}

// Nova cria uma tarefa com defaults corretos
func Nova(titulo, descricao string) (*Tarefa, error) {
    t := &Tarefa{
        Titulo:    titulo,
        Descricao: descricao,
        Status:    StatusPendente,
        CriadoEm: time.Now(),
    }
    if err := t.Validar(); err != nil {
        return nil, err
    }
    return t, nil
}
```

## repository/tarefa_repo.go

```go
package repository

import (
    "context"
    "sync"
    "sync/atomic"

    "github.com/seunome/todo/internal/domain"
)

// Interface - define o contrato, não a implementação
type TarefaRepository interface {
    Criar(ctx context.Context, t *domain.Tarefa) error
    Buscar(ctx context.Context, id int) (*domain.Tarefa, error)
    Listar(ctx context.Context) ([]*domain.Tarefa, error)
    Atualizar(ctx context.Context, t *domain.Tarefa) error
    Deletar(ctx context.Context, id int) error
}

// InMemory: implementação em memória para desenvolvimento e testes
type inMemoryRepo struct {
    mu      sync.RWMutex
    tarefas map[int]*domain.Tarefa
    nextID  atomic.Int64
}

func NovoInMemoryRepo() TarefaRepository {
    return &inMemoryRepo{
        tarefas: make(map[int]*domain.Tarefa),
    }
}

func (r *inMemoryRepo) Criar(ctx context.Context, t *domain.Tarefa) error {
    r.mu.Lock()
    defer r.mu.Unlock()

    t.ID = int(r.nextID.Add(1))
    // Guarda uma cópia para evitar que o chamador modifique depois
    copia := *t
    r.tarefas[t.ID] = &copia
    return nil
}

func (r *inMemoryRepo) Buscar(ctx context.Context, id int) (*domain.Tarefa, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()

    t, ok := r.tarefas[id]
    if !ok {
        return nil, domain.ErrNaoEncontrado
    }
    // Retorna cópia - imutabilidade
    copia := *t
    return &copia, nil
}

func (r *inMemoryRepo) Listar(ctx context.Context) ([]*domain.Tarefa, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()

    tarefas := make([]*domain.Tarefa, 0, len(r.tarefas))
    for _, t := range r.tarefas {
        copia := *t
        tarefas = append(tarefas, &copia)
    }
    return tarefas, nil
}

func (r *inMemoryRepo) Atualizar(ctx context.Context, t *domain.Tarefa) error {
    r.mu.Lock()
    defer r.mu.Unlock()

    if _, ok := r.tarefas[t.ID]; !ok {
        return domain.ErrNaoEncontrado
    }
    copia := *t
    r.tarefas[t.ID] = &copia
    return nil
}

func (r *inMemoryRepo) Deletar(ctx context.Context, id int) error {
    r.mu.Lock()
    defer r.mu.Unlock()

    if _, ok := r.tarefas[id]; !ok {
        return domain.ErrNaoEncontrado
    }
    delete(r.tarefas, id)
    return nil
}
```

## service/tarefa_service.go

```go
package service

import (
    "context"
    "fmt"
    "log/slog"

    "github.com/seunome/todo/internal/domain"
    "github.com/seunome/todo/internal/repository"
)

type TarefaService struct {
    repo repository.TarefaRepository
    log  *slog.Logger
}

func NovoTarefaService(repo repository.TarefaRepository, log *slog.Logger) *TarefaService {
    return &TarefaService{repo: repo, log: log}
}

func (s *TarefaService) Criar(ctx context.Context, titulo, descricao string) (*domain.Tarefa, error) {
    tarefa, err := domain.Nova(titulo, descricao)
    if err != nil {
        return nil, fmt.Errorf("ao criar tarefa: %w", err)
    }

    if err := s.repo.Criar(ctx, tarefa); err != nil {
        return nil, fmt.Errorf("ao salvar tarefa: %w", err)
    }

    s.log.InfoContext(ctx, "tarefa criada", "id", tarefa.ID, "titulo", tarefa.Titulo)
    return tarefa, nil
}

func (s *TarefaService) Concluir(ctx context.Context, id int) error {
    tarefa, err := s.repo.Buscar(ctx, id)
    if err != nil {
        return fmt.Errorf("ao buscar tarefa: %w", err)
    }

    tarefa.Status = domain.StatusConcluida

    if err := s.repo.Atualizar(ctx, tarefa); err != nil {
        return fmt.Errorf("ao atualizar tarefa: %w", err)
    }

    s.log.InfoContext(ctx, "tarefa concluída", "id", id)
    return nil
}

func (s *TarefaService) Listar(ctx context.Context) ([]*domain.Tarefa, error) {
    return s.repo.Listar(ctx)
}
```

## handler/tarefa_handler.go

```go
package handler

import (
    "encoding/json"
    "errors"
    "log/slog"
    "net/http"
    "strconv"
    "strings"

    "github.com/seunome/todo/internal/domain"
    "github.com/seunome/todo/internal/service"
)

type TarefaHandler struct {
    svc *service.TarefaService
    log *slog.Logger
}

func NovoTarefaHandler(svc *service.TarefaService, log *slog.Logger) *TarefaHandler {
    return &TarefaHandler{svc: svc, log: log}
}

// Registra todas as rotas
func (h *TarefaHandler) Registrar(mux *http.ServeMux) {
    mux.HandleFunc("/tarefas", h.handleTarefas)
    mux.HandleFunc("/tarefas/", h.handleTarefa) // com /
}

func (h *TarefaHandler) handleTarefas(w http.ResponseWriter, r *http.Request) {
    switch r.Method {
    case http.MethodGet:
        h.listar(w, r)
    case http.MethodPost:
        h.criar(w, r)
    default:
        http.Error(w, "método não permitido", http.StatusMethodNotAllowed)
    }
}

func (h *TarefaHandler) handleTarefa(w http.ResponseWriter, r *http.Request) {
    // Extrai ID da URL: /tarefas/42
    idStr := strings.TrimPrefix(r.URL.Path, "/tarefas/")
    id, err := strconv.Atoi(idStr)
    if err != nil {
        http.Error(w, "ID inválido", http.StatusBadRequest)
        return
    }

    switch r.Method {
    case http.MethodPut:
        h.concluir(w, r, id)
    case http.MethodDelete:
        h.deletar(w, r, id)
    default:
        http.Error(w, "método não permitido", http.StatusMethodNotAllowed)
    }
}

func (h *TarefaHandler) criar(w http.ResponseWriter, r *http.Request) {
    var input struct {
        Titulo    string `json:"titulo"`
        Descricao string `json:"descricao"`
    }

    if err := json.NewDecoder(r.Body).Decode(&input); err != nil {
        h.erroJSON(w, "corpo inválido", http.StatusBadRequest)
        return
    }

    tarefa, err := h.svc.Criar(r.Context(), input.Titulo, input.Descricao)
    if err != nil {
        if errors.Is(err, domain.ErrTituloVazio) {
            h.erroJSON(w, err.Error(), http.StatusBadRequest)
            return
        }
        h.log.ErrorContext(r.Context(), "erro ao criar tarefa", "erro", err)
        h.erroJSON(w, "erro interno", http.StatusInternalServerError)
        return
    }

    h.respondJSON(w, tarefa, http.StatusCreated)
}

func (h *TarefaHandler) listar(w http.ResponseWriter, r *http.Request) {
    tarefas, err := h.svc.Listar(r.Context())
    if err != nil {
        h.log.ErrorContext(r.Context(), "erro ao listar", "erro", err)
        h.erroJSON(w, "erro interno", http.StatusInternalServerError)
        return
    }
    h.respondJSON(w, tarefas, http.StatusOK)
}

func (h *TarefaHandler) concluir(w http.ResponseWriter, r *http.Request, id int) {
    if err := h.svc.Concluir(r.Context(), id); err != nil {
        if errors.Is(err, domain.ErrNaoEncontrado) {
            h.erroJSON(w, "tarefa não encontrada", http.StatusNotFound)
            return
        }
        h.log.ErrorContext(r.Context(), "erro ao concluir", "erro", err)
        h.erroJSON(w, "erro interno", http.StatusInternalServerError)
        return
    }
    w.WriteHeader(http.StatusNoContent)
}

func (h *TarefaHandler) deletar(w http.ResponseWriter, r *http.Request, id int) {
    // ... similar ao concluir ...
    w.WriteHeader(http.StatusNoContent)
}

func (h *TarefaHandler) respondJSON(w http.ResponseWriter, data interface{}, status int) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    json.NewEncoder(w).Encode(data)
}

func (h *TarefaHandler) erroJSON(w http.ResponseWriter, mensagem string, status int) {
    h.respondJSON(w, map[string]string{"erro": mensagem}, status)
}
```

## cmd/api/main.go

```go
package main

import (
    "context"
    "log/slog"
    "net/http"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/seunome/todo/internal/handler"
    "github.com/seunome/todo/internal/repository"
    "github.com/seunome/todo/internal/service"
)

func main() {
    // Logger estruturado (Go 1.21+)
    log := slog.New(slog.NewJSONHandler(os.Stdout, nil))

    // Wiring: injeção de dependências manual
    repo := repository.NovoInMemoryRepo()
    svc := service.NovoTarefaService(repo, log)
    h := handler.NovoTarefaHandler(svc, log)

    mux := http.NewServeMux()
    h.Registrar(mux)

    // Middleware de logging
    handler := loggingMiddleware(log, mux)

    srv := &http.Server{
        Addr:         ":8080",
        Handler:      handler,
        ReadTimeout:  15 * time.Second,
        WriteTimeout: 15 * time.Second,
    }

    // Inicia servidor
    go func() {
        log.Info("servidor iniciado", "addr", srv.Addr)
        if err := srv.ListenAndServe(); err != http.ErrServerClosed {
            log.Error("erro no servidor", "erro", err)
            os.Exit(1)
        }
    }()

    // Graceful shutdown
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    log.Info("desligando servidor...")
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()

    if err := srv.Shutdown(ctx); err != nil {
        log.Error("erro no shutdown", "erro", err)
    }
    log.Info("servidor parado")
}

func loggingMiddleware(log *slog.Logger, next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        inicio := time.Now()
        next.ServeHTTP(w, r)
        log.Info("requisição",
            "method", r.Method,
            "path", r.URL.Path,
            "duracao", time.Since(inicio),
        )
    })
}
```

***

# Bastidores do Runtime Go

## Goroutines Internamente

Uma goroutine é representada no runtime pela struct `g` (em `runtime/runtime2.go`). Os campos relevantes incluem:

- `stack`: os limites inferior e superior da stack atual
- `stackguard0`: ponteiro para detecção de overflow de stack
- `_panic`: lista de defers ativos
- `sched`: contexto salvo do scheduler (registradores de CPU quando a goroutine é suspensa)
- `atomicstatus`: estado atual (rodando, esperando, morta, etc.)

**Stack growth:** quando uma goroutine está prestes a ultrapassar sua stack atual, o runtime:

1. Aloca uma nova stack maior (geralmente 2x)
2. Copia toda a stack atual para a nova
3. Atualiza todos os ponteiros que apontavam para endereços na stack antiga
4. Retoma a execução na nova stack

Isso é transparente para o programador mas tem um custo. O step 3 (atualizar ponteiros) é o "stack copying" que acontece. Por isso, ponteiros para variáveis de stack são problemáticos entre goroutines - mas o Go evita isso através do escape analysis: se algo pode ser apontado de outra goroutine, vai para o heap.

## Garbage Collector Tri-Color

O GC do Go usa o algoritmo "tri-color concurrent mark-sweep" com a invariante tri-color:

**A invariante:** nenhum objeto preto aponta diretamente para um objeto branco. Isso garante que objetos brancos "atrás" de objetos pretos não sejam coletados prematuramente.

```plain
Algoritmo simplificado:

1. STW curto: inicializa marcação, adiciona raízes à fila cinza

2. Concurrent marking (corre junto com o programa):
   - Pega objeto cinza da fila
   - Marca como preto
   - Adiciona seus filhos brancos como cinza
   - Repete até a fila cinza estar vazia

3. STW curto: finaliza (termina goroutines auxiliares de GC)

4. Concurrent sweeping (corre junto com o programa):
   - Libera objetos brancos (não alcançados)

Objetos alocados durante a marcação são marcados como cinza ou preto
para manter a invariante.
```

O write barrier garante a invariante: quando o programa escreve um ponteiro em um objeto preto para um objeto branco, o write barrier intervém e marca o objeto branco como cinza.

## Scheduler Detalhado

O scheduler do Go é implementado em `runtime/proc.go` e usa um algoritmo chamado "work-stealing":

```plain
Estados de uma goroutine:
_Gidle      → criada mas não inicializada
_Grunnable  → pronta para rodar, na fila de um P
_Grunning   → ativamente rodando num M
_Gsyscall   → bloqueada numa syscall
_Gwaiting   → esperando por algo (channel, timer, etc.)
_Gdead      → terminou, pode ser reutilizada
```

Quando uma goroutine faz uma syscall bloqueante:

1. O M que rodava a goroutine descola do P
2. O P pode ser pego por outro M para continuar executando outras goroutines
3. Quando a syscall retorna, a goroutine tenta pegar um P; se nenhum estiver disponível, vai para a fila global

Isso é por que I/O bound workloads podem usar muito mais goroutines do que CPUs - goroutines esperando I/O não bloqueiam threads do OS.

***

# Quando Go é Excelente

**Servidores e microsserviços HTTP/gRPC:** a combinação de goroutines baratas, GC com baixa latência e binário estático faz de Go a linguagem ideal. O servidor HTTP da stdlib é production-ready. gRPC tem excelente suporte.

**Ferramentas de linha de comando:** `go build` produz um binário estático sem dependências. Docker, Kubernetes, Terraform, GitHub CLI - todos escritos em Go. A razão é exatamente essa: distribua um único arquivo.

**Sistemas com alta concorrência de I/O:** proxies, API gateways, servidores de WebSocket, brokers. Goroutines lidam com 100.000 conexões simultâneas com naturalidade.

**Infraestrutura e DevOps:** a maioria das ferramentas modernas de infraestrutura é Go. O ecossistema é rico.

**Data pipelines e workers:** pipelines de processamento de dados, workers de fila, ETL. O modelo de concorrência é perfeito para isso.

***

# Quando Go NÃO é Ideal

**Machine learning e data science:** Python domina de forma avassaladora. O ecossistema (PyTorch, NumPy, Pandas, Jupyter) não tem equivalente em Go. Não tente lutar contra isso.

**Scripting e automação rápida:** Python, Ruby ou bash são mais práticos para scripts de 50 linhas. Go tem overhead de ter que compilar e a verbosidade não compensa para scripts pequenos.

**Sistemas de tempo real extremamente rígido (hard real-time):** o GC pode introduzir pauses imprevisíveis. Para controle industrial com microsegundos de deadline, você quer C, Rust ou Ada.

**Front-end web:** sim, Go compila para WebAssembly, mas o ecossistema de front-end é JavaScript/TypeScript. GopherJS e WASM existem mas são nicho.

**Domínios altamente matemáticos ou de computação científica:** Fortran, C, Julia e MATLAB têm bibliotecas numéricas maduras. Go não tem.

***

# Tradeoffs Reais

## Verbosidade do Tratamento de Erros

```go
// O famoso padrão if err != nil
resultado, err := passo1()
if err != nil { return err }

dados, err := passo2(resultado)
if err != nil { return err }

saida, err := passo3(dados)
if err != nil { return err }
```

Este código é mais longo do que try/catch. Mas é mais explícito: você sabe exatamente o que pode falhar em cada ponto. Em Java, você pode ter exceções pulando de funções que você nem sabia que podiam lançar. Em Go, o fluxo de erro é visível.

O tradeoff é real: mais código vs mais clareza. A comunidade Go aceita o custo de verbosidade em troca de explicitness.

## Ausência de Generics por Longo Tempo

Por muitos anos, não ter generics forçou padrões repetitivos (copiar/colar código para tipos diferentes) ou uso excessivo de `interface{}` com custos de performance. Go 1.18 (2022) trouxe generics, mas a comunidade ainda está aprendendo onde usá-los bem.

## GC no Lugar de Gerenciamento Manual

Go não tem o controle de memória de C++ ou Rust. Para sistemas onde você precisa de zero alocações de heap ou controle muito fino de tempo de vida de objetos, Go não é a ferramenta. O Rust resolve este problema de forma muito mais completa.

## Falta de Imutabilidade Nativa

Não existe `const` para structs ou slices. Imutabilidade em Go é por convenção e disciplina, não por enforcement do compilador. Isso pode levar a bugs sutis em código concorrente.

## Ecosistema Mais Jovem

Comparado a Java, Python ou JavaScript, o ecossistema Go é menor. Para nichos como ML, finanças quantitativas ou processamento de mídia, você frequentemente encontrará bibliotecas mais maduras em outras linguagens.

***

# Conclusão

Go não é a linguagem "certa" para tudo. Nenhuma linguagem é. Mas para um conjunto específico de problemas - servidores, ferramentas, infraestrutura, sistemas concorrentes - ela é extraordinariamente eficaz.

O que a torna especial não é nenhuma feature individual. É a combinação de simplicidade deliberada + concorrência de primeira classe + compilação rápida + deploy trivial + performance razoável + excelente tooling.

A filosofia de "faça a coisa óbvia, não a esperta" resulta em codebases que são mais fáceis de manter conforme crescem e conforme a equipe muda. Isso tem valor imenso em sistemas de produção.

Aprender Go bem significa internalizar essa mentalidade: valorize a clareza acima da brevidade, a explicitness acima da mágica, a simplicidade acima da sofisticação. É uma disciplina, não apenas uma sintaxe.

O próximo passo depois deste guia é escrever código real. Pegue um projeto pessoal, reescreva uma ferramenta que você usa, construa um servidor para uma API que você consome. A teoria sem prática envelhece rápido.

Go está esperando. O compilador é rápido.

***

_Este guia cobre Go até a versão 1.22. Alguns recursos como generics (1.18), `slog` (1.21) e mudanças de semântica de loop (1.22) são mencionados ao longo do texto._
