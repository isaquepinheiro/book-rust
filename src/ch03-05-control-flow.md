## Fluxo de Controle

A capacidade de executar algum código dependendo se uma condição é `true` e de executar algum código repetidamente enquanto uma condição é `true` são blocos de construção básicos na maioria das linguagens de programação. As construções mais comuns que permitem controlar o fluxo de execução do código Rust são as expressões `if` e os loops.

### `if` Expressions

Uma expressão `if` permite que você ramifique seu código dependendo de condições. Você fornece uma condição e, em seguida, declara: "Se esta condição for atendida, execute este bloco de código. Se a condição não for atendida, não execute este bloco de código."

Crie um novo projeto chamado *branches* em seu diretório de *projects* para explorar a expressão `if`. No arquivo *src/main.rs*, insira o seguinte:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-26-if-true/src/main.rs}}
```

Todas as expressões `if` começam com a palavra-chave `if`, seguida de uma condição. Neste caso, a condição verifica se a variável `number` tem um valor menor que 5. Colocamos o bloco de código para executar se a condição for `true` imediatamente após a condição dentro de chaves. Blocos de código associados às condições em expressões `if` às vezes são chamados de *ramos*, assim como os ramos em expressões `match` que discutimos na seção ["Comparando o Palpite com o Número Secreto"][comparing-the-guess-to-the-secret-number]<!-- ignore --> do Capítulo 2.

Opcionalmente, também podemos incluir uma expressão `else`, o que escolhemos fazer aqui, para dar ao programa um bloco de código alternativo para executar caso a condição seja avaliada como `false`. Se você não fornecer uma expressão `else` e a condição for `false`, o programa simplesmente ignorará o bloco `if` e continuará com o próximo trecho de código.

Tente executar este código; você deve ver a seguinte saída:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-26-if-true/output.txt}}
```

Vamos tentar alterar o valor de `number` para um valor que torne a condição `false` para ver o que acontece:

```rust,ignore
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-27-if-false/src/main.rs:here}}
```

Execute o programa novamente e observe a saída:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-27-if-false/output.txt}}
```

Também vale a pena notar que a condição neste código *deve ser* um `bool`. Se a condição não for um `bool`, receberemos um erro. Por exemplo, tente executar o seguinte código:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-28-if-condition-must-be-bool/src/main.rs}}
```

A condição `if` avalia para um valor `3` desta vez, e o Rust gera um erro:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-28-if-condition-must-be-bool/output.txt}}
```

O erro indica que o Rust esperava um `bool`, mas recebeu um número inteiro. Ao contrário de linguagens como Ruby e JavaScript, o Rust não tentará automaticamente converter tipos não booleanos em um booleano. Você deve ser explícito e sempre fornecer um booleano como condição para o `if`. Se quisermos que o bloco de código `if` seja executado apenas quando um número não for igual a `0`, por exemplo, podemos alterar a expressão `if` da seguinte forma:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-29-if-not-equal-0/src/main.rs}}
```

A execução deste código imprimirá: `number was something other than zero`.

#### Lidando com Múltiplas Condições com `else if`

Você pode usar várias condições combinando `if` e `else` em uma expressão `else if`. Por exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-30-else-if/src/main.rs}}
```

Este programa tem quatro caminhos possíveis que ele pode seguir. Depois de executá-lo, você deve ver a seguinte saída:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-30-else-if/output.txt}}
```

Quando este programa é executado, ele verifica cada expressão `if` sequencialmente e executa o primeiro bloco cuja condição avalia para `true`. Note que mesmo que 6 seja divisível por 2, não vemos a saída "number is divisible by 2", nem vemos o texto "number is not divisible by 4, 3, or 2" do bloco `else`. Isso ocorre porque o Rust executa apenas o bloco para a primeira condição `true` que encontra e, uma vez encontrada uma condição verdadeira, ele nem mesmo verifica o restante.

Usar muitas expressões `else if` pode deixar seu código confuso, portanto, se você tiver mais de uma, convém refatorar seu código. O Capítulo 6 descreve uma construção de ramificação poderosa em Rust chamada `match` para esses casos.

#### Usando `if` em uma Declaração `let`

