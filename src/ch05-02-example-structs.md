## Um Exemplo de Programa Usando Structs

Para entender quando devemos usar structs, vamos escrever um programa que calcula a área de um retângulo. Começaremos usando variáveis simples e, em seguida, refatoraremos o programa até estarmos usando structs.

Vamos criar um novo projeto binário com o Cargo chamado *rectangles*, que receberá a largura e a altura de um retângulo especificadas em pixels e calculará a área do retângulo. O Código 5-8 mostra um programa curto com uma maneira de fazer exatamente isso em nosso arquivo *src/main.rs* do projeto.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/src/main.rs:all}}
```

<span class="caption">Código 5-8: Calculando a área de um retângulo especificado por variáveis separadas de largura e altura.</span>

Agora, execute este programa usando `cargo run`:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/output.txt}}
```

Este código consegue calcular a área do retângulo chamando a função `area` com cada dimensão, mas podemos fazer mais para tornar este código claro e legível.

O problema com este código é evidente na assinatura da função `area`:

```rust,ignore
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/src/main.rs:here}}
```

A função `area` deve calcular a área de um retângulo, mas a função que escrevemos tem dois parâmetros, e não fica claro em nenhum lugar do nosso programa que esses parâmetros estão relacionados. Seria mais legível e mais fácil de gerenciar agrupar largura e altura juntas. Já discutimos uma maneira de fazer isso na seção ["O Tipo Tupla"][the-tuple-type] do Capítulo 3: usando tuplas.

### Refatoração com Tuplas

O Código 5-9 mostra outra versão do nosso programa que utiliza tuplas.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-09/src/main.rs}}
```

<span class="caption">Código 5-9: Especificando a largura e a altura do retângulo com uma tupla.</span>

De certa forma, este programa está melhor. As tuplas nos permitem adicionar um pouco de estrutura, e agora estamos passando apenas um argumento. No entanto, de outra forma, esta versão é menos clara: as tuplas não nomeiam seus elementos, então precisamos indexar as partes da tupla, tornando nosso cálculo menos óbvio.

Misturar a largura e a altura não importaria para o cálculo da área, mas se quiséssemos desenhar o retângulo na tela, isso importaria! Teríamos que lembrar que a `width` é o índice `0` da tupla e a `height` é o índice `1` da tupla. Isso seria ainda mais difícil para alguém entender e lembrar se eles fossem usar nosso código. Porque não transmitimos o significado de nossos dados em nosso código, agora é mais fácil introduzir erros.

### Refatoração com Structs: Adicionando Mais Significado

Usamos structs para adicionar significado, dando rótulos aos dados. Podemos transformar a tupla que estamos usando em uma struct com um nome para o todo, bem como nomes para as partes, como mostrado no Código 5-10.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-10/src/main.rs}}
```

<span class="caption">Código 5-10: Definindo uma struct `Rectangle`</span>

Aqui definimos uma struct e a nomeamos como `Rectangle`. Dentro das chaves, definimos os campos como `width` e `height`, ambos com o tipo `u32`. Em seguida, no `main`, criamos uma instância específica de `Rectangle` com uma largura de `30` e uma altura de `50`.

Nossa função `area` agora está definida com um parâmetro, que nomeamos como `rectangle`, cujo tipo é um empréstimo imutável de uma instância da struct `Rectangle`. Como mencionado no Capítulo 4, queremos emprestar a struct em vez de tomar posse dela. Dessa forma, o `main` retém a propriedade e pode continuar usando `rect1`, que é a razão pela qual usamos o `&` na assinatura da função e onde chamamos a função.

A função `area` acessa os campos `width` e `height` da instância de `Rectangle` (observe que acessar campos de uma instância de struct emprestada não move os valores dos campos, o que é por isso que frequentemente vemos empréstimos de structs). Nossa assinatura de função para `area` agora diz exatamente o que queremos: calcular a área de um `Rectangle`, usando seus campos `width` e `height`. Isso transmite que a largura e a altura estão relacionadas entre si e dá nomes descritivos aos valores em vez de usar os índices de tupla `0` e `1`. Isso melhora a clareza do código.

### Adicionando Funcionalidade Útil com Traits Derivados

Seria útil ser capaz de imprimir uma instância de `Rectangle` enquanto depuramos nosso programa e ver os valores de todos os seus campos. O Código 5-11 tenta usar a [macro `println!`][println], como fizemos em capítulos anteriores. No entanto, isso não funcionará.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/src/main.rs}}
```

<span class="caption">Código 5-11: Tentativa de imprimir uma instância de `Rectangle`.</span>

Quando compilamos este código, recebemos um erro com a seguinte mensagem central:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/output.txt:3}}
```

A macro `println!` pode fazer vários tipos de formatação e, por padrão, as chaves dizem ao `println!` para usar a formatação conhecida como `Display`: saída destinada ao consumo direto do usuário final. Os tipos primitivos que vimos até agora implementam `Display` por padrão porque só existe uma maneira de mostrar um `1` ou qualquer outro tipo primitivo para um usuário. Mas com structs, a maneira como o `println!` deve formatar a saída é menos clara porque existem mais possibilidades de exibição: você deseja vírgulas ou não? Você deseja imprimir as chaves? Todos os campos devem ser mostrados? Devido a essa ambiguidade, o Rust não tenta adivinhar o que queremos, e as structs não têm uma implementação fornecida de `Display` para usar com `println!` e o marcador `{}`.

