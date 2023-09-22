<!-- Old heading. Do not remove or links may break. -->
<a id="the-match-control-flow-operator"></a>
## O Construto de Controle `match`

Rust possui um construto de controle extremamente poderoso chamado `match` que permite comparar um valor com uma série de padrões e executar código com base em qual padrão corresponde. Os padrões podem ser compostos por valores literais, nomes de variáveis, curingas e muitas outras coisas; [Capítulo 18][ch18-00-patterns]<!-- ignore --> cobre todos os diferentes tipos de padrões e o que eles fazem. O poder do `match` advém da expressividade dos padrões e do fato de que o compilador confirma que todos os casos possíveis são tratados.

Pense em uma expressão `match` como sendo semelhante a uma máquina de classificação de moedas: moedas deslizam por uma pista com furos de tamanhos variados ao longo dela, e cada moeda cai pelo primeiro furo que encontra e se encaixa. Da mesma forma, os valores passam por cada padrão em um `match`, e no primeiro padrão em que o valor "encaixa", o valor cai no bloco de código associado para ser usado durante a execução.

Falando em moedas, vamos usá-las como exemplo com o `match`! Podemos escrever uma função que recebe uma moeda dos EUA desconhecida e, de maneira semelhante à máquina de contagem, determina qual moeda é e retorna seu valor em centavos, como mostrado no Exemplo 6-3.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-03/src/main.rs:here}}
```

<span class="caption">Exemplo 6-3: Um enum e uma expressão `match` que possui
as variantes do enum como seus padrões</span>

Vamos analisar o `match` na função `value_in_cents`. Primeiro, listamos a palavra-chave `match`, seguida de uma expressão, que neste caso é o valor `coin`. Isso parece muito semelhante a uma expressão condicional usada com `if`, mas há uma grande diferença: com `if`, a condição precisa avaliar para um valor booleano, mas aqui pode ser de qualquer tipo. O tipo de `coin` neste exemplo é o enum `Coin` que definimos na primeira linha.

Em seguida, temos os "braços" do `match`. Um braço tem duas partes: um padrão e algum código. O primeiro braço aqui tem um padrão que é o valor `Coin::Penny` e, em seguida, o operador `=>` que separa o padrão e o código a ser executado. O código neste caso é apenas o valor `1`. Cada braço é separado do próximo por uma vírgula.

Quando a expressão `match` é executada, ela compara o valor resultante com o padrão de cada braço, na ordem. Se um padrão corresponde ao valor, o código associado a esse padrão é executado. Se esse padrão não corresponder ao valor, a execução continua para o próximo braço, assim como em uma máquina de classificação de moedas. Podemos ter quantos braços forem necessários: em Listagem 6-3, nosso `match` tem quatro braços.

O código associado a cada braço é uma expressão, e o valor resultante da expressão no braço correspondente é o valor que é retornado para toda a expressão `match`.

Normalmente, não usamos chaves se o código do braço do `match` for curto, como é o caso em Listagem 6-3, onde cada braço apenas retorna um valor. Se você quiser executar várias linhas de código em um braço do `match`, deve usar chaves, e a vírgula após o braço se torna opcional. Por exemplo, o código a seguir imprime "Lucky penny!" toda vez que o método é chamado com um `Coin::Penny`, mas ainda retorna o último valor do bloco, `1`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-08-match-arm-multiple-lines/src/main.rs:here}}
```

### Padrões que vinculam a valores

Outra característica útil dos braços do `match` é que eles podem se vincular às partes dos valores que correspondem ao padrão. É assim que podemos extrair valores das variantes de um enum.

