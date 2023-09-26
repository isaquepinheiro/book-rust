## Validando Referências com Lifetimes

Lifetimes (tempo de vida) são outro tipo de genérico que já estamos usando. Em vez de garantir que um tipo tenha o comportamento que desejamos, as lifetimes garantem que as referências sejam válidas pelo tempo que precisamos delas.

Um detalhe que não discutimos na seção ["Referências e
Empréstimos"][references-and-borrowing]<!-- ignore --> no Capítulo 4 é
que cada referência no Rust possui uma *lifetime* (tempo de vida), que é o escopo durante o qual
essa referência é válida. Na maioria das vezes, as lifetimes são implícitas e inferidas,
assim como a maioria das vezes, os tipos são inferidos. Só precisamos anotar tipos
quando múltiplos tipos são possíveis. De maneira semelhante, devemos anotar as lifetimes
quando as lifetimes das referências podem estar relacionadas de algumas maneiras diferentes. O Rust
nos exige que anotemos essas relações usando parâmetros de lifetime genéricos para
garantir que as referências reais usadas em tempo de execução sejam definitivamente válidas.

Anotar lifetimes não é um conceito que a maioria das outras linguagens de programação possui,
portanto, isso pode parecer desconhecido. Embora não cubramos lifetimes em sua totalidade neste capítulo,
discutiremos maneiras comuns pelas quais você pode encontrar a sintaxe de lifetime para que você possa se familiarizar com o conceito.

### Prevenindo Referências Suspensas com Lifetimes

O principal objetivo das lifetimes é prevenir *referências suspensas*, que causam um
programa a fazer referência a dados diferentes dos dados que se destina a referenciar.
Considere o programa no Exemplo 10-16, que possui um escopo externo e um escopo interno.

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-16/src/main.rs}}
```

<span class="caption">Exemplo 10-16: Uma tentativa de usar uma referência cujo valor
saiu do escopo</span>

> Nota: Os exemplos nos Exemplos 10-16, 10-17 e 10-23 declaram variáveis
> sem atribuir-lhes um valor inicial, então o nome da variável existe no
> escopo externo. À primeira vista, isso pode parecer estar em conflito com a ausência de valores nulos no Rust. No entanto, se tentarmos usar uma variável antes de atribuir um valor a ela, receberemos um erro de compilação, o que demonstra que o Rust de fato não permite valores nulos.

O escopo externo declara uma variável chamada `r` sem valor inicial, e o
escopo interno declara uma variável chamada `x` com o valor inicial de 5. Dentro
do escopo interno, tentamos definir o valor de `r` como uma referência para `x`. Em seguida,
o escopo interno termina, e tentamos imprimir o valor em `r`. Esse código não
compilará porque aquilo a que o valor `r` se refere saiu de escopo antes de tentarmos usá-lo. Aqui está a mensagem de erro:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-16/output.txt}}
```

A variável `x` não "permanece viva tempo suficiente". A razão para isso é que `x` estará fora
do escopo quando o escopo interno terminar na linha 7. Mas `r` ainda é válido para o
escopo externo; porque seu escopo é mais amplo, dizemos que ele "permanece vivo por mais tempo". Se
Rust permitisse que este código funcionasse, `r` estaria fazendo referência à memória que foi
desalocada quando `x` saiu de escopo, e qualquer coisa que tentássemos fazer com `r`
não funcionaria corretamente. Então, como o Rust determina que este código é inválido?
Ele utiliza um verificador de empréstimo (borrow checker).

### O Verificador de Empréstimo

O compilador Rust possui um *verificador de empréstimo* que compara os escopos para determinar
se todos os empréstimos são válidos. O Exemplo 10-17 mostra o mesmo código do Exemplo
10-16, mas com anotações mostrando as lifetimes das variáveis.

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-17/src/main.rs}}
```

<span class="caption">Exemplo 10-17: Anotações das lifetimes de `r` e
`x`, nomeadas `'a` e `'b`, respectivamente</span>

Aqui, anotamos a lifetime de `r` com `'a` e a lifetime de `x`
com `'b`. Como você pode ver, o bloco interno `'b` é muito menor do que o bloco de lifetime externo `'a`. Em tempo de compilação, o Rust compara o tamanho das duas lifetimes e percebe que `r` possui uma lifetime de `'a`, mas que se refere à memória com uma lifetime de `'b`. O programa é rejeitado porque `'b` é mais curto do que `'a`: o objeto da referência não vive tanto tempo quanto a referência.

