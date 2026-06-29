---
title: Como um modelo de linguagem (LLM) funciona
slug: how-llm-works
description: ''
summary: ''
tags:
  - LLMs
  - ia
categories:
  - dev
  - LLMs
keywords: []
author: Gabriel Maggioni
date: 2026-06-29T10:37:00-03:00
lastmod: ''
showToc: true
TocOpen: false
hiddenInHomeList: false
draft: false
---

Tem uma pergunta que eu ouço com uma frequência impressionante de gente que trabalha com tecnologia: "mas como o ChatGPT _sabe_ o que responder?". E a resposta curta, "ele não sabe nada, ele só prevê a próxima palavra", costuma deixar as pessoas mais confusas do que estavam antes. Faz sentido. Se é só previsão de próxima palavra, por que às vezes parece que ele está raciocinando, corrigindo o próprio erro, ou explicando um conceito que você jura que ele "entendeu"?

A resposta completa é mais interessante do que qualquer uma dessas duas frases soltas, e ela passa por estatística, álgebra linear, uma dose generosa de engenharia de sistemas e, no final, por arquivos de alguns gigabytes que você pode baixar e rodar na sua própria máquina sem internet. É essa jornada inteira que eu quero percorrer aqui.

## O problema que ninguém conseguia resolver

Linguagem humana é uma bagunça do ponto de vista de um computador. Não é à toa que, durante décadas, processamento de linguagem natural foi considerado um dos problemas mais difíceis da ciência da computação, ao lado de visão computacional. Um computador lida bem com regras explícitas: se isso, então aquilo. Linguagem não funciona assim. A mesma palavra muda de sentido dependendo do contexto, frases ambíguas são a regra e não a exceção, e o significado de uma sentença muitas vezes depende de coisas que nem estão escritas -conhecimento de mundo, tom, intenção.

As primeiras tentativas sérias de fazer máquinas "entenderem" texto vieram de modelos estatísticos baseados em contagem. O mais simples e didático desses é o modelo de n-gramas: você pega um corpus enorme de texto, conta quantas vezes cada sequência de N palavras aparece, e usa essas frequências para estimar qual palavra provavelmente vem depois de outra. Se "café da" aparece seguido de "manhã" em 80% dos casos no seu corpus, o modelo aposta em "manhã".

Funciona, até certo ponto. O problema é que essa abordagem não escala. Quanto mais palavras de contexto você quer considerar, mais combinações possíveis existem, e a maioria delas nunca vai aparecer nem no corpus mais gigante que você conseguir reunir -isso é o que chamamos de **esparsidade**, a sensação de que seus dados nunca são suficientes porque o espaço de combinações possíveis cresce exponencialmente. Um modelo de bigrama (duas palavras) já é limitado; um de trigrama melhora um pouco mas explode em tamanho; chegar em sequências longas o suficiente para capturar o sentido de uma frase real é simplesmente inviável guardando contagens brutas.

Além disso, contagem de frequência não captura significado. "Rei" e "monarca" são praticamente sinônimos, mas para um modelo de n-gramas são duas entradas completamente diferentes na tabela, sem nenhuma relação entre si. O modelo não tem noção de que essas palavras vivem num espaço semântico parecido. Ele só sabe contar.

Foi essa limitação que empurrou a pesquisa em direção a redes neurais. As primeiras tentativas usavam **RNNs** (redes neurais recorrentes), que processam texto palavra por palavra, mantendo uma espécie de "memória" interna que vai sendo atualizada a cada novo token. Depois vieram as LSTMs, uma variante desenhada para lidar melhor com dependências de longo prazo, porque RNNs simples tendem a "esquecer" informação de muitas palavras atrás -um problema técnico conhecido como desaparecimento do gradiente, que vamos revisitar mais adiante.

O problema das RNNs e LSTMs não era falta de inteligência arquitetural. Era velocidade. Como elas processam a sequência um token por vez, dependendo do resultado do token anterior, é impossível paralelizar esse processamento em GPUs do jeito que se paraleliza, por exemplo, multiplicação de matrizes. E paralelização é exatamente o que permite treinar modelos em escala industrial. Sem ela, treinar algo do tamanho de um GPT moderno levaria anos, não semanas.

## A virada de 2017

