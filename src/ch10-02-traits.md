## Traits: Definindo Comportamento Compartilhado

Um *trait* define a funcionalidade que um tipo específico possui e pode compartilhar com outros tipos. Podemos usar traits para definir comportamentos compartilhados de uma maneira abstrata. Podemos usar *limites de traits* para especificar que um tipo genérico pode ser qualquer tipo que tenha determinado comportamento.

> Nota: Traits são semelhantes a uma característica frequentemente chamada de *interfaces* em outras linguagens, embora com algumas diferenças.

### Definindo um Trait

O comportamento de um tipo consiste nos métodos que podemos chamar nesse tipo. Tipos diferentes compartilham o mesmo comportamento se pudermos chamar os mesmos métodos em todos esses tipos. As definições de traits são uma maneira de agrupar assinaturas de métodos para definir um conjunto de comportamentos necessários para realizar algum propósito.

Por exemplo, digamos que temos várias structs que armazenam vários tipos e quantidades de texto: uma struct `NewsArticle` que armazena uma notícia arquivada em um local específico e um `Tweet` que pode ter no máximo 280 caracteres, juntamente com metadados que indicam se é um novo tweet, um retweet ou uma resposta a outro tweet.

Queremos criar uma biblioteca de agregação de mídia chamada `aggregator` que pode exibir resumos de dados que podem ser armazenados em uma instância de `NewsArticle` ou `Tweet`. Para fazer isso, precisamos de um resumo de cada tipo, e solicitaremos esse resumo chamando um método `summarize` em uma instância. A Listagem 10-12 mostra a definição de um trait público `Summary` que expressa esse comportamento.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-12/src/lib.rs}}
```

<span class="caption">Listagem 10-12: Um trait `Summary` que consiste no comportamento fornecido por um método `summarize`.</span>

Aqui, declaramos um trait usando a palavra-chave `trait` e, em seguida, o nome do trait, que neste caso é `Summary`. Também declaramos o trait como `pub` para que as crates que dependem desta crate também possam fazer uso deste trait, como veremos em alguns exemplos. Dentro das chaves, declaramos as assinaturas dos métodos que descrevem os comportamentos dos tipos que implementam este trait, que neste caso é `fn summarize(&self) -> String`.

Após a assinatura do método, em vez de fornecer uma implementação dentro das chaves, usamos um ponto e vírgula. Cada tipo que implementa esse trait deve fornecer seu próprio comportamento personalizado para o corpo do método. O compilador irá garantir que qualquer tipo que tenha o trait `Summary` terá o método `summarize` definido com essa assinatura exatamente.

Um trait pode ter vários métodos em seu corpo: as assinaturas dos métodos são listadas uma por linha e cada linha termina com um ponto e vírgula.

### Implementando um Trait em um Tipo

Agora que definimos as assinaturas desejadas dos métodos do trait `Summary`, podemos implementá-lo nos tipos em nosso agregador de mídia. A Listagem 10-13 mostra uma implementação do trait `Summary` na struct `NewsArticle` que utiliza o título, o autor e o local para criar o valor de retorno de `summarize`. Para a struct `Tweet`, definimos `summarize` como o nome de usuário seguido do texto completo do tweet, assumindo que o conteúdo do tweet já está limitado a 280 caracteres.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-13/src/lib.rs:here}}
```

<span class="caption">Listagem 10-13: Implementando o trait `Summary` nos tipos `NewsArticle` e `Tweet`</span>

Implementar um trait em um tipo é semelhante à implementação de métodos regulares. A diferença é que, após `impl`, colocamos o nome do trait que queremos implementar, depois usamos a palavra-chave `for` e especificamos o nome do tipo para o qual queremos implementar o trait. Dentro do bloco `impl`, colocamos as assinaturas de método que a definição do trait possui. Em vez de adicionar um ponto e vírgula após cada assinatura, usamos chaves e preenchemos o corpo do método com o comportamento específico que desejamos que os métodos do trait tenham para o tipo específico.