Como exemplo, vamos alterar uma das variantes do nosso enum para conter dados em seu interior. De 1999 a 2008, os Estados Unidos cunharam moedas de 25 centavos com designs diferentes para cada um dos 50 estados em um dos lados. Nenhuma outra moeda tinha designs de estados, então apenas as moedas de 25 centavos têm esse valor adicional. Podemos adicionar essa informação ao nosso `enum` alterando a variante `Quarter` para incluir um valor `UsState` armazenado dentro dela, como fizemos na Listagem 6-4.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-04/src/main.rs:here}}
```

<span class="caption">Listagem 6-4: Um enum `Coin` no qual a variante `Quarter`
também contém um valor `UsState`</span>

Vamos imaginar que um amigo está tentando colecionar todas as 50 moedas estaduais. Enquanto organizamos nosso troco solto por tipo de moeda, também chamaremos o nome do estado associado a cada moeda de 25 centavos, para que, se for um estado que nosso amigo não possui, ele possa adicioná-lo à sua coleção.

Na expressão `match` para este código, adicionamos uma variável chamada `state` ao padrão que corresponde aos valores da variante `Coin::Quarter`. Quando um `Coin::Quarter` corresponde, a variável `state` será vinculada ao valor do estado daquela moeda de 25 centavos. Em seguida, podemos usar `state` no código para aquele braço, da seguinte forma:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-09-variable-in-pattern/src/main.rs:here}}
```

Se chamássemos `value_in_cents(Coin::Quarter(UsState::Alaska))`, `coin` seria igual a `Coin::Quarter(UsState::Alaska)`. Quando comparamos esse valor com cada um dos braços do `match`, nenhum deles corresponde até chegarmos a `Coin::Quarter(state)`. Nesse ponto, a vinculação para `state` será o valor `UsState::Alaska`. Podemos então usar essa vinculação na expressão `println!`, obtendo assim o valor interno do estado da variante `Coin` para `Quarter`.

### Fazendo correspondência com `Option<T>`

Na seção anterior, queríamos obter o valor interno `T` do caso `Some` ao usar `Option<T>`; também podemos lidar com `Option<T>` usando `match`, assim como fizemos com o enum `Coin`! Em vez de comparar moedas, vamos comparar as variantes de `Option<T>`, mas a forma como a expressão `match` funciona permanece a mesma.

Digamos que desejamos escrever uma função que recebe um `Option<i32>` e, se houver um valor dentro, adiciona 1 a esse valor. Se não houver um valor dentro, a função deve retornar o valor `None` e não tentar realizar nenhuma operação.

Esta função é muito fácil de escrever, graças ao `match`, e ficará assim, conforme a Listagem 6-5.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:here}}
```

<span class="caption">Listagem 6-5: Uma função que utiliza uma expressão `match` em um `Option`<i32>`</span>

Vamos examinar a primeira execução de `plus_one` com mais detalhes. Quando chamamos `plus_one(cinco)`, a variável `x` no corpo de `plus_one` terá o valor `Some(5)`. Em seguida, comparamos isso com cada ramo da expressão `match`:

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

O valor `Some(5)` não corresponde ao padrão `None`, então continuamos para o próximo ramo:

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:second_arm}}
```

`Some(5)` corresponde a `Some(i)`? Sim! Temos a mesma variante. O `i` está vinculado ao valor contido em `Some`, então `i` assume o valor `5`. O código no braço correspondente ao `match` é então executado, e adicionamos 1 ao valor de `i`, criando um novo valor `Some` com nosso total `6` dentro.

Agora, consideremos a segunda chamada de `plus_one` na Listagem 6-5, onde `x` é `None`. Entramos no `match` e comparamos com o primeiro braço:

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

Corresponde! Não há valor para adicionar, então o programa para e retorna o valor `None` no lado direito de `=>`. Como o primeiro braço correspondeu, nenhum outro braço é comparado.

A combinação de `match` e enums é útil em muitas situações. Você verá esse padrão com frequência no código Rust: fazer `match` com um enum, vincular uma variável aos dados internos e, em seguida, executar código com base nisso. Pode ser um pouco complicado no início, mas uma vez que você se acostuma, vai desejar tê-lo em todas as linguagens. É consistentemente um favorito entre os usuários.

### Correspondências são Exaustivas

Há outro aspecto do `match` que precisamos discutir: os padrões dos braços devem cobrir todas as possibilidades. Considere esta versão de nossa função `plus_one`, que tem um erro e não será compilada:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/src/main.rs:here}}
```