O Exemplo 10-18 corrige o código para que não haja uma referência suspensa e
compila sem erros.

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-18/src/main.rs}}
```

<span class="caption">Exemplo 10-18: Uma referência válida porque os dados têm uma
lifetime mais longa do que a referência</span>

Aqui, `x` tem a lifetime `'b`, que neste caso é maior do que `'a`. Isso significa que `r` pode fazer referência a `x`, porque o Rust sabe que a referência em `r` será sempre válida enquanto `x` for válida.

Agora que você sabe onde estão as lifetimes das referências e como o Rust analisa as lifetimes para garantir que as referências serão sempre válidas, vamos explorar as lifetimes genéricas de parâmetros e valores de retorno no contexto de funções.

### Lifetimes Genéricas em Funções

Vamos escrever uma função que retorna a string mais longa entre duas fatias de string. Esta
função receberá duas fatias de string e retornará uma única fatia de string. Depois
de implementarmos a função `longest`, o código no Exemplo 10-19 deve
imprimir `A string mais longa é abcd`.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-19/src/main.rs}}
```

<span class="caption">Exemplo 10-19: Uma função `main` que chama a função `longest`
para encontrar a fatia de string mais longa entre duas</span>

Observe que queremos que a função receba fatias de string, que são referências,
em vez de strings, porque não queremos que a função `longest` tome
posse de seus parâmetros. Consulte a seção ["Fatias de String como
Parâmetros"][string-slices-as-parameters]<!-- ignore --> no Capítulo 4
para mais discussão sobre por que os parâmetros que usamos no Exemplo 10-19 são os que queremos.

Se tentarmos implementar a função `longest` conforme mostrado no Exemplo 10-20,
ela não será compilada.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-20/src/main.rs:here}}
```

<span class="caption">Exemplo 10-20: Uma implementação da função `longest`
que retorna a fatia de string mais longa entre duas, mas ainda não compila</span>

Em vez disso, recebemos o seguinte erro que menciona as lifetimes:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-20/output.txt}}
```

O texto de ajuda revela que o tipo de retorno precisa de um parâmetro de lifetime genérico porque o Rust não consegue determinar se a referência retornada se refere a `x` ou `y`. Na verdade, nós também não sabemos, porque o bloco `if` no corpo desta função retorna uma referência para `x` e o bloco `else` retorna uma referência para `y`!

Quando estamos definindo essa função, não sabemos os valores concretos que serão passados para esta função, então não sabemos se o caso `if` ou o caso `else` será executado. Também não sabemos as lifetimes concretas das referências que serão passadas, então não podemos olhar para os escopos como fizemos nos Exemplos 10-17 e 10-18 para determinar se a referência que retornamos sempre será válida. O verificador de empréstimo também não pode determinar isso, porque ele não sabe como as lifetimes de `x` e `y` se relacionam com a lifetime do valor de retorno. Para corrigir esse erro, adicionaremos parâmetros de lifetime genéricos que definem a relação entre as referências para que o verificador de empréstimo possa realizar sua análise.

### Sintaxe de Anotação de Lifetime

As anotações de lifetime não alteram a duração das referências. Em vez disso, elas descrevem as relações entre as lifetimes de várias referências entre si sem afetar as lifetimes. Assim como as funções podem aceitar qualquer tipo quando a assinatura especifica um parâmetro de tipo genérico, as funções podem aceitar referências com qualquer lifetime, especificando um parâmetro de lifetime genérico.

As anotações de lifetime têm uma sintaxe um pouco incomum: os nomes dos parâmetros de lifetime devem começar com um apóstrofo (`'`) e geralmente são todos em minúsculas e muito curtos, assim como os tipos genéricos. A maioria das pessoas usa o nome `'a` para a primeira anotação de lifetime. Colocamos as anotações de parâmetro de lifetime após o `&` de uma referência, usando um espaço para separar a anotação do tipo da referência.

