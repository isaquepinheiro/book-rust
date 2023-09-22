## Trazendo Caminhos para o Escopo com a Palavra-chave `use`

Ter que escrever os caminhos para chamar funções pode parecer inconveniente e repetitivo. No Exemplo 7-7, independentemente de escolhermos o caminho absoluto ou relativo para a função `add_to_waitlist`, toda vez que quiséssemos chamar `add_to_waitlist`, tínhamos que especificar `front_of_house` e `hosting` também. Felizmente, há uma maneira de simplificar esse processo: podemos criar um atalho para um caminho com a palavra-chave `use` uma vez e, em seguida, usar o nome mais curto em todos os outros lugares no escopo.

No Exemplo 7-11, trazemos o módulo `crate::front_of_house::hosting` para o escopo da função `eat_at_restaurant`, para que só precisemos especificar `hosting::add_to_waitlist` para chamar a função `add_to_waitlist` em `eat_at_restaurant`.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-11/src/lib.rs}}
```

<span class="caption">Exemplo 7-11: Trazendo um módulo para o escopo com `use`</span>

Adicionar `use` e um caminho em um escopo é semelhante a criar um link simbólico no sistema de arquivos. Ao adicionar `use crate::front_of_house::hosting` na raiz da caixa, `hosting` agora é um nome válido nesse escopo, assim como se o módulo `hosting` tivesse sido definido na raiz da caixa. Os caminhos trazidos para o escopo com `use` também verificam a privacidade, assim como qualquer outro caminho.

Observe que `use` cria apenas o atalho para o escopo específico em que o `use` ocorre. O Exemplo 7-12 move a função `eat_at_restaurant` para um novo módulo filho chamado `customer`, que é então um escopo diferente do `use` statement, portanto o corpo da função não irá compilar:

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground,test_harness,does_not_compile,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-12/src/lib.rs}}
```

<span class="caption">Exemplo 7-12: Uma declaração `use` se aplica apenas no escopo em que está</span>

O erro do compilador mostra que o atalho não se aplica mais ao módulo `customer`:

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-12/output.txt}}
```

Observe também um aviso de que o `use` não é mais usado em seu escopo! Para corrigir esse problema, mova o `use` para dentro do módulo `customer` também, ou faça referência ao atalho no módulo pai com `super::hosting` dentro do módulo filho `customer`.

### Criando Caminhos `use` Idiomáticos

No exemplo 7-11, você pode ter se perguntado por que especificamos `use crate::front_of_house::hosting` e então chamamos `hosting::add_to_waitlist` em `eat_at_restaurant` em vez de especificar o caminho `use` até a função `add_to_waitlist` para alcançar o mesmo resultado, como no exemplo 7-13.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-13/src/lib.rs}}
```

<span class="caption">Exemplo 7-13: Tragendo a função `add_to_waitlist` para o escopo com `use`, o que é não idiomático</span>

Embora os Exemplos 7-11 e 7-13 realizem a mesma tarefa, o Exemplo 7-11 é a maneira idiomática de trazer uma função para o escopo com `use`. Trazer o módulo pai da função para o escopo com `use` significa que precisamos especificar o módulo pai ao chamar a função. Especificar o módulo pai ao chamar a função torna claro que a função não está definida localmente, mas ainda minimiza a repetição do caminho completo. O código no Exemplo 7-13 é confuso quanto aonde `add_to_waitlist` é definida.

Por outro lado, ao trazer structs, enums e outros itens com `use`, é idiomático especificar o caminho completo. O Exemplo 7-14 mostra a maneira idiomática de trazer a struct `HashMap` da biblioteca padrão para o escopo de uma crate binária.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-14/src/main.rs}}
```

<span class="caption">Exemplo 7-14: Tragendo `HashMap` para o escopo de maneira idiomática</span>

Não há uma razão forte por trás desse idiom: é apenas a convenção que surgiu, e as pessoas se acostumaram a ler e escrever código Rust dessa maneira.

A exceção a esse idiom é quando estamos trazendo dois itens com o mesmo nome para o escopo com declarações `use`, porque o Rust não permite isso. O Exemplo 7-15 mostra como trazer dois tipos `Result` para o escopo que têm o mesmo nome, mas módulos pais diferentes, e como fazer referência a eles.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-15/src/lib.rs:here}}
```