Agora que a biblioteca implementou o trait `Summary` em `NewsArticle` e `Tweet`, os usuários da crate podem chamar os métodos do trait em instâncias de `NewsArticle` e `Tweet` da mesma forma que chamamos métodos regulares. A única diferença é que o usuário deve trazer o trait para o escopo, assim como os tipos. Aqui está um exemplo de como uma crate binária poderia usar nossa crate de biblioteca `aggregator`:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-01-calling-trait-method/src/main.rs}}
```

Este código imprime `1 novo tweet: horse_ebooks: é claro, como você provavelmente já sabe, as pessoas`.

Outras crates que dependem da crate `aggregator` também podem trazer o trait `Summary` para o escopo a fim de implementar o `Summary` em seus próprios tipos. Uma restrição a ser observada é que podemos implementar um trait em um tipo somente se pelo menos um dos trait ou o tipo for local à nossa crate. Por exemplo, podemos implementar traits da biblioteca padrão, como `Display`, em um tipo personalizado como `Tweet`, como parte da funcionalidade de nossa crate `aggregator`, porque o tipo `Tweet` é local à nossa crate `aggregator`. Também podemos implementar `Summary` em `Vec<T>` em nossa crate `aggregator`, porque o trait `Summary` é local à nossa crate `aggregator`.

Mas não podemos implementar traits externos em tipos externos. Por exemplo, não podemos implementar o trait `Display` em `Vec<T>` dentro de nossa crate `aggregator`, porque `Display` e `Vec<T>` são ambos definidos na biblioteca padrão e não são locais à nossa crate `aggregator`. Essa restrição faz parte de uma propriedade chamada *coerência*, e mais especificamente da *regra dos órfãos*, assim chamada porque o tipo pai não está presente. Essa regra garante que o código de outras pessoas não possa quebrar o seu código e vice-versa. Sem essa regra, duas crates poderiam implementar o mesmo trait para o mesmo tipo, e o Rust não saberia qual implementação usar.

### Implementações Padrão

Às vezes, é útil ter um comportamento padrão para alguns ou todos os métodos em um trait em vez de exigir implementações para todos os métodos em todos os tipos. Então, à medida que implementamos o trait em um tipo específico, podemos manter ou substituir o comportamento padrão de cada método.

Na Listagem 10-14, especificamos uma string padrão para o método `summarize` do trait `Summary` em vez de apenas definir a assinatura do método, como fizemos na Listagem 10-12.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-14/src/lib.rs:here}}
```

<span class="caption">Listagem 10-14: Definindo um trait `Summary` com uma implementação padrão do método `summarize`.</span>

Para usar uma implementação padrão para resumir instâncias de `NewsArticle`, especificamos um bloco `impl` vazio com `impl Summary for NewsArticle {}`.

Mesmo que não estejamos mais definindo o método `summarize` diretamente em `NewsArticle`, fornecemos uma implementação padrão e especificamos que `NewsArticle` implementa o trait `Summary`. Como resultado, ainda podemos chamar o método `summarize` em uma instância de `NewsArticle`, como neste exemplo:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-02-calling-default-impl/src/main.rs:here}}
```

Este código imprime `Novo artigo disponível! (Leia mais...)`.

Criar uma implementação padrão não exige que façamos alterações na implementação de `Summary` em `Tweet`, como na Listagem 10-13. A razão para isso é que a sintaxe para substituir uma implementação padrão é a mesma que a sintaxe para implementar um método de trait que não possui uma implementação padrão.

Implementações padrão podem chamar outros métodos no mesmo trait, mesmo que esses outros métodos não tenham uma implementação padrão. Dessa forma, um trait pode fornecer muita funcionalidade útil e apenas exigir dos implementadores que especifiquem uma pequena parte dela. Por exemplo, poderíamos definir o trait `Summary` para ter um método `summarize_author` cuja implementação é obrigatória, e então definir um método `summarize` que tenha uma implementação padrão que chama o método `summarize_author`:

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-03-default-impl-calls-other-methods/src/lib.rs:here}}
```

Para usar esta versão de `Summary`, só precisamos definir `summarize_author` quando implementamos o trait em um tipo:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-03-default-impl-calls-other-methods/src/lib.rs:impl}}
```

Após definirmos `summarize_author`, podemos chamar `summarize` em instâncias da struct `Tweet`, e a implementação padrão de `summarize` chamará a definição de `summarize_author` que fornecemos. Como implementamos `summarize_author`, o trait `Summary` nos deu o comportamento do método `summarize` sem exigir que escrevêssemos mais código.

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-03-default-impl-calls-other-methods/src/main.rs:here}}
```

Este código imprime `1 novo tweet: (Leia mais de @horse_ebooks...)`.

Observe que não é possível chamar a implementação padrão de uma implementação que está substituindo o mesmo método.

### Traits como Parâmetros