Aqui estão alguns exemplos: uma referência a um `i32` sem um parâmetro de lifetime, uma referência a um `i32` que tem um parâmetro de lifetime chamado `'a`, e uma referência mutável a um `i32` que também tem a lifetime `'a`.

```rust,ignore
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

Uma anotação de lifetime por si só não tem muito significado, porque as anotações têm o propósito de informar ao Rust como os parâmetros de lifetime genéricos de múltiplas referências se relacionam entre si. Vamos examinar como as anotações de lifetime se relacionam entre si no contexto da função `longest`.

### Anotações de Lifetime em Assinaturas de Funções

Para usar anotações de lifetime em assinaturas de funções, precisamos declarar os parâmetros de *lifetime* genéricos dentro de colchetes angulares entre o nome da função e a lista de parâmetros, da mesma forma que fizemos com os parâmetros de *tipo* genéricos.

Queremos que a assinatura expresse a seguinte restrição: a referência retornada será válida enquanto ambos os parâmetros forem válidos. Essa é a relação entre as lifetimes dos parâmetros e o valor de retorno. Vamos nomear a lifetime como `'a` e então adicioná-la a cada referência, como mostrado no Exemplo 10-21.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-21/src/main.rs:here}}
```

<span class="caption">Exemplo 10-21: A definição da função `longest`
especificando que todas as referências na assinatura devem ter a mesma lifetime `'a`</span>

Este código deve compilar e produzir o resultado desejado quando o usamos com a
função `main` no Exemplo 10-19.

A assinatura da função agora diz ao Rust que, para alguma lifetime `'a`, a função
recebe dois parâmetros, ambos fatias de string que vivem pelo menos tanto quanto
a lifetime `'a`. A assinatura da função também diz ao Rust que a fatia de string
retornada pela função viverá pelo menos tanto quanto a lifetime `'a`.
Na prática, isso significa que a lifetime da referência retornada pela
função `longest` é a mesma que a menor das lifetimes dos valores
referenciados pelos argumentos da função. Essas relações são o que queremos
que o Rust utilize ao analisar este código.

Lembre-se de que, ao especificarmos os parâmetros de lifetime nesta assinatura de função,
não estamos alterando as lifetimes de nenhum dos valores passados ou retornados. Em vez disso,
estamos especificando que o verificador de empréstimo deve rejeitar quaisquer valores que não
estejam em conformidade com essas restrições. Observe que a função `longest` não precisa
saber exatamente quanto tempo `x` e `y` viverão, apenas que algum escopo pode ser
substituído por `'a` que satisfará esta assinatura.

Ao anotar lifetimes em funções, as anotações vão na assinatura da função, não no corpo da função. As anotações de lifetime fazem parte do contrato da função, assim como os tipos na assinatura. Ter as assinaturas de função conterem o contrato de lifetime significa que a análise feita pelo compilador Rust pode ser mais simples. Se houver um problema com a forma como uma função está anotada ou como ela é chamada, os erros do compilador podem apontar para a parte de nosso código e as restrições com mais precisão. Se, em vez disso, o compilador Rust fizesse mais inferências sobre o que pretendíamos que fossem as relações das lifetimes, o compilador só poderia apontar para o uso de nosso código muitas etapas distante da causa do problema.

Quando passamos referências concretas para `longest`, a lifetime concreta que é
substituída por `'a` é a parte do escopo de `x` que se sobrepõe com o escopo de `y`. Em outras palavras, a lifetime genérica `'a` receberá a lifetime concreta que é igual à menor das lifetimes de `x` e `y`. Como anotamos a referência retornada com o mesmo parâmetro de lifetime `'a`, a referência retornada também será válida pelo tempo da menor das lifetimes de `x` e `y`.

