## Funções

Funções são comuns no código Rust. Você já viu uma das funções mais importantes na linguagem: a função `main`, que é o ponto de entrada de muitos programas. Você também viu a palavra-chave `fn`, que permite declarar novas funções.

O código Rust utiliza o *snake case* como estilo convencional para nomes de funções e variáveis, onde todas as letras são minúsculas e underscores (_) separam palavras. Aqui está um programa que contém um exemplo de definição de função:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-16-functions/src/main.rs}}
```

Definimos uma função em Rust inserindo `fn` seguido de um nome de função e um conjunto de parênteses. As chaves indicam ao compilador onde o corpo da função começa e termina.

Podemos chamar qualquer função que tenhamos definido inserindo seu nome seguido de um conjunto de parênteses. Como `another_function` está definida no programa, pode ser chamada de dentro da função `main`. Observe que definimos `another_function` *depois* da função `main` no código-fonte; poderíamos tê-la definido antes também. O Rust não se importa onde você define suas funções, apenas que elas sejam definidas em algum lugar em um escopo que possa ser visto pelo chamador.

Vamos iniciar um novo projeto binário chamado *functions* para explorar mais funções. Coloque o exemplo de `another_function` em *src/main.rs* e execute-o. Você deve ver a seguinte saída:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-16-functions/output.txt}}
```

As linhas são executadas na ordem em que aparecem na função `main`. Primeiro, a mensagem "Hello, world!" é impressa e, em seguida, `another_function` é chamada e sua mensagem é impressa.

### Parâmetros

Podemos definir funções com *parâmetros*, que são variáveis especiais que fazem parte da assinatura de uma função. Quando uma função possui parâmetros, você pode fornecer valores concretos para esses parâmetros. Tecnicamente, os valores concretos são chamados de *argumentos*, mas em conversas informais, as pessoas tendem a usar as palavras *parâmetro* e *argumento* de forma intercambiável, tanto para as variáveis na definição de uma função quanto para os valores concretos passados quando você chama uma função.

Nesta versão de `another_function`, adicionamos um parâmetro:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-17-functions-with-parameters/src/main.rs}}
```

Tente executar este programa; você deve obter a seguinte saída:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-17-functions-with-parameters/output.txt}}
```

A declaração de `another_function` possui um parâmetro chamado `x`. O tipo de `x` é especificado como `i32`. Quando passamos `5` para `another_function`, a macro `println!` coloca `5` onde estava o par de chaves que continha `x` na string de formato.

Nas assinaturas de funções, você *deve* declarar o tipo de cada parâmetro. Isso é uma decisão deliberada no design do Rust: exigir anotações de tipo em definições de função significa que o compilador quase nunca precisa que você as use em outras partes do código para determinar qual tipo você está se referindo. O compilador também é capaz de fornecer mensagens de erro mais úteis se ele souber quais tipos a função espera.

Ao definir vários parâmetros, separe as declarações de parâmetros com vírgulas, como mostrado aqui:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-18-functions-with-multiple-parameters/src/main.rs}}
```

Este exemplo cria uma função chamada `print_labeled_measurement` com dois parâmetros. O primeiro parâmetro é chamado de `value` e é do tipo `i32`. O segundo é chamado de `unit_label` e é do tipo `char`. A função então imprime texto que contém tanto o `value` quanto o `unit_label`.

Vamos tentar executar este código. Substitua o programa atualmente em seu arquivo *src/main.rs* do projeto *functions* pelo exemplo anterior e execute-o usando `cargo run`:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-18-functions-with-multiple-parameters/output.txt}}
```

Como chamamos a função com `5` como valor para `value` e `'h'` como valor para `unit_label`, a saída do programa contém esses valores.

### Declarações e Expressões

Os corpos das funções são compostos por uma série de declarações que opcionalmente terminam em uma expressão. Até agora, as funções que cobrimos não incluíram uma expressão de finalização, mas você já viu uma expressão como parte de uma declaração. Como o Rust é uma linguagem baseada em expressões, esta é uma distinção importante a ser compreendida. Outras linguagens não têm as mesmas distinções, então vamos analisar o que são declarações e expressões e como suas diferenças afetam os corpos das funções.

* **Declarações** são instruções que executam alguma ação e não retornam um valor.
* **Expressões** avaliam um valor resultante. Vamos ver alguns exemplos.

