## Tipos de Dados

Em Rust, cada valor é de um *tipo de dado* específico, o que informa ao Rust que tipo de
dados está sendo especificado, para que ele saiba como trabalhar com esses dados. Vamos olhar para
dois subconjuntos de tipos de dados: escalares e compostos.

Lembre-se de que Rust é uma linguagem *tipada estaticamente*, o que significa que
ela deve conhecer os tipos de todas as variáveis em tempo de compilação. O compilador geralmente consegue
inferir qual tipo queremos usar com base no valor e em como o utilizamos. Em casos
em que muitos tipos são possíveis, como quando convertemos uma `String` para um tipo numérico usando `parse` na seção [“Comparando o Palpite com o Número Secreto”][comparando-o-palpite-com-o-número-secreto]<!-- ignore --> no
Capítulo 2, devemos adicionar uma anotação de tipo, assim:

[comparando-o-palpite-com-o-número-secreto]: link-para-o-capítulo-2

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

Se não adicionarmos a anotação de tipo `: u32` mostrada no código anterior, o Rust
exibirá o seguinte erro, o que significa que o compilador precisa de mais
informações de nossa parte para saber qual tipo queremos usar:

```console
{{#include ../listings/ch03-common-programming-concepts/output-only-01-no-type-annotations/output.txt}}
```

Você verá diferentes anotações de tipo para outros tipos de dados.

### Tipos Escalares

Um tipo *escalar* representa um único valor. Rust possui quatro tipos escalares primários:
inteiros, números de ponto flutuante, booleanos e caracteres. Você pode reconhecer
esses tipos de outras linguagens de programação. Vamos explorar como eles funcionam em Rust.

#### Tipos Inteiros

Um *inteiro* é um número sem componente fracionário. Usamos um tipo inteiro
no Capítulo 2, o tipo `u32`. Essa declaração de tipo indica que o
valor ao qual está associado deve ser um inteiro sem sinal (os tipos inteiros com sinal
começam com `i` em vez de `u`) que ocupa 32 bits de espaço. A Tabela 3-1 mostra
os tipos inteiros embutidos em Rust. Podemos usar qualquer uma dessas variantes para declarar
o tipo de um valor inteiro.

<span class="caption">Tabela 3-1: Tipos Inteiros em Rust</span>

| Length  | Signed  | Unsigned |
|---------|---------|----------|
| 8-bit   | `i8`    | `u8`     |
| 16-bit  | `i16`   | `u16`    |
| 32-bit  | `i32`   | `u32`    |
| 64-bit  | `i64`   | `u64`    |
| 128-bit | `i128`  | `u128`   |
| arch    | `isize` | `usize`  |

Cada variante pode ser assinada (signed) ou não assinada (unsigned) e tem um tamanho explícito.
*Assinada* e *não assinada* referem-se a se é possível que o número seja
negativo, ou seja, se o número precisa ter um sinal junto com ele
(assinada) ou se ele sempre será positivo e, portanto, pode ser
representado sem um sinal (não assinada). É como escrever números em papel: quando
o sinal importa, um número é mostrado com um sinal de mais ou um sinal de menos; no entanto,
quando é seguro assumir que o número é positivo, ele é mostrado sem sinal.
Números assinados são armazenados usando a representação [complemento de dois][complemento-de-dois]<!-- ignore
-->.

Cada variante assinada pode armazenar números no intervalo de -(2<sup>n - 1</sup>) a 2<sup>n -
1</sup> - 1, onde *n* é o número de bits que a variante utiliza. Portanto, um
`i8` pode armazenar números no intervalo de -(2<sup>7</sup>) a 2<sup>7</sup> - 1, o que equivale a
-128 a 127. As variantes não-assinadas podem armazenar números no intervalo de 0 a 2<sup>n</sup> - 1,
então um `u8` pode armazenar números no intervalo de 0 a 2<sup>8</sup> - 1, o que equivale a 0 a 255.

Além disso, os tipos `isize` e `usize` dependem da arquitetura do
computador em que seu programa está sendo executado, o que é indicado na tabela como "arq":
64 bits se você estiver em uma arquitetura de 64 bits e 32 bits se você estiver em uma arquitetura de 32 bits.