Se continuarmos lendo os erros, encontraremos esta nota útil:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/output.txt:9:10}}
```

Vamos tentar! A chamada da macro `println!` agora ficará assim: `println!("rect1 is {:?}", rect1);`. Colocar o especificador `:?` dentro das chaves informa ao `println!` que queremos usar um formato de saída chamado `Debug`. O trait `Debug` nos permite imprimir nossa struct de uma maneira útil para os desenvolvedores, para que possamos ver seu valor enquanto depuramos nosso código.

Compile o código com essa alteração. Caramba! Ainda recebemos um erro:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-01-debug/output.txt:3}}
```

Mas novamente, o compilador nos fornece uma nota útil:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-01-debug/output.txt:9:10}}
```

Rust *inclui* funcionalidade para imprimir informações de depuração, mas temos que optar explicitamente por disponibilizar essa funcionalidade para nossa struct. Para fazer isso, adicionamos o atributo externo `#[derive(Debug)]` logo antes da definição da struct, como mostrado no Código 5-12.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-12/src/main.rs}}
```

<span class="caption">Código 5-12: Adicionando o atributo para derivar o trait `Debug` e imprimir a instância de `Rectangle` usando formatação de depuração.</span>

Agora, quando executarmos o programa, não receberemos erros e veremos a seguinte saída:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-12/output.txt}}
```

Legal! Não é a saída mais bonita, mas mostra os valores de todos os campos para esta instância, o que definitivamente ajudaria durante a depuração. Quando temos structs maiores, é útil ter uma saída um pouco mais fácil de ler; nesses casos, podemos usar `{:#?}` em vez de `{:?}` na string `println!`. Neste exemplo, usar o estilo `{:#?}` produzirá o seguinte resultado:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-02-pretty-debug/output.txt}}
```

Outra maneira de imprimir um valor usando o formato `Debug` é usar a [macro `dbg!`][dbg], que assume a propriedade de uma expressão (ao contrário de `println!`, que assume uma referência), imprime o arquivo e o número da linha onde a chamada da macro `dbg!` ocorre em seu código, juntamente com o valor resultante dessa expressão, e retorna a propriedade do valor.

> Nota: Chamar a macro `dbg!` imprime na saída de console padrão de erro (`stderr`), em oposição a `println!`, que imprime na saída de console padrão (`stdout`). Falaremos mais sobre `stderr` e `stdout` na [seção "Escrevendo Mensagens de Erro na Saída de Erro em Vez da Saída Padrão" no Capítulo 12][err].

Aqui está um exemplo em que estamos interessados no valor atribuído ao campo `width`, bem como no valor da struct inteira em `rect1`:

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-05-dbg-macro/src/main.rs}}
```

Podemos colocar `dbg!` em torno da expressão `30 * scale` e, como `dbg!` retorna a propriedade do valor da expressão, o campo `width` obterá o mesmo valor como se não tivéssemos a chamada `dbg!` lá. Não queremos que `dbg!` assuma a propriedade de `rect1`, então usamos uma referência a `rect1` na chamada seguinte. Veja como fica a saída deste exemplo:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/no-listing-05-dbg-macro/output.txt}}
```

Podemos ver que a primeira parte da saída veio da linha 10 de *src/main.rs*, onde estamos depurando a expressão `30 * scale`, e seu valor resultante é `60` (a formatação `Debug` implementada para inteiros é imprimir apenas o seu valor). A chamada `dbg!` na linha 14 de *src/main.rs* imprime o valor de `&rect1`, que é a struct `Rectangle`. Esta saída utiliza a formatação `Debug` bonita do tipo `Rectangle`. A macro `dbg!` pode ser realmente útil quando você está tentando entender o que seu código está fazendo!

Além do trait `Debug`, o Rust forneceu vários traits para usarmos com o atributo `derive` que podem adicionar comportamento útil aos nossos tipos personalizados. Esses traits e seus comportamentos estão listados em [Apêndice C][app-c]. Abordaremos como implementar esses traits com comportamento personalizado, bem como como criar seus próprios traits no Capítulo 10. Existem também muitos atributos além de `derive`; para mais informações, consulte [a seção "Atributos" no Guia de Referência do Rust][attributes].

Nossa função `area` é muito específica: ela calcula apenas a área de retângulos. Seria útil vincular esse comportamento de forma mais próxima à nossa struct `Rectangle`, pois ela não funcionará com nenhum outro tipo. Vamos ver como podemos continuar a refatorar este código transformando a função `area` em um *método* `area` definido em nosso tipo `Rectangle`.

[the-tuple-type]: ch03-02-data-types.html#the-tuple-type
[app-c]: appendix-03-derivable-traits.md
[println]: ../std/macro.println.html
[dbg]: ../std/macro.dbg.html
[err]: ch12-06-writing-to-stderr-instead-of-stdout.html
[attributes]: ../reference/attributes.html