Em 2017, um grupo de pesquisadores do Google publicou um artigo chamado "Attention Is All You Need". O título já é uma provocação: a ideia central era que você podia jogar fora toda a complexidade recorrente das RNNs e LSTMs e construir um modelo de linguagem competitivo usando só um mecanismo chamado **atenção** (attention), processando a sequência inteira de uma vez, em paralelo.

Essa arquitetura ficou conhecida como **Transformer**, e ela é literalmente a base de tudo que veio depois -GPT, Claude, Gemini, Llama, todos eles são variações sobre esse mesmo esqueleto.

A ideia de atenção, no fundo, tenta resolver um problema bem humano: quando você lê uma frase, você não dá peso igual para cada palavra na hora de interpretar uma palavra específica. Pega a frase "o gato perseguiu o rato porque ele estava com fome". Quando você lê "ele", seu cérebro automaticamente volta e decide que "ele" se refere ao gato, não ao rato -porque gatos com fome perseguem ratos, e o contrário seria estranho. Você fez isso sem esforço consciente, mas envolveu olhar para palavras específicas da frase e ignorar outras.

O mecanismo de atenção faz algo parecido, de forma matemática. Para cada palavra (tecnicamente, cada **token**, que eu explico já já) da sequência, o modelo calcula o quanto ela deveria "prestar atenção" em cada outra palavra da mesma sequência para entender melhor o próprio significado naquele contexto. Sem entrar em fórmulas, pensa assim: cada token gera três coisas -uma pergunta ("o que eu estou procurando?"), uma resposta ("o que eu tenho a oferecer?") e um conteúdo ("o que eu realmente significo"). O modelo compara a pergunta de cada token com a resposta de todos os outros tokens da frase, decide quais combinam melhor, e usa essa comparação para misturar o conteúdo de forma ponderada. O resultado é uma representação de cada palavra que já "sabe" quais outras palavras da frase são relevantes para ela.

O nome técnico para essas três coisas é _query_, _key_ e _value_ -mas o que importa reter é a ideia, não o jargão. E o pulo do gato é que esse cálculo pode ser feito para todos os tokens da sequência simultaneamente, em paralelo, usando operações de multiplicação de matriz que GPUs adoram fazer. Isso é o que destravou a escala. De repente era possível treinar em datasets gigantescos, em paralelo, em clusters de GPU, sem o gargalo sequencial das arquiteturas anteriores.

Vale notar que o artigo original do Google nasceu para tradução automática, não para gerar texto livre. O "GPT" da OpenAI usa só a metade "decodificadora" dessa arquitetura, otimizada especificamente para uma tarefa: prever o próximo token de uma sequência, repetidamente. É essa escolha de design, aparentemente simples, que sustenta tudo que veio depois.

## Como o modelo aprende

Antes de explicar treinamento, precisamos resolver um detalhe técnico: um modelo de linguagem não lê palavras. Ele lê números. Todo o processo de transformar texto em algo que uma rede neural consegue processar passa por algumas etapas.

A primeira é a **tokenização**. Em vez de quebrar o texto em palavras inteiras, os LLMs modernos quebram em pedaços de subpalavra, chamados **tokens**. "Inacreditável" pode virar três ou quatro tokens diferentes, dependendo do tokenizador. Por que fazer assim em vez de usar palavras completas? Porque isso permite lidar com qualquer palavra, mesmo as que o modelo nunca viu inteiras durante o treino -uma palavra rara ou inventada ainda pode ser decomposta em pedaços conhecidos. O conjunto de todos os tokens possíveis que um modelo reconhece se chama **vocabulário**, e normalmente tem algo entre 30 mil e mais de 100 mil entradas, dependendo do modelo.

Cada token do vocabulário é associado a um **embedding**: um vetor, uma lista de números (algo como algumas centenas ou milhares de dimensões) que representa aquele token num espaço matemático. A ideia por trás dos embeddings é que tokens com significados parecidos acabam, depois do treinamento, com vetores próximos nesse espaço. "Rei" e "monarca" vão ficar geometricamente próximos; "rei" e "geladeira", bem distantes. Essa proximidade não é definida manualmente por ninguém -ela emerge do processo de treino, conforme o modelo ajusta esses vetores para ficar melhor na tarefa que ele está sendo treinado para fazer.