Vamos ver como as anotações de lifetime restringem a função `longest` ao
passar referências com lifetimes concretas diferentes. O Exemplo 10-22 é
um exemplo direto.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-22/src/main.rs:here}}
```

<span class="caption">Exemplo 10-22: Usando a função `longest` com
referências a valores `String` que têm lifetimes concretas diferentes</span>

Neste exemplo, `string1` é válida até o final do escopo externo, `string2`
é válida até o final do escopo interno e `result` faz referência a algo
que é válido até o final do escopo interno. Execute este código e você verá
que o verificador de empréstimo aprova; ele compilará e imprimirá `A string mais longa
é long string is long`.

A seguir, vamos tentar um exemplo que mostra que a lifetime da referência em
`result` deve ser a menor das duas lifetimes dos argumentos. Vamos mover a
declaração da variável `result` para fora do escopo interno, mas deixar a
atribuição do valor à variável `result` dentro do escopo com
`string2`. Em seguida, moveremos o `println!` que usa `result` para fora do
escopo interno, após o término do escopo interno. O código no Exemplo 10-23 não
compilará.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-23/src/main.rs:here}}
```

<span class="caption">Exemplo 10-23: Tentando usar `result` após `string2`
ter saído de escopo</span>

Quando tentamos compilar este código, obtemos o seguinte erro:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-23/output.txt}}
```

O erro mostra que, para `result` ser válido para a instrução `println!`,
`string2` precisaria ser válido até o final do escopo externo. O Rust sabe disso porque anotamos as lifetimes dos parâmetros da função e dos valores de retorno usando o mesmo parâmetro de lifetime `'a`.

Nós, como humanos, podemos olhar para este código e ver que `string1` é mais longa do que `string2` e, portanto, `result` conterá uma referência a `string1`. Como `string1` ainda não saiu de escopo, uma referência a `string1` ainda será válida para a instrução `println!`. No entanto, o compilador não consegue ver que a referência é válida neste caso. Nós informamos ao Rust que a lifetime da referência retornada pela função `longest` é a mesma que a menor das lifetimes das referências passadas. Portanto, o verificador de empréstimo proíbe o código no Exemplo 10-23 como possivelmente tendo uma referência inválida.

Tente projetar mais experimentos que variem os valores e as lifetimes das referências passadas para a função `longest` e como a referência retornada é usada. Faça hipóteses sobre se seus experimentos passarão ou não pelo verificador de empréstimo antes de compilar; depois verifique se você está correto!

### Pensando em Termos de Lifetimes

A forma como você precisa especificar os parâmetros de lifetime depende do que sua
função está fazendo. Por exemplo, se alterássemos a implementação da função
`longest` para sempre retornar o primeiro parâmetro em vez da fatia de string mais longa, não precisaríamos especificar uma lifetime no parâmetro `y`. O código a seguir será compilado:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-08-only-one-reference-with-lifetime/src/main.rs:here}}
```

Especificamos um parâmetro de lifetime `'a` para o parâmetro `x` e o tipo de retorno, mas não para o parâmetro `y`, porque a lifetime de `y` não tem qualquer relação com a lifetime de `x` ou o valor de retorno.

Ao retornar uma referência de uma função, o parâmetro de lifetime para o
tipo de retorno precisa coincidir com o parâmetro de lifetime de um dos parâmetros. Se
a referência retornada *não* se referir a um dos parâmetros, ela deve se referir
a um valor criado dentro desta função. No entanto, isso seria uma referência suspensa
porque o valor sairá de escopo no final da função. Considere esta tentativa de implementação da função `longest` que não será compilada:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-09-unrelated-lifetime/src/main.rs:here}}
```

Aqui, mesmo que tenhamos especificado um parâmetro de lifetime `'a` para o tipo de retorno, esta implementação não será compilada porque a lifetime do valor de retorno não tem relação alguma com a lifetime dos parâmetros. Aqui está a mensagem de erro que obtemos:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-09-unrelated-lifetime/output.txt}}
```

O problema é que `result` sai de escopo e é limpo no final da função `longest`. Também estamos tentando retornar uma referência a `result` da função. Não há como especificar parâmetros de lifetime que mudariam a referência suspensa, e o Rust não nos permitirá criar uma referência suspensa. Neste caso, a melhor solução seria retornar um tipo de dado de propriedade (owned) em vez de uma referência, para que a função chamadora seja responsável pela limpeza do valor.

Em última análise, a sintaxe de lifetime trata de conectar as lifetimes de vários parâmetros e valores de retorno de funções. Uma vez conectadas, o Rust tem informações suficientes para permitir operações seguras de memória e desaprovar operações que criariam ponteiros suspensos ou violariam a segurança de memória.

### Anotações de Lifetime em Definições de Estruturas

Até agora, as estruturas que definimos armazenam tipos de propriedade (owned). Podemos definir estruturas para armazenar referências, mas nesse caso precisaríamos adicionar uma anotação de lifetime em cada referência na definição da estrutura. O Exemplo 10-24 contém uma estrutura chamada `ImportantExcerpt` que armazena uma fatia de string.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-24/src/main.rs}}
```