Não tratamos o caso `None`, portanto, este código causará um erro. Felizmente, é um erro que o Rust sabe como identificar. Se tentarmos compilar este código, receberemos o seguinte erro:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/output.txt}}
```

O Rust sabe que não cobrimos todos os casos possíveis e até sabe qual padrão esquecemos! As correspondências no Rust são *exaustivas*: precisamos esgotar todas as possibilidades para que o código seja válido. Especialmente no caso de `Option<T>`, quando o Rust nos impede de esquecer de lidar explicitamente com o caso `None`, ele nos protege de assumir que temos um valor quando podemos ter nulo, tornando assim impossível o erro de bilhões de dólares discutido anteriormente.

### Padrões de captura-tudo e o espaço reservado `_`

Usando enums, também podemos realizar ações especiais para alguns valores específicos, mas para todos os outros valores, tomar uma ação padrão. Imagine que estamos implementando um jogo em que, se você tirar um 3 em um lançamento de dados, seu jogador não se move, mas ganha um novo chapéu chique. Se você tirar um 7, seu jogador perde um chapéu chique. Para todos os outros valores, seu jogador se move na quantidade de espaços indicada no tabuleiro do jogo. Aqui está um exemplo de `match` que implementa essa lógica, com o resultado do lançamento de dados codificado diretamente em vez de um valor aleatório, e toda a lógica adicional representada por funções sem corpos porque a implementação real está fora do escopo deste exemplo:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-15-binding-catchall/src/main.rs:here}}
```

Para os dois primeiros braços, os padrões são os valores literais `3` e `7`. Para o último braço, que cobre todos os outros valores possíveis, o padrão é a variável que escolhemos nomear como `other`. O código que é executado para o braço `other` utiliza a variável passando-a para a função `move_player`.

Este código compila, mesmo que não tenhamos listado todos os possíveis valores que um `u8` pode ter, porque o último padrão corresponderá a todos os valores não listados especificamente. Este padrão de captura-tudo atende ao requisito de que o `match` deve ser exaustivo. Note que precisamos colocar o braço de captura-tudo por último, porque os padrões são avaliados na ordem. Se colocássemos o braço de captura-tudo mais cedo, os outros braços nunca seriam executados, então o Rust nos avisará se adicionarmos braços após um de captura-tudo!

O Rust também tem um padrão que podemos usar quando queremos um captura-tudo, mas não queremos *usar* o valor no padrão de captura-tudo: `_` é um padrão especial que corresponde a qualquer valor e não faz ligação com esse valor. Isso informa ao Rust que não vamos usar o valor, então o Rust não nos avisará sobre uma variável não utilizada.

Vamos mudar as regras do jogo: agora, se você tirar qualquer coisa que não seja um 3 ou um 7, você deve jogar novamente. Não precisamos mais usar o valor de captura-tudo, então podemos alterar nosso código para usar `_` em vez da variável chamada `other`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-16-underscore-catchall/src/main.rs:here}}
```

Este exemplo também atende ao requisito de exaustividade porque estamos explicitamente ignorando todos os outros valores no último braço; não esquecemos de nada.

Por fim, vamos mudar as regras do jogo mais uma vez para que nada aconteça na sua vez se você tirar qualquer coisa que não seja um 3 ou um 7. Podemos expressar isso usando o valor unitário (o tipo de tupla vazia que mencionamos na seção ["O Tipo de Tupla"][tuples]) como o código que acompanha o braço `_`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-17-underscore-unit/src/main.rs:here}}
```

Aqui, estamos dizendo explicitamente ao Rust que não vamos usar nenhum outro valor que não corresponda a um padrão em um braço anterior, e não queremos executar nenhum código nesse caso.

Há mais sobre padrões e correspondências que cobriremos no [Capítulo 18][ch18-00-patterns]. Por enquanto, vamos avançar para a sintaxe `if let`, que pode ser útil em situações em que a expressão `match` parece um pouco verbosa.

[tuples]: ch03-02-data-types.html#the-tuple-type
[ch18-00-patterns]: ch18-00-patterns.html