E qual é essa tarefa? No caso dos GPTs, é embaraçosamente simples de descrever: prever o próximo token de uma sequência de texto. Você dá ao modelo um trecho de texto cortado, esconde o que vem depois, e pede para ele adivinhar. Se o texto de treino é "o céu está", o modelo tenta prever "azul" (ou qualquer continuação plausível). Comparado com o que realmente vinha no texto original, calcula-se um erro -chamado **função de perda** (loss function) -que mede o quão errado o modelo estava nessa previsão específica.

É aqui que entra a parte que de fato "ensina" o modelo: **backpropagation**. É um algoritmo que, dado o erro calculado, percorre a rede neural de trás para frente, calculando o quanto cada um dos parâmetros internos do modelo contribuiu para esse erro. Esse "quanto contribuiu" é o **gradiente** -uma direção matemática que indica para qual lado cada parâmetro deveria se mover para reduzir o erro um pouquinho. O processo de efetivamente mover os parâmetros nessa direção, em pequenos passos, se chama gradiente descendente, e é repetido bilhões de vezes ao longo do treinamento.

A escala disso é difícil de internalizar. Modelos modernos são treinados com trilhões de tokens -uma fração consideravelmente grande de tudo que já foi publicado digitalmente em texto, livros, código, artigos, conversas. E cada parâmetro do modelo (esses números internos que vão sendo ajustados) não tem nenhum significado individual interpretável. Não existe um parâmetro "isso aqui controla se o texto é sobre culinária". O conhecimento fica distribuído de forma difusa por bilhões desses números, todos ajustados em conjunto, milhões de vezes, até que o modelo fique consistentemente bom em prever o próximo token em praticamente qualquer tipo de texto que aparece na internet e em livros.

E essa é, no fundo, a única coisa que ele aprende a fazer. Tudo o que parece "inteligência" depois -escrever código, resumir um texto, explicar um conceito de física -é consequência indireta de ficar extremamente bom em uma única tarefa: continuar texto de forma estatisticamente plausível, considerando um contexto gigantesco de exemplos vistos durante o treino.

## O que acontece quando você manda um prompt

Treinamento é uma coisa que acontece uma vez (ou de tempos em tempos, em ciclos de atualização). **Inferência** é o que acontece toda vez que você manda uma mensagem para o modelo e espera uma resposta. É um processo bem diferente, e vale destrinchar com calma porque é exatamente isso que roda na sua máquina quando você usa um modelo local.

Primeiro, seu texto passa pelo mesmo tokenizador usado no treino, virando uma sequência de tokens. Cada token é convertido no embedding correspondente -aquele vetor de números que vimos antes. Até aqui, nenhuma "inteligência" aconteceu ainda; é só conversão de formato.

Essa sequência de vetores entra então na pilha de camadas do Transformer. Modelos modernos têm dezenas de camadas empilhadas (alguns têm mais de cem), e cada camada repete basicamente o mesmo padrão: primeiro um bloco de **self-attention**, que é o mecanismo de atenção que descrevi antes aplicado dentro da própria sequência de entrada, permitindo que cada token "olhe" para os outros tokens do prompt e ajuste sua representação considerando esse contexto. Depois disso, vem um bloco de **MLP** (multilayer perceptron, uma rede neural simples e densa) que processa cada posição individualmente, aplicando uma transformação não-linear que permite ao modelo combinar e refinar a informação que a atenção acabou de juntar.

Esse par -atenção, depois MLP -se repete camada após camada, e a cada passagem a representação de cada token vai ficando mais "rica", incorporando mais contexto e mais abstração. É meio parecido com revisar um rascunho várias vezes: a cada revisão, o texto fica mais refinado, embora a estrutura básica continue a mesma.

No final da última camada, o modelo precisa transformar esse vetor abstrato de volta em algo útil: uma previsão sobre qual deveria ser o próximo token. Isso é feito através de uma camada final que produz os **logits** -uma pontuação numérica para cada token possível do vocabulário, indicando o quão favorável o modelo considera aquele token como continuação. Se o vocabulário tem 100 mil tokens, você tem 100 mil números de saída, um para cada candidato.

