## Variáveis e Mutabilidade

Como mencionado na seção ["Armazenando Valores com Variáveis"][storing-values-with-variables]<!-- ignore -->, por padrão, as variáveis são imutáveis em Rust. Esta é uma das muitas orientações que o Rust oferece para escrever seu código de uma maneira que aproveite a segurança e a facilidade de concorrência que o Rust oferece. No entanto, você ainda tem a opção de tornar suas variáveis mutáveis. Vamos explorar como e por que o Rust o incentiva a favorecer a imutabilidade e por que às vezes você pode optar por não fazer isso.

Quando uma variável é imutável, uma vez que um valor é vinculado a um nome, você não pode alterar esse valor. Para ilustrar isso, gere um novo projeto chamado *variables* em seu diretório *projects* usando `cargo new variables`.

Em seguida, em seu novo diretório *variables*, abra o arquivo *src/main.rs* e substitua seu código pelo código a seguir, que ainda não será compilado:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-01-variables-are-immutable/src/main.rs}}
```

Salve e execute o programa usando `cargo run`. Você deve receber uma mensagem de erro relacionada a um erro de imutabilidade, conforme mostrado neste resultado:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-01-variables-are-immutable/output.txt}}
```

Este exemplo mostra como o compilador ajuda você a encontrar erros em seus programas. Erros de compilação podem ser frustrantes, mas na realidade eles apenas significam que seu programa ainda não está fazendo com segurança o que você deseja; eles *não* significam que você não é um bom programador! Até os Rustaceans experientes ainda recebem erros de compilação.

Você recebeu a mensagem de erro `` cannot assign twice to immutable variable `x` `` porque tentou atribuir um segundo valor à variável imutável `x`.

É importante que obtenhamos erros de tempo de compilação quando tentamos alterar um valor designado como imutável, porque essa situação pode levar a bugs. Se uma parte do nosso código opera com a suposição de que um valor nunca mudará e outra parte do nosso código muda esse valor, é possível que a primeira parte do código não faça o que foi projetada para fazer. A causa desse tipo de bug pode ser difícil de rastrear depois, especialmente quando a segunda parte do código muda o valor apenas *às vezes*. O compilador Rust garante que, quando você afirma que um valor não mudará, ele realmente não mudará, para que você não precise controlá-lo por conta própria. Seu código, portanto, é mais fácil de entender.

Mas a mutabilidade pode ser muito útil e facilitar a escrita de código. Embora as variáveis sejam imutáveis por padrão, você pode torná-las mutáveis adicionando `mut` na frente do nome da variável, como fez no [Capítulo 2][storing-values-with-variables]<!-- ignore -->. Adicionar `mut` também transmite a intenção aos futuros leitores do código, indicando que outras partes do código estarão alterando o valor desta variável.

Por exemplo, vamos alterar o *src/main.rs* para o seguinte:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-02-adding-mut/src/main.rs}}
```

Quando executamos o programa agora, obtemos o seguinte:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-02-adding-mut/output.txt}}
```

Estamos autorizados a alterar o valor vinculado a `x` de `5` para `6` quando `mut` é usado. No final das contas, a decisão de usar mutabilidade ou não depende de você e do que você acha mais claro em uma situação específica.

## Constantes

Assim como variáveis imutáveis, *constantes* são valores que estão vinculados a um nome e não podem ser alterados, mas existem algumas diferenças entre constantes e variáveis.

Primeiro, você não pode usar `mut` com constantes. As constantes não são apenas imutáveis por padrão, elas são sempre imutáveis. Você declara constantes usando a palavra-chave `const` em vez da palavra-chave `let`, e o tipo do valor *deve* ser anotado. Vamos abordar tipos e anotações de tipos na próxima seção, [“Tipos de Dados”][data-types]<!-- ignore -->, então não se preocupe com os detalhes agora. Apenas saiba que você sempre deve anotar o tipo.

Constantes podem ser declaradas em qualquer escopo, incluindo o escopo global, o que as torna úteis para valores que muitas partes do código precisam conhecer.

A última diferença é que constantes só podem ser definidas como uma expressão constante, não o resultado de um valor que só pode ser calculado em tempo de execução.

Aqui está um exemplo de declaração de constante:

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