<span class="caption">Exemplo 10-24: Uma estrutura que armazena uma referência, requerendo
uma anotação de lifetime</span>

Esta estrutura tem um único campo chamado `part` que armazena uma fatia de string, que é uma referência. Assim como em tipos de dados genéricos, declaramos o nome do parâmetro de lifetime genérico dentro de colchetes angulares após o nome da estrutura para que possamos usar o parâmetro de lifetime no corpo da definição da estrutura. Esta anotação significa que uma instância de `ImportantExcerpt` não pode sobreviver à referência que ela mantém em seu campo `part`.

A função `main` aqui cria uma instância da estrutura `ImportantExcerpt` que mantém uma referência à primeira sentença da `String` de propriedade (owned) pela variável `novel`. Os dados em `novel` existem antes que a instância de `ImportantExcerpt` seja criada. Além disso, `novel` não sai de escopo até depois que `ImportantExcerpt` saia de escopo, então a referência na instância de `ImportantExcerpt` é válida.

### Elisão de Lifetimes

Você aprendeu que cada referência possui uma lifetime e que você precisa especificar
parâmetros de lifetime para funções ou estruturas que usam referências. No entanto, no
Capítulo 4, tivemos uma função no Exemplo 4-9, mostrada novamente no Exemplo 10-25, que
compilou sem anotações de lifetime.

<span class="filename">Filename: src/lib.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-25/src/main.rs:here}}
```

<span class="caption">Exemplo 10-25: Uma função que definimos no Exemplo 4-9 que
compilou sem anotações de lifetime, mesmo que o parâmetro e o tipo de retorno sejam referências</span>

A razão pela qual esta função compila sem anotações de lifetime é histórica:
nas primeiras versões (pré-1.0) do Rust, este código não teria compilado porque
cada referência precisava de uma lifetime explícita. Naquele momento, a assinatura da função teria sido escrita assim:

```rust,ignore
fn first_word<'a>(s: &'a str) -> &'a str {
```

Depois de escrever muito código Rust, a equipe do Rust descobriu que os programadores Rust estavam inserindo as mesmas anotações de lifetime repetidamente em situações específicas. Essas situações eram previsíveis e seguiam alguns padrões determinísticos. Os desenvolvedores programaram esses padrões no código do compilador para que o verificador de empréstimo pudesse inferir as lifetimes nessas situações e não precisasse de anotações explícitas.

Essa parte da história do Rust é relevante porque é possível que mais padrões determinísticos surjam e sejam adicionados ao compilador. No futuro, ainda menos anotações de lifetime podem ser necessárias.

Os padrões programados na análise de referências do Rust são chamados de *regras de elisão de lifetime*. Estas não são regras para os programadores seguirem; são um conjunto de casos particulares que o compilador considerará e, se o seu código se encaixar nesses casos, você não precisará escrever as lifetimes explicitamente.

As regras de elisão não fornecem inferência completa. Se o Rust aplicar deterministicamente as regras, mas ainda houver ambiguidade quanto às lifetimes das referências, o compilador não adivinhará qual deve ser a lifetime das referências restantes. Em vez de adivinhar, o compilador emitirá um erro que você pode resolver adicionando as anotações de lifetime.

As lifetimes nos parâmetros de função ou método são chamadas de *lifetimes de entrada*, e as lifetimes nos valores de retorno são chamadas de *lifetimes de saída*.

O compilador usa três regras para descobrir as lifetimes das referências quando não há anotações explícitas. A primeira regra se aplica às lifetimes de entrada, e a segunda e a terceira regras se aplicam às lifetimes de saída. Se o compilador chegar ao final das três regras e ainda houver referências para as quais não é possível determinar as lifetimes, o compilador emitirá um erro. Essas regras se aplicam às definições de `fn` e também aos blocos `impl`.

A primeira regra é que o compilador atribui um parâmetro de lifetime a cada parâmetro que é uma referência. Em outras palavras, uma função com um parâmetro recebe um parâmetro de lifetime: `fn foo<'a>(x: &'a i32)`; uma função com dois parâmetros recebe dois parâmetros de lifetime separados: `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`; e assim por diante.

A segunda regra é que, se houver exatamente um parâmetro de lifetime de entrada, essa lifetime será atribuída a todos os parâmetros de lifetime de saída: `fn foo<'a>(x: &'a i32) -> &'a i32`.