Esses logits, por si só, não são probabilidades -podem ser negativos, positivos, de qualquer magnitude. Por isso passam por uma função chamada **softmax**, que converte esse conjunto de números numa distribuição de probabilidade válida: tudo positivo, somando exatamente 1. Agora sim o modelo tem, efetivamente, algo como "32% de chance de o próximo token ser 'azul', 12% de chance de ser 'cinzento', 0,01% de chance de ser 'banana'", e assim por diante para os 100 mil candidatos.

A partir dessa distribuição, é preciso escolher um token. Existem estratégias diferentes para isso. A mais simples é greedy decoding: sempre escolher o token de maior probabilidade. Funciona, mas tende a gerar texto repetitivo e previsível. Por isso a maioria das implementações usa técnicas de amostragem controlada, com parâmetros como _temperatura_ (que achata ou acentua a distribuição, deixando a escolha mais ou menos aleatória), _top-k_ (considerar só os k tokens mais prováveis) ou _top-p_ (considerar só tokens suficientes para somar uma certa probabilidade acumulada). É por isso que o mesmo prompt pode gerar respostas levemente diferentes em execuções distintas.

Escolhido o token, ele é adicionado ao final da sequência, e todo o processo se repete -tokenizar (nesse caso, já está tokenizado), passar pelas camadas, gerar logits, aplicar softmax, escolher o próximo. De novo. E de novo. Centenas ou milhares de vezes, um token por vez, até que o modelo gere um token especial de parada ou atinja o limite configurado. É exatamente por isso que você vê o texto "aparecendo" palavra por palavra quando usa um chatbot: cada pedacinho que aparece na tela é, literalmente, o resultado de uma passagem inteira por todas as camadas da rede.

## Por que parece que ele está pensando

Esse processo todo que acabei de descrever não tem, em nenhum momento, uma consulta a um banco de dados, nenhuma busca na internet, nenhuma recuperação de "fato" armazenado em algum lugar identificável. Tudo o que o modelo faz é matemática sobre os pesos que foram ajustados durante o treinamento. Quando você pergunta a capital da Mongólia e ele responde "Ulan Bator", não houve uma consulta tipo SQL buscando "capital + Mongólia" em alguma tabela. Houve uma sequência de multiplicações de matriz cujo resultado, depois de bilhões de ajustes durante o treino, converge consistentemente para o token "Ulan" seguido de "Bator" nesse contexto.

Isso é difícil de aceitar intuitivamente porque o resultado final parece comportamento de busca de informação. Mas é importante separar duas coisas que ficam fáceis de confundir: memorização e generalização.

Memorização é quando o modelo, durante o treino, viu um trecho específico tantas vezes (ou um trecho raro o suficiente para ficar "gravado" nos pesos) que ele consegue reproduzi-lo quase literalmente. Isso acontece de fato -é um dos motivos pelos quais pesquisadores se preocupam com modelos "vazando" trechos de textos protegidos por direitos autorais que apareceram no treino.

Generalização é algo bem mais interessante e é o que realmente sustenta a utilidade desses modelos. É a capacidade de produzir uma resposta coerente para uma combinação de palavras que nunca apareceu, literalmente, em nenhum lugar do conjunto de treino. Se você pede para o modelo escrever um poema sobre um robô triste que trabalha numa padaria em Marte, é extremamente improvável que esse texto exato exista em algum lugar da internet. O modelo está combinando padrões aprendidos sobre estrutura de poemas, sobre robôs, sobre tristeza, sobre padarias, sobre Marte -tudo aprendido separadamente em contextos diferentes -e produzindo algo novo que respeita esses padrões simultaneamente.

Essa capacidade de generalizar bem é, ainda hoje, motivo de debate sério entre pesquisadores. Alguns argumentam que isso é evidência de algo parecido com raciocínio abstrato emergindo da escala; outros são mais céticos e descrevem o fenômeno como interpolação estatística extremamente sofisticada num espaço de altíssima dimensão, sem nenhuma necessidade de invocar "entendimento" no sentido que usamos para humanos. Eu não vou fingir que essa discussão está resolvida, porque não está. O que dá para afirmar com segurança é que não existe, dentro da arquitetura, nenhum módulo de raciocínio simbólico explícito, nenhuma base de conhecimento consultável, nenhuma memória de longo prazo entre conversas diferentes. Existe só uma função matemática gigantesca, fixa depois do treino, que mapeia sequências de tokens de entrada em distribuições de probabilidade de saída.