<span class="caption">Exemplo 7-15: Trazer dois tipos com o mesmo nome para o mesmo escopo requer o uso de seus módulos pais.</span>

Como você pode ver, usar os módulos pais distingue os dois tipos `Result`. Se, em vez disso, especificássemos `use std::fmt::Result` e `use std::io::Result`, teríamos dois tipos `Result` no mesmo escopo e o Rust não saberia qual deles queríamos quando usássemos `Result`.

### Fornecendo Novos Nomes com a Palavra-chave `as`

Há outra solução para o problema de trazer dois tipos com o mesmo nome para o mesmo escopo com `use`: após o caminho, podemos especificar `as` e um novo nome local, ou *alias*, para o tipo. O Exemplo 7-16 mostra outra maneira de escrever o código do Exemplo 7-15, renomeando um dos dois tipos `Result` usando `as`.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-16/src/lib.rs:here}}
```

<span class="caption">Exemplo 7-16: Renomeando um tipo ao trazê-lo para o escopo com a palavra-chave `as`</span>

No segundo `use`, escolhemos o novo nome `IoResult` para o tipo `std::io::Result`, o que não entrará em conflito com o `Result` de `std::fmt` que também trouxemos para o escopo. O Exemplo 7-15 e o Exemplo 7-16 são considerados idiomáticos, então a escolha fica a seu critério!

### Re-exportando Nomes com `pub use`

Quando trazemos um nome para o escopo com a palavra-chave `use`, o nome disponível no novo escopo é privado. Para permitir que o código que chama nosso código se refira a esse nome como se tivesse sido definido no escopo desse código, podemos combinar `pub` e `use`. Essa técnica é chamada de *re-exportação* porque estamos trazendo um item para o escopo, mas também tornando esse item disponível para que outras pessoas o tragam para o escopo delas.

O Exemplo 7-17 mostra o código do Exemplo 7-11 com `use` no módulo raiz alterado para `pub use`.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-17/src/lib.rs}}
```

<span class="caption">Exemplo 7-17: Tornando um nome disponível para qualquer código usar a partir de um novo escopo com `pub use`</span>

Antes dessa mudança, o código externo teria que chamar a função `add_to_waitlist` usando o caminho `restaurant::front_of_house::hosting::add_to_waitlist()`. Agora que esse `pub use` reexportou o módulo `hosting` do módulo raiz, o código externo pode usar o caminho `restaurant::hosting::add_to_waitlist()` em vez disso.

A reexportação é útil quando a estrutura interna do seu código é diferente da forma como os programadores que chamam o seu código pensariam sobre o domínio. Por exemplo, nesta metáfora de restaurante, as pessoas que administram o restaurante pensam em "front of house" e "back of house". Mas os clientes que visitam um restaurante provavelmente não pensarão nas partes do restaurante com esses termos. Com `pub use`, podemos escrever nosso código com uma estrutura, mas expor uma estrutura diferente. Fazê-lo organiza bem nossa biblioteca para programadores que trabalham na biblioteca e programadores que a chamam. Veremos outro exemplo de `pub use` e como ele afeta a documentação de sua crate na seção [“Exportando uma API Pública Conveniente com `pub use`”][ch14-pub-use] do Capítulo 14.

### Usando Pacotes Externos

No Capítulo 2, programamos um projeto de jogo de adivinhação que usava um pacote externo chamado `rand` para obter números aleatórios. Para usar o `rand` em nosso projeto, adicionamos esta linha ao *Cargo.toml*:

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:
* ch02-00-guessing-game-tutorial.md
* ch14-03-cargo-workspaces.md
-->

<span class="filename">Filename: Cargo.toml</span>

```toml
{{#include ../listings/ch02-guessing-game-tutorial/listing-02-02/Cargo.toml:9:}}
```