Agora que você sabe como definir e implementar traits, podemos explorar como usar
traits para definir funções que aceitam muitos tipos diferentes. Vamos usar o
trait `Summary` que implementamos nos tipos `NewsArticle` e `Tweet` em
Lista 10-13 para definir uma função `notify` que chama o método `summarize`
em seu parâmetro `item`, que é de algum tipo que implementa o trait `Summary`.
Para fazer isso, usamos a sintaxe `impl Trait`, assim:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-04-traits-as-parameters/src/lib.rs:here}}
```

Em vez de um tipo concreto para o parâmetro `item`, especificamos a palavra-chave `impl` e o nome do trait. Esse parâmetro aceita qualquer tipo que implemente o trait especificado. No corpo de `notify`, podemos chamar qualquer método em `item` que venha do trait `Summary`, como `summarize`. Podemos chamar `notify` e passar qualquer instância de `NewsArticle` ou `Tweet`. O código que chama a função com qualquer outro tipo, como uma `String` ou um `i32`, não irá compilar, porque esses tipos não implementam `Summary`.

<!-- Old headings. Do not remove or links may break. -->
<a id="fixing-the-largest-function-with-trait-bounds"></a>

#### Sintaxe de Limite de Trait

A sintaxe `impl Trait` funciona para casos simples, mas na verdade é um açúcar sintático para uma forma mais longa conhecida como um *limite de trait*; parece com isto:

```rust,ignore
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

Essa forma mais longa é equivalente ao exemplo da seção anterior, mas é mais detalhada. Colocamos os limites de trait com a declaração do parâmetro de tipo genérico após dois pontos e dentro de colchetes angulares.

A sintaxe `impl Trait` é conveniente e torna o código mais conciso em casos simples, enquanto a sintaxe completa de limite de trait pode expressar mais complexidade em outros casos. Por exemplo, podemos ter dois parâmetros que implementam `Summary`. Fazê-lo com a sintaxe `impl Trait` fica assim:

```rust,ignore
pub fn notify(item1: &impl Summary, item2: &impl Summary) {
```

O uso de `impl Trait` é apropriado se quisermos que essa função permita que `item1` e `item2` tenham tipos diferentes (desde que ambos os tipos implementem `Summary`). No entanto, se desejarmos forçar que ambos os parâmetros tenham o mesmo tipo, devemos usar um limite de trait, como este:

```rust,ignore
pub fn notify<T: Summary>(item1: &T, item2: &T) {
```

O tipo genérico `T` especificado como o tipo dos parâmetros `item1` e `item2` restringe a função de forma que o tipo concreto do valor passado como argumento para `item1` e `item2` deve ser o mesmo.

#### Especificando Múltiplos Limites de Trait com a Sintaxe `+`

Também podemos especificar mais de um limite de trait. Digamos que quiséssemos que `notify` usasse a formatação de exibição, bem como `summarize` em `item`: especificamos na definição de `notify` que `item` deve implementar tanto `Display` quanto `Summary`. Podemos fazer isso usando a sintaxe `+`:

```rust,ignore
pub fn notify(item: &(impl Summary + Display)) {
```

A sintaxe `+` também é válida com limites de trait em tipos genéricos:

```rust,ignore
pub fn notify<T: Summary + Display>(item: &T) {
```

Com os dois limites de trait especificados, o corpo de `notify` pode chamar `summarize` e usar `{}` para formatar `item`.

#### Limites de Trait mais Claros com Cláusulas `where`

Usar muitos limites de trait tem suas desvantagens. Cada tipo genérico tem seus próprios limites de trait, então funções com vários parâmetros de tipo genérico podem conter muitas informações de limite de trait entre o nome da função e sua lista de parâmetros, tornando a assinatura da função difícil de ler. Por esse motivo, o Rust possui uma sintaxe alternativa para especificar limites de trait dentro de uma cláusula `where` após a assinatura da função. Em vez de escrever isso:

```rust,ignore
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
```

podemos usar uma cláusula `where`, assim:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-07-where-clause/src/lib.rs:here}}
```

A assinatura desta função fica menos poluída: o nome da função, a lista de parâmetros e o tipo de retorno estão próximos, semelhante a uma função sem muitos limites de trait.

### Retornando Tipos que Implementam Traits

Também podemos usar a sintaxe `impl Trait` na posição de retorno para retornar um valor de algum tipo que implementa um trait, como mostrado aqui:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-05-returning-impl-trait/src/lib.rs:here}}
```