## O arquivo GGUF: o modelo dentro de uma mochila

Tudo que descrevi até aqui é arquitetura e processo. Agora vamos para a parte prática: como esse modelo treinado vira um arquivo que você baixa e roda offline no seu notebook.

Quando um modelo é treinado, o resultado bruto costuma viver em formatos pensados para frameworks de pesquisa, como PyTorch, geralmente espalhados em vários arquivos com bibliotecas pesadas como dependência. Isso é ótimo para quem está treinando ou ajustando modelos em laboratório, mas é péssimo para quem só quer rodar um modelo já pronto de forma simples, rápida e portátil, especialmente em CPU.

Foi para resolver esse problema que Georgi Gerganov criou o **GGUF** (uma evolução do formato anterior, GGML), como parte do projeto llama.cpp -uma implementação de inferência de LLMs em C/C++ pensada para rodar de forma eficiente sem depender de GPUs caras nem de pilhas de software complexas.

Um arquivo GGUF empacota, num único arquivo binário, tudo que é necessário para rodar o modelo: os **pesos** (os parâmetros numéricos aprendidos durante o treino -os mesmos que vimos sendo ajustados pelo gradiente), os **metadados** (informações como quantos parâmetros o modelo tem, quantas camadas, quantas cabeças de atenção, qual o tamanho do contexto suportado), informações da **arquitetura** (qual variante de Transformer está sendo usada, já que existem diferenças sutis entre famílias de modelos), o **tokenizer** completo (o vocabulário e as regras de como quebrar texto em tokens) e, frequentemente, os pesos já em alguma forma de **quantização**, que é o assunto da próxima seção.

Esse design de "tudo em um arquivo só" não é estético, é funcional: permite que ferramentas como llama.cpp façam **memory-mapping** do arquivo direto do disco -vamos falar disso com mais detalhe depois -sem precisar de um monte de arquivos auxiliares espalhados.

E por que esses arquivos costumam ter dezenas de gigabytes? Porque cada parâmetro do modelo é um número, e números ocupam espaço. Um modelo com 7 bilhões de parâmetros, guardado em precisão completa de 16 bits (2 bytes por número), já fica em torno de 14 GB só de pesos. Modelos com 70 bilhões de parâmetros, na mesma precisão, passam de 140 GB. A conta é direta: número de parâmetros multiplicado pelo número de bytes usados para representar cada um.

## Quantização: comprimindo o modelo sem perder a alma

Se cada parâmetro em precisão alta ocupa 2 ou 4 bytes, e um modelo tem dezenas de bilhões de parâmetros, fica claro que rodar isso num notebook comum seria inviável sem alguma forma de compressão. É aí que entra a quantização.

A ideia central é reduzir a precisão numérica usada para representar cada peso, trocando alguma exatidão matemática por uma economia drástica de memória e ganho de velocidade. Os modelos costumam ser treinados em **FP32** (32 bits, ponto flutuante, a precisão "cheia" tradicional de computação científica) ou, mais comumente hoje, em **FP16** ou variantes como BF16 (16 bits), que já é uma forma de economia adotada desde o treinamento, sem perda perceptível de qualidade para a maioria dos casos.

A partir daí, formatos de quantização mais agressivos entram em cena, geralmente identificados por nomes como **Q8**, **Q6**, **Q5**, **Q4** -o número indicando, grosso modo, quantos bits são usados para representar cada peso. Q8 usa 8 bits por parâmetro, reduzindo o tamanho à metade de um FP16, com perda de qualidade geralmente imperceptível no uso prático. Q4 usa só 4 bits, reduzindo o tamanho para um quarto, mas já introduzindo um erro de aproximação mais perceptível, especialmente em tarefas que exigem precisão fina, como matemática ou código complexo.

O detalhe interessante é que essas técnicas não são apenas "arredondar o número para menos casas decimais" de forma ingênua. Os formatos modernos -as variantes "k-quants" e, mais recentemente, os formatos **IQ** (i-quants) -usam estratégias mais sofisticadas, como agrupar pesos em pequenos blocos com um fator de escala compartilhado, ou usar uma matriz de importância calculada a partir de dados reais para decidir quais pesos merecem mais precisão e quais toleram mais aproximação. O resultado é que um Q4 bem feito hoje perde muito menos qualidade do que um Q4 ingênuo perderia há alguns anos.