Você pode escrever literais inteiros em qualquer uma das formas mostradas na Tabela 3-2. Note
que literais de números que podem ser de vários tipos numéricos permitem um sufixo de tipo,
como `57u8`, para designar o tipo. Literais de números também podem usar `_` como um
separador visual para tornar o número mais fácil de ler, como `1_000`, que terá
o mesmo valor que se você tivesse especificado `1000`.

<span class="caption">Tabela 3-2: Literais Inteiros em Rust</span>

| Number literals  | Example       |
|------------------|---------------|
| Decimal          | `98_222`      |
| Hex              | `0xff`        |
| Octal            | `0o77`        |
| Binary           | `0b1111_0000` |
| Byte (`u8` only) | `b'A'`        |

Então, como você sabe qual tipo de inteiro usar? Se você não tem certeza, as predefinições do Rust geralmente são bons pontos de partida: os tipos inteiros têm como padrão `i32`. A situação principal em que você usaria `isize` ou `usize` é ao indexar algum tipo de coleção.

> ##### Overflow de Inteiros
>
> Suponha que você tenha uma variável do tipo `u8` que pode armazenar valores entre 0 e
> 255. Se você tentar alterar a variável para um valor fora desse intervalo, como
> 256, ocorrerá *overflow de inteiro*, o que pode resultar em um de dois comportamentos.
> Quando você está compilando em modo de depuração, o Rust inclui verificações de overflow de inteiros
> que fazem com que o seu programa *entre em pânico* durante a execução se esse comportamento ocorrer. O Rust
> usa o termo *panique* quando um programa encerra com um erro; discutiremos
> paniques com mais profundidade na seção [“Erros Irrecuperáveis com
> `panic!`”][unrecoverable-errors-with-panic]<!-- ignore --> no Capítulo 9.
>
> Quando você está compilando em modo de lançamento com a flag `--release`, o Rust *não* inclui verificações de overflow de inteiro que causam paniques. Em vez disso, se ocorrer overflow, o Rust realiza *enrolamento de complemento de dois*. Em resumo, valores maiores que o valor máximo que o tipo pode conter "enrolam" para o mínimo dos valores que o tipo pode conter. No caso de um `u8`, o valor 256 se torna 0, o valor 257 se torna 1 e assim por diante. O programa não entrará em pânico, mas a variável terá um valor que provavelmente não é o que você estava esperando.
>
> Confiar no comportamento de enrolamento do overflow de inteiros é considerado um erro.
>
> Para lidar explicitamente com a possibilidade de overflow, você pode usar essas famílias de métodos fornecidos pela biblioteca padrão para tipos numéricos primitivos:
>
> * Enrolar em todos os modos com os métodos `wrapping_*`, como `wrapping_add`.
> * Retornar o valor `None` se houver overflow com os métodos `checked_*`.
> * Retornar o valor e um booleano indicando se houve overflow com os métodos `overflowing_*`.
> * Saturar nos valores mínimo ou máximo do valor com os métodos `saturating_*`.

#### Tipos de Ponto Flutuante

Rust também possui dois tipos primitivos para *números de ponto flutuante*, que são
números com pontos decimais. Os tipos de ponto flutuante em Rust são `f32` e `f64`,
que têm 32 bits e 64 bits de tamanho, respectivamente. O tipo padrão é `f64`
porque em CPUs modernas, ele tem aproximadamente a mesma velocidade que `f32`, mas é capaz de
mais precisão. Todos os tipos de ponto flutuante são assinados.

Aqui está um exemplo que mostra números de ponto flutuante em ação:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-06-floating-point/src/main.rs}}
```

Números de ponto flutuante são representados de acordo com o padrão IEEE-754. O tipo `f32` é um número de ponto flutuante de precisão simples, e o `f64` tem precisão dupla.

#### Numeric Operations

Rust suporta as operações matemáticas básicas que você esperaria para todos os tipos de números: adição, subtração, multiplicação, divisão e resto da divisão. A divisão de inteiros trunca em direção a zero para o inteiro mais próximo. O código a seguir mostra como você usaria cada operação numérica em uma declaração `let`:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-07-numeric-operations/src/main.rs}}
```

Cada expressão nessas declarações usa um operador matemático e avalia para um único valor, que é então associado a uma variável. [Apêndice B][appendice_b]<!-- ignore --> contém uma lista de todos os operadores que Rust fornece.