Ao usar `impl Summary` como tipo de retorno, estamos especificando que a função `returns_summarizable` retorna algum tipo que implementa o trait `Summary`, sem mencionar o tipo concreto. Neste caso, `returns_summarizable` retorna um `Tweet`, mas o código que chama essa função não precisa saber disso.

A capacidade de especificar um tipo de retorno apenas pelo trait que ele implementa é especialmente útil no contexto de closures e iterators, que são abordados no Capítulo 13. Closures e iterators criam tipos que apenas o compilador conhece ou tipos que são muito longos para serem especificados. A sintaxe `impl Trait` permite que você especifique de forma concisa que uma função retorna algum tipo que implementa o trait `Iterator` sem precisar escrever um tipo muito longo.

No entanto, você só pode usar `impl Trait` se estiver retornando um único tipo. Por exemplo, este código que retorna um `NewsArticle` ou um `Tweet` com o tipo de retorno especificado como `impl Summary` não funcionaria:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-06-impl-trait-returns-one-type/src/lib.rs:here}}
```

Retornar um `NewsArticle` ou um `Tweet` não é permitido devido às restrições em torno de como a sintaxe `impl Trait` é implementada no compilador. Abordaremos como escrever uma função com esse comportamento na seção [“Usando Objetos de Trait que Permitem Valores de Tipos Diferentes”][using-trait-objects-that-allow-for-values-of-different-types] do Capítulo 17.

### Usando Restrições de Trait para Implementar Métodos Condicionalmente

Ao utilizar uma restrição de trait com um bloco `impl` que utiliza parâmetros de tipo genérico,
podemos implementar métodos condicionalmente para tipos que implementam as traits especificadas.
Por exemplo, o tipo `Pair<T>` no Exemplo 10-15 sempre implementa a função `new` para retornar uma nova instância de `Pair<T>` (lembre-se da seção [“Definindo Métodos”][métodos]<!-- ignore --> do Capítulo 5 que `Self` é um alias de tipo para o tipo do bloco `impl`, que, neste caso, é `Pair<T>`). Mas no próximo bloco `impl`, `Pair<T>` implementa apenas o método `cmp_display` se seu tipo interno `T` implementa a trait `PartialOrd` que permite a comparação *e* a trait `Display` que permite a impressão.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-15/src/lib.rs}}
```

<span class="caption">Exemplo 10-15: Implementando métodos condicionalmente em um
tipo genérico com base nas restrições de trait</span>

Também podemos implementar uma trait condicionalmente para qualquer tipo que implemente
outra trait. Implementações de uma trait em qualquer tipo que satisfaça as restrições de trait
são chamadas *implementações abrangentes* e são amplamente usadas na
biblioteca padrão do Rust. Por exemplo, a biblioteca padrão implementa a
trait `ToString` em qualquer tipo que implemente a trait `Display`. O bloco `impl`
na biblioteca padrão se parece com o código a seguir:

```rust,ignore
impl<T: Display> ToString for T {
    // --snip--
}
```

Devido a essa implementação abrangente na biblioteca padrão, podemos chamar o
método `to_string` definido pela trait `ToString` em qualquer tipo que implemente
a trait `Display`. Por exemplo, podemos transformar inteiros em seus valores
correspondentes em `String` da seguinte maneira, porque os inteiros implementam `Display`:

```rust
let s = 3.to_string();
```

As implementações abrangentes aparecem na documentação da trait na
seção "Implementadores".

Traits e restrições de trait nos permitem escrever código que usa parâmetros de tipo genérico para
reduzir a duplicação, mas também especificar ao compilador que queremos que o tipo genérico tenha um comportamento específico. O compilador pode, então, usar as informações de restrição de trait para verificar se todos os tipos concretos usados com nosso código fornecem o comportamento correto. Em linguagens de tipagem dinâmica, receberíamos um erro em tempo de execução se chamássemos um método em um tipo que não o definisse. Mas o Rust move esses erros para o tempo de compilação, então somos obrigados a corrigir os problemas antes mesmo que nosso código possa ser executado. Além disso, não precisamos escrever código que verifica o comportamento em tempo de execução, porque já verificamos em tempo de compilação. Fazer isso melhora o desempenho sem precisar abrir mão da flexibilidade dos tipos genéricos.

[using-trait-objects-that-allow-for-values-of-different-types]: ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types
[methods]: ch05-03-method-syntax.html#defining-methods