O efeito prático de tudo isso é direto: quanto mais agressiva a quantização, menor o arquivo, menos RAM ou VRAM necessária, e mais rápida a inferência (porque mover menos bytes entre memória e processador, em modelos desse tamanho, costuma ser o fator que mais limita a velocidade -mais sobre isso já já). Em troca, você paga com qualidade: respostas um pouco menos precisas, mais chance de erro em tarefas que exigem raciocínio fino. Para a maioria dos usos do dia a dia, um Q4 ou Q5 de um modelo bom é praticamente indistinguível da versão FP16 original. Para tarefas de alta exigência, vale considerar formatos menos agressivos.

É exatamente essa técnica que torna possível rodar modelos de bilhões de parâmetros em hardware de consumidor. Sem quantização, a entrada na conversa sobre "rodar LLM em casa" praticamente não existiria fora de quem tem uma GPU profissional de dezenas de gigabytes de VRAM.

## O que acontece quando você roda um modelo no seu próprio computador

Juntando tudo isso, dá para descrever com bastante precisão o que acontece quando você abre uma ferramenta como llama.cpp, Ollama ou LM Studio e carrega um arquivo GGUF.

A primeira etapa é o carregamento do arquivo. Em vez de ler o arquivo inteiro para a RAM de uma vez (o que seria lento e desperdiçaria memória se você não fosse usar tudo de imediato), a maioria das implementações usa **memory-mapping** (mmap): o sistema operacional cria um mapeamento entre o arquivo no disco e um espaço de endereços de memória, e os dados só são efetivamente carregados na RAM conforme são acessados. Isso acelera bastante a inicialização e permite que o sistema operacional gerencie de forma inteligente o que fica em cache de memória e o que pode ser descartado se precisar de espaço.

Em seguida, a ferramenta decide como distribuir as camadas do modelo entre os recursos de hardware disponíveis. Se você tem uma GPU com VRAM suficiente, idealmente todas as camadas (ou o máximo possível) são carregadas ali, porque GPUs são ordens de magnitude mais rápidas que CPUs para esse tipo de operação. Quando a VRAM não é suficiente para o modelo inteiro, entra a estratégia de **offloading**: algumas camadas ficam na GPU, outras ficam na RAM e são processadas pela CPU. É um compromisso -você consegue rodar modelos maiores do que sua VRAM permitiria sozinha, mas paga o preço de velocidade nas camadas que sobraram para a CPU, e ainda paga o custo de transferir dados entre RAM e VRAM a cada passagem.

Durante a geração, entra em cena um componente crucial para a viabilidade prática do processo: o **KV cache** (cache de chaves e valores). Lembra que cada token, durante a self-attention, gera uma "pergunta", uma "resposta" e um "conteúdo"? Sem cache, a cada novo token gerado, o modelo precisaria recalcular essas representações para todos os tokens anteriores da conversa, do zero, a cada passo. Isso seria absurdamente redundante e lento. Em vez disso, o modelo guarda essas chaves e valores já calculados de tokens anteriores numa estrutura de cache, e só precisa calcular as informações referentes ao token mais novo a cada passo. Esse cache cresce conforme a conversa fica mais longa, e é um dos principais consumidores de memória durante a geração -é parte do motivo pelo qual conversas muito longas consomem cada vez mais recursos.

O **batch size** define quantas sequências (ou, em alguns contextos, quantos tokens) são processados simultaneamente numa mesma passada pelo modelo. No uso típico de chat com uma pessoa só conversando, o batch durante a geração token a token costuma ser pequeno, mas durante o processamento inicial do prompt (a fase chamada de _prefill_, antes de começar a gerar a resposta), é possível processar todos os tokens do prompt em paralelo de uma vez, o que é bem mais eficiente do que gerar token por token.

E o **context window** é o limite de quantos tokens o modelo consegue considerar simultaneamente -incluindo todo o histórico da conversa e a resposta sendo gerada. Esse limite não é arbitrário; ele é definido pela arquitetura usada no treino e tem um custo direto em memória, já que o KV cache cresce proporcionalmente ao tamanho do contexto sendo mantido.