Como o `if` é uma expressão, podemos usá-lo no lado direito de uma declaração `let` para atribuir o resultado a uma variável, como mostrado no Exemplo 3-2.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-02/src/main.rs}}
```

<span class="caption">Exemplo 3-2: Atribuindo o resultado de uma expressão `if` a uma variável</span>

A variável `number` será vinculada a um valor com base no resultado da expressão `if`. Execute este código para ver o que acontece:

```console
{{#include ../listings/ch03-common-programming-concepts/listing-03-02/output.txt}}
```

Lembre-se de que blocos de código avaliam para a última expressão dentro deles, e números por si só também são expressões. Neste caso, o valor de toda a expressão `if` depende de qual bloco de código é executado. Isso significa que os valores que têm o potencial de ser resultados de cada braço do `if` devem ser do mesmo tipo; no Exemplo 3-2, os resultados tanto do braço `if` quanto do braço `else` eram inteiros `i32`. Se os tipos estiverem incompatíveis, como no exemplo a seguir, receberemos um erro:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-31-arms-must-return-same-type/src/main.rs}}
```

Quando tentamos compilar este código, receberemos um erro. Os tipos de valores nos braços `if` e `else` são incompatíveis, e o Rust indica exatamente onde está o problema no programa:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-31-arms-must-return-same-type/output.txt}}
```

A expressão no bloco `if` avalia para um inteiro, e a expressão no bloco `else` avalia para uma string. Isso não funcionará porque as variáveis devem ter um único tipo, e o Rust precisa saber em tempo de compilação qual é o tipo da variável `number`, definitivamente. Saber o tipo de `number` permite que o compilador verifique se o tipo é válido em todos os lugares em que usamos `number`. O Rust não seria capaz de fazer isso se o tipo de `number` fosse determinado apenas em tempo de execução; o compilador seria mais complexo e faria menos garantias sobre o código se tivesse que acompanhar múltiplos tipos hipotéticos para qualquer variável.

### Repetição com Laços

É frequentemente útil executar um bloco de código mais de uma vez. Para esta tarefa, o Rust fornece vários *laços*, que executarão o código dentro do corpo do laço até o final e depois começarão imediatamente de novo no início. Para experimentar com laços, vamos criar um novo projeto chamado *loops*.

O Rust possui três tipos de laços: `loop`, `while` e `for`. Vamos experimentar cada um deles.

#### Repetindo Código com o `loop`

A palavra-chave `loop` instrui o Rust a executar um bloco de código repetidamente para sempre ou até que você explicitamente o instrua a parar.

Como exemplo, altere o arquivo *src/main.rs* em seu diretório *loops* para ficar assim:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-loop/src/main.rs}}
```

Quando executamos este programa, veremos "again!" impresso continuamente até que paremos o programa manualmente. A maioria dos terminais suporta o atalho de teclado <span class="keystroke">ctrl-c</span> para interromper um programa que está preso em um loop contínuo. Experimente:

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-32-loop
cargo run
CTRL-C
-->

```console
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29s
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```

O símbolo `^C` representa onde você pressionou <span class="keystroke">ctrl-c</span>. Você pode ou não ver a palavra `again!` impressa após o `^C`, dependendo de onde o código estava no loop quando ele recebeu o sinal de interrupção.

Felizmente, o Rust também fornece uma maneira de sair de um loop usando código. Você pode usar a palavra-chave `break` dentro do loop para dizer ao programa quando parar de executar o loop. Lembre-se de que fizemos isso no jogo de adivinhação na seção ["Saindo após um palpite correto"][quitting-after-a-correct-guess]<!-- ignore --> do Capítulo 2 para sair do programa quando o usuário ganhou o jogo ao adivinhar o número correto.

Também usamos `continue` no jogo de adivinhação, o que em um loop diz ao programa para pular qualquer código restante nesta iteração do loop e ir para a próxima iteração.

#### Retornando Valores de Loops

Uso comum de um `loop` é tentar novamente uma operação que você sabe que pode falhar, como verificar se uma thread completou seu trabalho. Você também pode precisar passar o resultado dessa operação para fora do loop para o restante do seu código. Para fazer isso, você pode adicionar o valor que deseja retornar após a expressão `break` que você usa para interromper o loop; esse valor será retornado do loop para que você possa usá-lo, como mostrado aqui:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-33-return-value-from-loop/src/main.rs}}
```

Antes do loop, declaramos uma variável chamada `counter` e a inicializamos com `0`. Em seguida, declaramos uma variável chamada `result` para armazenar o valor retornado do loop. Em cada iteração do loop, adicionamos `1` à variável `counter` e, em seguida, verificamos se `counter` é igual a `10`. Quando isso acontece, usamos a palavra-chave `break` com o valor `counter * 2`. Após o loop, usamos um ponto e vírgula para encerrar a instrução que atribui o valor a `result`. Finalmente, imprimimos o valor em `result`, que neste caso é `20`.

#### Rótulos de Loops para Desambiguar Entre Múltiplos Loops

Se você tiver loops dentro de loops, `break` e `continue` se aplicarão ao loop mais interno naquele ponto. Você pode opcionalmente especificar um *rótulo de loop* em um loop que você pode então usar com `break` ou `continue` para especificar que essas palavras-chave se aplicam ao loop rotulado em vez do loop mais interno. Os rótulos de loop devem começar com uma única aspa. Aqui está um exemplo com dois loops aninhados:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-5-loop-labels/src/main.rs}}
```