A terceira regra é que, se houver vários parâmetros de lifetime de entrada, mas um deles for `&self` ou `&mut self` porque se trata de um método, a lifetime de `self` será atribuída a todos os parâmetros de lifetime de saída. Essa terceira regra torna os métodos muito mais legíveis e fáceis de escrever, porque menos símbolos são necessários.

Vamos fingir que somos o compilador. Aplicaremos essas regras para descobrir as lifetimes das referências na assinatura da função `first_word` no Exemplo 10-25. A assinatura começa sem lifetimes associadas às referências:

```rust,ignore
fn first_word(s: &str) -> &str {
```

Em seguida, o compilador aplica a primeira regra, que especifica que cada parâmetro recebe sua própria lifetime. Vamos chamá-la de `'a` como de costume, então agora a assinatura é a seguinte:

```rust,ignore
fn first_word<'a>(s: &'a str) -> &str {
```

A segunda regra se aplica porque há exatamente uma lifetime de entrada. A segunda regra especifica que a lifetime do único parâmetro de entrada é atribuída à lifetime de saída, então agora a assinatura é esta:

```rust,ignore
fn first_word<'a>(s: &'a str) -> &'a str {
```

Agora, todas as referências nesta assinatura de função têm lifetimes, e o compilador pode continuar sua análise sem precisar que o programador anote as lifetimes nesta assinatura de função.

Vamos dar uma olhada em outro exemplo, desta vez usando a função `longest` que não tinha parâmetros de lifetime quando começamos a trabalhar com ela no Exemplo 10-20:

```rust,ignore
fn longest(x: &str, y: &str) -> &str {
```

Vamos aplicar a primeira regra: cada parâmetro recebe sua própria lifetime. Desta vez, temos dois parâmetros em vez de um, então temos duas lifetimes:

```rust,ignore
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &str {
```

Você pode ver que a segunda regra não se aplica porque há mais de uma lifetime de entrada. A terceira regra também não se aplica, porque `longest` é uma função e não um método, então nenhum dos parâmetros é `self`. Depois de trabalhar com todas as três regras, ainda não conseguimos determinar qual é a lifetime do tipo de retorno. É por isso que recebemos um erro ao tentar compilar o código no Exemplo 10-20: o compilador trabalhou com as regras de elisão de lifetimes, mas ainda não conseguiu determinar todas as lifetimes das referências na assinatura.

Como a terceira regra realmente se aplica principalmente em assinaturas de métodos, vamos analisar as lifetimes nesse contexto a seguir para entender por que a terceira regra significa que não precisamos anotar as lifetimes nas assinaturas de métodos com muita frequência.

### Anotações de Lifetimes em Definições de Métodos

Quando implementamos métodos em uma struct com lifetimes, usamos a mesma sintaxe que a de parâmetros de tipo genérico mostrada no Exemplo 10-11. Onde declaramos e usamos os parâmetros de lifetime depende se eles estão relacionados aos campos da struct ou aos parâmetros e valores de retorno do método.

Os nomes de lifetimes para campos de struct sempre precisam ser declarados após a palavra-chave `impl` e, em seguida, usados após o nome da struct, porque essas lifetimes fazem parte do tipo da struct.

Nas assinaturas de método dentro do bloco `impl`, as referências podem estar vinculadas à lifetime de referências nos campos da struct ou podem ser independentes. Além disso, as regras de elisão de lifetimes muitas vezes fazem com que as anotações de lifetime não sejam necessárias nas assinaturas de método. Vamos examinar alguns exemplos usando a struct chamada `ImportantExcerpt` que definimos no Exemplo 10-24.