## Onde cada peça de hardware entra nessa história

Vale separar exatamente o papel de cada componente, porque é comum ver gente comprando hardware errado para o objetivo que tem.

A **GPU** é, de forma simplificada, um processador com milhares de núcleos pequenos otimizados para fazer muitas operações matemáticas simples em paralelo -exatamente o tipo de operação que domina a inferência de Transformers, que é majoritariamente multiplicação de matrizes. É por isso que GPUs aceleram tanto esse processo comparado a CPUs, que têm poucos núcleos, mas cada um muito mais versátil e poderoso individualmente para tarefas sequenciais.

A **VRAM**, a memória dedicada da GPU, é provavelmente o recurso mais crítico e mais escasso nessa equação. É nela que os pesos do modelo (ou parte deles, no caso de offloading) e o KV cache precisam estar para que a GPU os processe rapidamente. VRAM costuma ter uma largura de banda de memória -a velocidade com que dados podem ser lidos e escritos -muito maior que a RAM tradicional do sistema, e essa diferença de **largura de banda** é, na prática, o fator que mais determina a velocidade de geração de tokens, ainda mais que o poder de processamento bruto da GPU. Isso acontece porque, durante a inferência, o "gargalo" não costuma ser fazer a conta, mas sim mover os pesos gigantescos de memória para o processador, repetidamente, a cada token gerado.

A **RAM** do sistema entra em cena quando a VRAM não é suficiente, segurando as camadas que sobraram via offloading, ou quando você está rodando inteiramente em CPU. Tem largura de banda significativamente menor que VRAM moderna, o que explica por que rodar modelos grandes inteiramente em CPU, mesmo em processadores potentes, tende a ser muito mais lento que rodar em GPU.

A **CPU** continua relevante mesmo em setups com GPU: ela coordena o processo, executa o tokenizador, gerencia o que vai para onde, e processa qualquer camada que tenha sido deixada de fora da VRAM.

O **SSD** entra em dois momentos: no carregamento inicial do arquivo GGUF (onde a velocidade de leitura do disco influencia diretamente quanto tempo você espera antes do modelo ficar pronto para uso) e, em situações onde a RAM disponível é insuficiente para o que está sendo mapeado, o sistema operacional pode precisar buscar páginas de memória do disco repetidamente -um cenário bem mais lento e que costuma ser a explicação real por trás de relatos de "modelo travando" em máquinas com pouca memória.

Juntando tudo: um modelo pode ser rápido numa máquina e lentíssimo em outra com a mesma GPU "no papel", simplesmente por diferenças de largura de banda de memória, quantidade de VRAM disponível para caber o modelo inteiro sem offloading, ou velocidade do SSD na hora de carregar.

## Tamanho importa? Depende do que você está medindo

É tentador assumir que mais parâmetros é sempre melhor, mas a relação é mais sutil do que isso.

Parâmetros são, essencialmente, a capacidade do modelo de armazenar padrões aprendidos durante o treino. Mais parâmetros, em teoria, significa mais espaço para capturar nuances, relações complexas, conhecimento de domínios variados, raciocínio em múltiplas etapas. E, de fato, historicamente, modelos maiores treinados de forma adequada tendem a performar melhor numa gama ampla de tarefas -isso é bem documentado em estudos sobre as chamadas _leis de escala_.

Mas "treinados de forma adequada" é a parte que faz toda a diferença. Um modelo gigante treinado com poucos dados, ou com dados de baixa qualidade, pode performar pior que um modelo bem menor, mas treinado com mais cuidado e mais dados proporcionalmente ao seu tamanho. Existe uma relação ideal entre tamanho do modelo e quantidade de dados de treino, e descolar dessa relação -modelo grande demais para os dados disponíveis, ou modelo pequeno tentando absorver dados demais -gera resultados subótimos dos dois lados.