O nome da constante é `THREE_HOURS_IN_SECONDS` e seu valor é definido como o resultado da multiplicação de 60 (o número de segundos em um minuto) por 60 (o número de minutos em uma hora) por 3 (o número de horas que queremos contar neste programa). A convenção de nomenclatura do Rust para constantes é usar letras maiúsculas com underscores entre as palavras. O compilador é capaz de avaliar um conjunto limitado de operações em tempo de compilação, o que nos permite escolher escrever esse valor de uma maneira mais fácil de entender e verificar, em vez de definir essa constante com o valor 10.800. Consulte a [seção sobre avaliação de constantes no Rust Reference][const-eval] para obter mais informações sobre quais operações podem ser usadas ao declarar constantes.

As constantes são válidas durante todo o tempo em que um programa é executado, dentro do escopo em que foram declaradas. Essa propriedade torna as constantes úteis para valores em seu domínio de aplicação que várias partes do programa podem precisar conhecer, como o número máximo de pontos que qualquer jogador de um jogo pode ganhar, ou a velocidade da luz.

Nomear valores codificados diretamente usados em todo o seu programa como constantes é útil para transmitir o significado desse valor aos futuros mantenedores do código. Também ajuda a ter apenas um local em seu código que você precisaria alterar se o valor codificado diretamente precisasse ser atualizado no futuro.

### Sombreamento

Como você viu no tutorial do jogo de adivinhação no [Capítulo 2][comparing-the-guess-to-the-secret-number]<!-- ignore -->, você pode declarar uma nova variável com o mesmo nome de uma variável anterior. Os Rustaceans dizem que a primeira variável é *sombreada* pela segunda, o que significa que a segunda variável é o que o compilador verá quando você usa o nome da variável. Na prática, a segunda variável sobrepõe a primeira, assumindo qualquer uso do nome da variável para si mesma, até que ela mesma seja sombreada ou o escopo termine. Podemos sombrear uma variável usando o mesmo nome da variável e repetindo o uso da palavra-chave `let`, conforme segue:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-03-shadowing/src/main.rs}}
```

Este programa primeiro vincula `x` a um valor de `5`. Em seguida, ele cria uma nova variável `x`, repetindo `let x =`, tomando o valor original e adicionando `1`, para que o valor de `x` seja então `6`. Em seguida, dentro de um escopo interno criado com as chaves, a terceira instrução `let` também sombreia `x` e cria uma nova variável, multiplicando o valor anterior por `2`, dando a `x` um valor de `12`. Quando esse escopo termina, a sombreamento interno termina e `x` volta a ser `6`. Quando executamos este programa, ele produzirá a seguinte saída:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-03-shadowing/output.txt}}
```

O sombreamento é diferente de marcar uma variável como `mut` porque receberemos um erro de compilação se tentarmos acidentalmente reatribuir a essa variável sem usar a palavra-chave `let`. Ao usar `let`, podemos realizar algumas transformações em um valor, mas a variável permanecerá imutável após essas transformações terem sido concluídas.

A outra diferença entre `mut` e sombreamento é que, como estamos efetivamente criando uma nova variável quando usamos a palavra-chave `let` novamente, podemos alterar o tipo do valor, mas reutilizar o mesmo nome. Por exemplo, digamos que nosso programa peça ao usuário para indicar quantos espaços eles desejam entre algum texto, digitando caracteres de espaço, e depois queremos armazenar essa entrada como um número:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-04-shadowing-can-change-types/src/main.rs:here}}
```

A primeira variável `spaces` é do tipo string e a segunda variável `spaces` é do tipo número. O sombreamento nos permite evitar a necessidade de criar nomes diferentes, como `spaces_str` e `spaces_num`; em vez disso, podemos reutilizar o nome mais simples `spaces`. No entanto, se tentarmos usar `mut` para isso, como mostrado aqui, receberemos um erro de compilação:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-05-mut-cant-change-types/src/main.rs:here}}
```

O erro diz que não é permitido alterar o tipo de uma variável:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-05-mut-cant-change-types/output.txt}}
```

Agora que exploramos como as variáveis funcionam, vamos examinar mais tipos de dados que elas podem ter.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[data-types]: ch03-02-data-types.html#data-types
[storing-values-with-variables]: ch02-00-guessing-game-tutorial.html#storing-values-with-variables
[const-eval]: ../reference/const_eval.html