Primeiro, usaremos um método chamado `level` cujo único parâmetro é uma referência a `self` e cujo valor de retorno é um `i32`, que não é uma referência a nada:

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-10-lifetimes-on-methods/src/main.rs:1st}}
```

A declaração de parâmetro de lifetime após `impl` e seu uso após o nome do tipo são obrigatórios, mas não somos obrigados a anotar a lifetime da referência a `self` por causa da primeira regra de elisão.

Aqui está um exemplo em que a terceira regra de elisão de lifetimes se aplica:

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-10-lifetimes-on-methods/src/main.rs:3rd}}
```

Há duas lifetimes de entrada, então o Rust aplica a primeira regra de elisão de lifetimes e atribui lifetimes separadas a `&self` e `announcement`. Em seguida, porque um dos parâmetros é `&self`, o tipo de retorno recebe a lifetime de `&self`, e todas as lifetimes foram consideradas.

### A Lifetime Estática

Uma lifetime especial que precisamos discutir é `'static`, que denota que a referência afetada *pode* viver durante toda a duração do programa. Todas as literais de string têm a lifetime `'static`, que podemos anotar da seguinte forma:

```rust
let s: &'static str = "I have a static lifetime.";
```

Os textos dessas strings são armazenados diretamente no binário do programa, o que está sempre disponível. Portanto, a lifetime de todas as literais de string é `'static`.

Você pode ver sugestões para usar a lifetime `'static` em mensagens de erro. Mas antes de especificar `'static` como a lifetime para uma referência, pense se a referência realmente vive durante toda a duração do seu programa ou não, e se você deseja que ela o faça. Na maioria das vezes, uma mensagem de erro sugerindo a lifetime `'static` resulta de uma tentativa de criar uma referência pendente ou de uma falta de correspondência das lifetimes disponíveis. Nesses casos, a solução é corrigir esses problemas, não especificar a lifetime `'static`.

## Parâmetros de Tipo Genérico, Limites de Trait e Lifetimes Juntos

Vamos dar uma breve olhada na sintaxe de especificar parâmetros de tipo genérico, limites de trait e lifetimes, tudo em uma única função!

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-11-generics-traits-and-lifetimes/src/main.rs:here}}
```

Este é o função `longest` do Listing 10-21 que retorna a fatia de string mais longa entre duas. Mas agora, ela tem um parâmetro extra chamado `ann` do tipo genérico `T`, que pode ser preenchido por qualquer tipo que implemente o trait `Display`, conforme especificado pela cláusula `where`. Este parâmetro extra será impresso usando `{}`, daí a necessidade da restrição do trait `Display`. Como as lifetimes são um tipo de genérico, as declarações do parâmetro de lifetime `'a` e do parâmetro de tipo genérico `T` vão na mesma lista dentro dos colchetes angulares após o nome da função.

## Resumo

Nós cobrimos muito neste capítulo! Agora que você sabe sobre parâmetros de tipo genérico, traits e limites de trait, e parâmetros de lifetime genéricos, você está pronto para escrever código sem repetição que funcione em muitas situações diferentes. Parâmetros de tipo genérico permitem que você aplique o código a tipos diferentes. Traits e limites de trait garantem que, mesmo que os tipos sejam genéricos, eles terão o comportamento de que o código precisa. Você aprendeu como usar anotações de lifetime para garantir que este código flexível não tenha referências pendentes. E toda essa análise acontece em tempo de compilação, o que não afeta o desempenho em tempo de execução!

Acredite ou não, há muito mais a aprender sobre os tópicos que discutimos neste capítulo: o Capítulo 17 discute objetos de trait, que são outra maneira de usar traits. Existem também cenários mais complexos envolvendo anotações de lifetime que você só precisará em cenários muito avançados; para esses, você deve ler a [Referência do Rust][reference]. Mas a seguir, você aprenderá como escrever testes em Rust para garantir que seu código esteja funcionando como deveria.

[references-and-borrowing]:
ch04-02-references-and-borrowing.html#references-and-borrowing
[string-slices-as-parameters]:
ch04-03-slices.html#string-slices-as-parameters
[reference]: ../reference/index.html