Adicionar `rand` como uma dependência em *Cargo.toml* diz ao Cargo para baixar o pacote `rand` e quaisquer dependências do [crates.io](https://crates.io/) e tornar o `rand` disponível para nosso projeto.

Em seguida, para trazer as definições do `rand` para o escopo de nosso pacote, adicionamos uma linha `use` começando com o nome da crate, `rand`, e listamos os itens que queríamos trazer para o escopo. Lembre-se de que na seção [“Generating a Random Number”][rand]<!-- ignore --> do Capítulo 2, trouxemos o trait `Rng` para o escopo e chamamos a função `rand::thread_rng`:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-03/src/main.rs:ch07-04}}
```

Membros da comunidade Rust disponibilizaram muitos pacotes em [crates.io](https://crates.io/), e incorporar qualquer um deles em seu pacote envolve essas mesmas etapas: listá-los no arquivo *Cargo.toml* do seu pacote e usar `use` para trazer itens de suas crates para o escopo.

Observe que a biblioteca padrão `std` também é uma crate que está fora do nosso pacote. Como a biblioteca padrão é fornecida com a linguagem Rust, não precisamos alterar o *Cargo.toml* para incluir `std`. Mas precisamos nos referir a ela com `use` para trazer itens de lá para o escopo de nosso pacote. Por exemplo, com `HashMap`, usaríamos esta linha:

```rust
use std::collections::HashMap;
```

Este é um caminho absoluto que começa com `std`, o nome da crate da biblioteca padrão.

### Usando Caminhos Aninhados para Limpar Listas de `use` Grandes

Se estamos usando vários itens definidos na mesma crate ou no mesmo módulo, listar cada item em sua própria linha pode ocupar muito espaço vertical em nossos arquivos. Por exemplo, essas duas declarações `use` que tivemos no Jogo de Adivinhação no Exemplo 2-4 trazem itens de `std` para o escopo:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/no-listing-01-use-std-unnested/src/main.rs:here}}
```

Em vez disso, podemos usar caminhos aninhados para trazer os mesmos itens para o escopo em uma única linha. Fazemos isso especificando a parte comum do caminho, seguida por dois dois pontos e, em seguida, chaves em torno de uma lista das partes dos caminhos que diferem, conforme mostrado no Exemplo 7-18.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-18/src/main.rs:here}}
```

<span class="caption">Exemplo 7-18: Especificando um caminho aninhado para trazer vários itens com o mesmo prefixo para o escopo</span>

Em programas maiores, trazer muitos itens para o escopo da mesma crate ou módulo usando caminhos aninhados pode reduzir significativamente o número de declarações `use` separadas necessárias!

Podemos usar um caminho aninhado em qualquer nível de um caminho, o que é útil ao combinar duas declarações `use` que compartilham um subcaminho. Por exemplo, o Exemplo 7-19 mostra duas declarações `use`: uma que traz `std::io` para o escopo e outra que traz `std::io::Write` para o escopo.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-19/src/lib.rs}}
```

<span class="caption">Listing 7-19: Two `use` statements where one is a subpath
of the other</span>

A parte comum desses dois caminhos é `std::io`, e esse é o primeiro caminho completo. Para mesclar esses dois caminhos em uma única declaração `use`, podemos usar `self` no caminho aninhado, conforme mostrado no Exemplo 7-20.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-20/src/lib.rs}}
```

<span class="caption">Exemplo 7-20: Combinando os caminhos do Exemplo 7-19 em uma única declaração `use`</span>

Esta linha traz `std::io` e `std::io::Write` para o escopo.

### O Operador Glob

Se quisermos trazer *todos* os itens públicos definidos em um caminho para o escopo, podemos especificar esse caminho seguido pelo operador `*`.

```rust
use std::collections::*;
```

Essa declaração `use` traz todos os itens públicos definidos em `std::collections` para o escopo atual. Tenha cuidado ao usar o operador glob! O uso do glob pode tornar mais difícil identificar quais nomes estão no escopo e de onde um nome usado em seu programa foi definido.

O operador glob é frequentemente usado ao testar para trazer tudo sob teste para o módulo `tests`; falaremos sobre isso na seção [“Como Escrever Testes”][writing-tests]<!-- ignore --> no Capítulo 11. O operador glob também é às vezes usado como parte do padrão de prelude: consulte [a documentação da biblioteca padrão](../std/prelude/index.html#other-preludes)<!-- ignore --> para obter mais informações sobre esse padrão.

[ch14-pub-use]: ch14-02-publishing-to-crates-io.html#exporting-a-convenient-public-api-with-pub-use
[rand]: ch02-00-guessing-game-tutorial.html#generating-a-random-number
[writing-tests]: ch11-01-writing-tests.html#how-to-write-tests