Tamanho também importa de formas diferentes dependendo da tarefa. Para tarefas que exigem conhecimento de mundo amplo, raciocínio em múltiplas etapas, ou lidar com ambiguidade sutil de linguagem, modelos maiores costumam ter vantagem real. Para tarefas estreitas e bem definidas -classificar sentimento de um texto, extrair entidades de um formato específico, responder dentro de um domínio restrito -um modelo pequeno, mas especificamente ajustado (fine-tuned) para aquela tarefa, frequentemente supera um modelo gigante genérico, com uma fração do custo computacional e da latência.

E existe ainda outra variável que não é tamanho de jeito nenhum: a qualidade da curadoria dos dados de treino, a forma como o modelo foi ajustado depois do pré-treinamento (técnicas de alinhamento, ajuste fino por instrução, aprendizado por reforço com feedback humano) frequentemente importam mais para a qualidade percebida no uso do dia a dia do que simplesmente o número de parâmetros. É por isso que comparar modelos só pelo número de bilhões de parâmetros no nome é, na melhor das hipóteses, uma aproximação grosseira.

## Alguns mitos que vale desfazer

**"O modelo pesquisa no Google quando responde."** Não, pelo menos não por padrão. Um modelo de linguagem puro, rodando localmente a partir de um GGUF, não tem absolutamente nenhuma conexão com a internet durante a geração de texto. Tudo que ele "sabe" está codificado nos pesos ajustados durante o treino. Ferramentas como o ChatGPT podem ter uma funcionalidade adicional de busca na web, mas isso é um sistema separado, construído em volta do modelo, que injeta resultados de busca no contexto antes de pedir para o modelo gerar uma resposta. O modelo em si continua só prevendo o próximo token.

**"Ele guarda todas as conversas que já teve."** O modelo, como artefato matemático, é fixo depois do treinamento -os pesos não mudam durante o uso normal. Qualquer "memória" de conversas anteriores que você perceba em algum produto vem de um sistema externo guardando e reinjetando contexto na conversa atual, não de o modelo estar literalmente aprendendo ou se atualizando com seus dados em tempo real. Rodando localmente, isso é ainda mais evidente: a menos que você mesmo implemente algum sistema de memória externo, cada conversa começa do zero.

**"Ele pensa igual um humano."** Como vimos, o processo é geração estatística de tokens baseada em padrões aprendidos, sem nenhum módulo de consciência, intenção ou experiência subjetiva conhecido na arquitetura. Isso não significa que o comportamento resultante seja simples -é, sem dúvida, surpreendentemente sofisticado -mas equiparar o mecanismo a cognição humana é, na melhor das hipóteses, uma simplificação enganosa, e segue sendo tema de debate genuíno entre pesquisadores sérios.

**"Quanto mais parâmetros, melhor, em qualquer situação."** Como vimos na seção anterior, depende de dados de treino, qualidade do ajuste, e da tarefa específica que você está tentando resolver.

**"O arquivo GGUF é o próprio modelo."** Tecnicamente, é uma representação serializada e frequentemente quantizada dos pesos e metadados do modelo -não necessariamente idêntica, bit a bit, à versão original de treino. Um mesmo modelo pode existir em múltiplos arquivos GGUF diferentes, cada um com um nível de quantização distinto, gerando comportamentos sutilmente diferentes apesar de "serem o mesmo modelo" na origem.

## Estamos no começo

Se você chegou até aqui, já tem uma visão bem mais completa do que a maioria das pessoas que usa esses modelos todos os dias -do mecanismo de atenção que tornou tudo isso possível, passando pelo treinamento estatístico bruto, até o arquivo quantizado que cabe no seu SSD e processa tokens usando a VRAM da sua placa de vídeo.

E vale lembrar: tudo isso, da publicação do paper original até os modelos que rodam offline em laptops, aconteceu num intervalo de tempo ridiculamente curto para os padrões históricos de qualquer outra tecnologia. As arquiteturas continuam mudando, as técnicas de quantização continuam melhorando, e a fronteira entre "o que só roda em datacenter" e "o que roda no seu computador pessoal" continua se movendo, mês a mês.

Se esse assunto te interessou, a melhor forma de internalizar de verdade é parar de ler e começar a brincar. Baixa um GGUF pequeno, sobe ele no llama.cpp ou no Ollama, experimenta diferentes níveis de quantização no mesmo modelo e sente na pele a diferença de velocidade e qualidade. Teoria explica o mecanismo; rodar na prática é o que faz o conceito de fato fazer sentido.