O loop externo possui o rótulo `'counting_up` e contará de 0 a 2. O loop interno sem um rótulo conta de 10 a 9. O primeiro `break` que não especifica um rótulo sairá apenas do loop interno. A declaração `break 'counting_up;` sairá do loop externo. Este código imprimirá:

```console
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-5-loop-labels/output.txt}}
```

Um programa frequentemente precisa avaliar uma condição dentro de um loop. Enquanto a condição for `true`, o loop é executado. Quando a condição deixa de ser `true`, o programa chama `break`, interrompendo o loop. É possível implementar esse comportamento usando uma combinação de `loop`, `if`, `else` e `break`; você pode tentar fazer isso em um programa, se desejar. No entanto, esse padrão é tão comum que o Rust possui uma construção de linguagem incorporada para isso, chamada de loop `while`. No Exemplo 3-3, usamos `while` para executar o programa três vezes, contando para baixo a cada vez, e depois, após o loop, imprimir uma mensagem e sair.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-03/src/main.rs}}
```

<span class="caption">Exemplo 3-3: Usando um loop `while` para executar código enquanto uma
condição permanece verdadeira</span>

Essa construção elimina grande parte da aninhamento que seria necessária se você usasse
`loop`, `if`, `else` e `break`, e é mais clara. Enquanto uma condição
avalia para `true`, o código é executado; caso contrário, ele sai do loop.

#### Percorrendo uma Coleção com `for`

Você pode optar por usar a construção `while` para percorrer os elementos de uma
coleção, como um array. Por exemplo, o loop no Exemplo 3-4 imprime cada
elemento no array `a`.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-04/src/main.rs}}
```

<span class="caption">Exemplo 3-4: Percorrendo cada elemento de uma coleção
usando um loop `while`</span>

Aqui, o código conta os elementos no array. Ele começa no índice
`0` e depois faz um loop até atingir o último índice no array (ou seja, quando `index < 5` não é mais `true`). Executar este código imprimirá todos os elementos no array:

```console
{{#include ../listings/ch03-common-programming-concepts/listing-03-04/output.txt}}
```

Todos os cinco valores do array aparecem no terminal, como esperado. Mesmo que o `índice`
alcance um valor de `5` em algum momento, o loop para de executar antes de tentar
pegar um sexto valor do array.

No entanto, essa abordagem é propensa a erros; poderíamos fazer o programa entrar em pânico se
o valor do índice ou a condição de teste estiverem incorretos. Por exemplo, se você alterasse a
definição do array `a` para ter quatro elementos, mas esqueceu de atualizar a
condição para `while index < 4`, o código entraria em pânico. Além disso, é lento, porque
o compilador adiciona código em tempo de execução para realizar a verificação condicional de se o
índice está dentro dos limites do array em cada iteração do loop.

Como alternativa mais concisa, você pode usar um loop `for` e executar algum código
para cada item em uma coleção. Um loop `for` se parece com o código no Exemplo 3-5.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-05/src/main.rs}}
```

<span class="caption">Exemplo 3-5: Percorrendo cada elemento de uma coleção usando um loop `for`</span>

Quando executamos este código, veremos a mesma saída que no Exemplo 3-4. Mais
importante ainda, aumentamos a segurança do código e eliminamos a
possibilidade de erros que podem resultar em ultrapassar o final do array ou não
percorrer todos os itens.

Usando o loop `for`, você não precisaria se lembrar de alterar nenhum outro código se
mudasse o número de valores no array, como aconteceria com o método
usado no Exemplo 3-4.

A segurança e a concisão dos loops `for` os tornam a construção de loop mais comumente usada
em Rust. Mesmo em situações em que você deseja executar algum código um
certo número de vezes, como no exemplo de contagem regressiva que usava um loop `while`
no Exemplo 3-3, a maioria dos programadores Rust usaria um loop `for`. A maneira de fazer isso
seria usar um `Range`, fornecido pela biblioteca padrão, que gera
todos os números em sequência, começando por um número e terminando antes de outro
número.

Aqui está como a contagem regressiva ficaria usando um loop `for` e outro método
que ainda não discutimos, `rev`, para inverter a sequência:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-34-for-range/src/main.rs}}
```

Este código é um pouco melhor, não é?

## Summary

Você conseguiu! Este foi um capítulo considerável: você aprendeu sobre variáveis, tipos de dados escalares e compostos, funções, comentários, expressões `if` e loops! Para praticar os conceitos discutidos neste capítulo, tente criar programas para fazer o seguinte:

* Converter temperaturas entre Fahrenheit e Celsius.
* Gerar o *n*-ésimo número de Fibonacci.
* Imprimir a letra da canção de Natal "Os Doze Dias de Natal", aproveitando a repetição na música.

Quando estiver pronto para avançar, falaremos sobre um conceito no Rust que *não* existe comumente em outras linguagens de programação: propriedade (ownership).

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[quitting-after-a-correct-guess]:
ch02-00-guessing-game-tutorial.html#quitting-after-a-correct-guess