#### O Tipo Booleano

Como na maioria das outras linguagens de programação, um tipo booleano em Rust tem dois valores possíveis: `true` e `false`. Os booleanos têm um tamanho de um byte. O tipo booleano em Rust é especificado usando `bool`. Por exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-08-boolean/src/main.rs}}
```

A principal maneira de usar valores booleanos é por meio de condicionais, como uma expressão `if`. Vamos abordar como as expressões `if` funcionam em Rust na seção [“Fluxo de Controle”][fluxo-de-controle]<!-- ignore -->.

#### O Tipo de Caractere

O tipo `char` em Rust é o tipo alfabético mais primitivo da linguagem. Aqui estão
alguns exemplos de declaração de valores `char`:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-09-char/src/main.rs}}
```

Observe que especificamos literais `char` com aspas simples, ao contrário de literais de strings, que usam aspas duplas. O tipo `char` do Rust tem quatro bytes de tamanho e representa um Valor Escalar Unicode (Unicode Scalar Value), o que significa que ele pode representar muito mais do que apenas ASCII. Letras acentuadas, caracteres chineses, japoneses e coreanos, emojis e espaços de largura zero são todos valores `char` válidos em Rust. Valores Escalar Unicode variam de `U+0000` a `U+D7FF` e de `U+E000` a `U+10FFFF`, inclusive. No entanto, um "caractere" não é realmente um conceito no Unicode, portanto, sua intuição humana do que é um "caractere" pode não coincidir com o que é um `char` em Rust. Discutiremos esse tópico em detalhes em [“Armazenando Texto Codificado em UTF-8 com Strings”][strings]<!-- ignore --> no Capítulo 8.

### Tipos Compostos

Os *tipos compostos* podem agrupar múltiplos valores em um único tipo. Rust possui dois
tipos compostos primitivos: tuplas e arrays.

#### O Tipo Tupla

Uma *tupla* é uma forma geral de agrupar um número de valores com uma
variedade de tipos em um único tipo composto. Tuplas têm um comprimento fixo: uma vez
declaradas, elas não podem crescer ou diminuir de tamanho.

Criamos uma tupla escrevendo uma lista de valores separados por vírgulas dentro
de parênteses. Cada posição na tupla tem um tipo, e os tipos dos
diferentes valores na tupla não precisam ser os mesmos. Adicionamos anotações de tipo opcionais neste exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-10-tuples/src/main.rs}}
```

A variável `tup` se vincula à tupla inteira porque uma tupla é considerada um único elemento composto. Para obter os valores individuais de uma tupla, podemos usar a correspondência de padrões para desestruturar um valor de tupla, como segue:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-11-destructuring-tuples/src/main.rs}}
```

Este programa primeiro cria uma tupla e a vincula à variável `tup`. Em seguida, utiliza um padrão com `let` para pegar `tup` e transformá-la em três variáveis separadas: `x`, `y` e `z`. Isso é chamado de *destructuring* (desestruturação) porque quebra a única tupla em três partes. Por fim, o programa imprime o valor de `y`, que é `6.4`.

Também podemos acessar um elemento de uma tupla diretamente usando um ponto (`.`) seguido do índice do valor que queremos acessar. Por exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-12-tuple-indexing/src/main.rs}}
```

Este programa cria a tupla `x` e, em seguida, acessa cada elemento da tupla usando seus respectivos índices. Como na maioria das linguagens de programação, o primeiro índice em uma tupla é 0.

A tupla sem nenhum valor tem um nome especial, *unit*. Esse valor e seu tipo correspondente são escritos como `()` e representam um valor vazio ou um tipo de retorno vazio. Expressões retornam implicitamente o valor de unidade se elas não retornarem outro valor.

#### O Tipo Array

Outra forma de ter uma coleção de múltiplos valores é com um *array*. Ao contrário de uma tupla, cada elemento de um array deve ter o mesmo tipo. Diferentemente de arrays em algumas outras linguagens, arrays em Rust têm um comprimento fixo.

Nós escrevemos os valores em um array como uma lista separada por vírgulas dentro de colchetes:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-13-arrays/src/main.rs}}
```