Na verdade, já usamos declarações e expressões. Criar uma variável e atribuir um valor a ela com a palavra-chave `let` é uma declaração. No Exemplo 3-1, `let y = 6;` é uma declaração.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-01/src/main.rs}}
```

<span class="caption">Exemplo 3-1: Uma declaração da função `main` contendo uma declaração</span>

As definições de funções também são declarações; todo o exemplo anterior é uma declaração em si.

As declarações não retornam valores. Portanto, você não pode atribuir uma declaração `let` a outra variável, como o código a seguir tenta fazer; você receberá um erro:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-19-statements-vs-expressions/src/main.rs}}
```

Quando você executar este programa, o erro que você receberá se parecerá com isto:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-19-statements-vs-expressions/output.txt}}
```

A declaração `let y = 6` não retorna um valor, portanto, não há nada para `x` se vincular. Isso é diferente do que acontece em outras linguagens, como C e Ruby, onde a atribuição retorna o valor da atribuição. Nessas linguagens, você pode escrever `x = y = 6` e ter tanto `x` quanto `y` com o valor `6`; esse não é o caso no Rust.

Expressões avaliam um valor e compõem a maior parte do restante do código que você escreverá em Rust. Considere uma operação matemática, como `5 + 6`, que é uma expressão que avalia para o valor `11`. Expressões podem fazer parte de declarações: no Exemplo 3-1, o `6` na declaração `let y = 6;` é uma expressão que avalia para o valor `6`. Chamar uma função é uma expressão. Chamar uma macro é uma expressão. Um novo bloco de escopo criado com chaves é uma expressão, por exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-20-blocks-are-expressions/src/main.rs}}
```

Esta expressão:

```rust,ignore
{
    let x = 3;
    x + 1
}
```

é um bloco que, neste caso, avalia para `4`. Esse valor fica vinculado a `y` como parte da declaração `let`. Observe que a linha `x + 1` não tem um ponto e vírgula no final, o que é diferente da maioria das linhas que você viu até agora. Expressões não incluem pontos e vírgulas finais. Se você adicionar um ponto e vírgula ao final de uma expressão, ela se tornará uma declaração e, em seguida, não retornará um valor. Mantenha isso em mente ao explorar os valores de retorno de funções e expressões a seguir.

### Funções com Valores de Retorno

Funções podem retornar valores para o código que as chama. Não nomeamos os valores de retorno, mas devemos declarar seu tipo após uma seta (`->`). No Rust, o valor de retorno da função é sinônimo do valor da última expressão no bloco do corpo da função. Você pode retornar antecipadamente de uma função usando a palavra-chave `return` e especificando um valor, mas a maioria das funções retorna implicitamente a última expressão. Aqui está um exemplo de uma função que retorna um valor:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-21-function-return-values/src/main.rs}}
```

Não há chamadas de função, macros ou mesmo declarações `let` na função `five` - apenas o número `5` por si só. Isso é perfeitamente válido em Rust. Observe que o tipo de retorno da função também é especificado como `-> i32`. Tente executar este código; a saída deve ser semelhante a esta:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-21-function-return-values/output.txt}}
```

O `5` em `five` é o valor de retorno da função, por isso o tipo de retorno é `i32`. Vamos examinar isso com mais detalhes. Existem duas partes importantes: primeiro, a linha `let x = five();` mostra que estamos usando o valor de retorno de uma função para inicializar uma variável. Como a função `five` retorna `5`, essa linha é equivalente ao seguinte:

```rust
let x = 5;
```

Em segundo lugar, a função `five` não possui parâmetros e define o tipo do valor de retorno, mas o corpo da função é apenas um `5` solitário sem ponto e vírgula porque é uma expressão cujo valor queremos retornar.

Vamos dar uma olhada em outro exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-22-function-parameter-and-return/src/main.rs}}
```

A execução deste código imprimirá `O valor de x é: 6`. Mas se colocarmos um ponto e vírgula no final da linha que contém `x + 1`, transformando-a de uma expressão em uma declaração, receberemos um erro:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-23-statements-dont-return-values/src/main.rs}}
```

Compilar este código produz um erro, como a seguir:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-23-statements-dont-return-values/output.txt}}
```

A mensagem de erro principal, `mismatched types`, revela o problema principal deste código. A definição da função `plus_one` diz que ela retornará um `i32`, mas declarações não avaliam um valor, o que é expresso por `()`, o tipo unitário. Portanto, nada é retornado, o que contradiz a definição da função e resulta em um erro. Nesta saída, o Rust fornece uma mensagem para possivelmente ajudar a corrigir esse problema: ele sugere remover o ponto e vírgula, o que resolveria o erro.