Arrays são úteis quando você deseja alocar seus dados na pilha (stack) em vez do heap (discutiremos a pilha e o heap com mais detalhes no [Capítulo 4][stack-and-heap]<!-- ignore -->) ou quando deseja garantir que sempre tenha um número fixo de elementos. No entanto, um array não é tão flexível quanto o tipo vector.

Um *vector* é um tipo de coleção semelhante fornecido pela biblioteca padrão que *pode* crescer ou diminuir em tamanho. Se você não tem certeza se deve usar um array ou um vector, provavelmente deve usar um vector. O [Capítulo 8][vectors]<!-- ignore --> discute os vetores com mais detalhes.

No entanto, os arrays são mais úteis quando você sabe que o número de elementos não precisará ser alterado. Por exemplo, se estivesse usando os nomes dos meses em um programa, provavelmente usaria um array em vez de um vector, porque você sabe que sempre conterá 12 elementos:

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

Você escreve o tipo de um array usando colchetes com o tipo de cada elemento, um ponto e vírgula e, em seguida, o número de elementos no array, como segue:

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

Aqui, `i32` é o tipo de cada elemento. Após o ponto e vírgula, o número `5` indica que o array contém cinco elementos.

Você também pode inicializar um array para conter o mesmo valor para cada elemento, especificando o valor inicial, seguido por um ponto e vírgula e, em seguida, o comprimento do array entre colchetes, como mostrado aqui:

```rust
let a = [3; 5];
```

O array chamado `a` conterá `5` elementos, que inicialmente serão todos definidos como o valor `3`. Isso é equivalente a escrever `let a = [3, 3, 3, 3, 3];`, mas de uma forma mais concisa.

##### Acessando Elementos de um Array

Um array é um único bloco de memória de tamanho conhecido e fixo que pode ser alocado na pilha. Você pode acessar elementos de um array usando indexação, como segue:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-14-array-indexing/src/main.rs}}
```

Neste exemplo, a variável chamada `first` receberá o valor `1`, pois esse é o valor no índice `[0]` no array. A variável chamada `second` receberá o valor `2` do índice `[1]` no array.

##### Acesso Inválido a Elementos de um Array

Vamos ver o que acontece se você tentar acessar um elemento de um array que está além do final do array. Digamos que você execute este código, semelhante ao jogo de adivinhação do Capítulo 2, para obter um índice de array do usuário:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,panics
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access/src/main.rs}}
```

Este código compila com sucesso. Se você executar este código usando `cargo run` e inserir `0`, `1`, `2`, `3` ou `4`, o programa imprimirá o valor correspondente a esse índice no array. Se você, em vez disso, inserir um número além do final do array, como `10`, verá uma saída como esta:

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access
cargo run
10
-->

```console
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

O programa resultou em um erro de *tempo de execução* no ponto em que foi usado um valor inválido na operação de indexação. O programa saiu com uma mensagem de erro e não executou a instrução `println!` final. Quando você tenta acessar um elemento usando indexação, o Rust verifica se o índice que você especificou é menor do que o comprimento do array. Se o índice for maior ou igual ao comprimento, o Rust gerará um erro. Essa verificação precisa ocorrer em tempo de execução, especialmente neste caso, porque o compilador não pode saber qual valor um usuário inserirá quando executar o código posteriormente.

Este é um exemplo dos princípios de segurança de memória do Rust em ação. Em muitas linguagens de baixo nível, esse tipo de verificação não é realizado e, quando você fornece um índice incorreto, a memória inválida pode ser acessada. O Rust protege você contra esse tipo de erro saindo imediatamente em vez de permitir o acesso à memória e continuar a execução. O Capítulo 9 discute mais sobre o tratamento de erros do Rust e como você pode escrever código legível e seguro que não gera panics nem permite acesso a memória inválida.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[twos-complement]: https://en.wikipedia.org/wiki/Two%27s_complement
[control-flow]: ch03-05-control-flow.html#control-flow
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[stack-and-heap]: ch04-01-what-is-ownership.html#the-stack-and-the-heap
[vectors]: ch08-01-vectors.html
[unrecoverable-errors-with-panic]: ch09-01-unrecoverable-errors-with-panic.html
[appendix_b]: appendix-02-operators.md
