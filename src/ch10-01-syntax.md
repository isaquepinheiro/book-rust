## Tipos de Dados Genéricos

Usamos genéricos para criar definições de itens, como assinaturas de função ou
estruturas, que podemos, em seguida, usar com muitos tipos de dados concretos diferentes. Vamos
primeiro analisar como definir funções, estruturas, enumerações e métodos usando
genéricos. Em seguida, discutiremos como os genéricos afetam o desempenho do código.

### Em Definições de Função

Ao definir uma função que usa genéricos, colocamos os genéricos na
assinatura da função, onde normalmente especificaríamos os tipos de dados dos
parâmetros e o valor de retorno. Fazê-lo torna nosso código mais flexível e fornece
mais funcionalidade aos chamadores de nossa função, ao mesmo tempo que evita a duplicação de código.

Continuando com nossa função `maior`, o Exemplo 10-4 mostra duas funções que
encontram o maior valor em uma fatia. Em seguida, combinaremos essas funções em uma única
função que usa genéricos.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-04/src/main.rs:here}}
```

<span class="caption">Exemplo 10-4: Duas funções que diferem apenas em seus
nomes e nos tipos em suas assinaturas</span>

"A função `largest_i32` é aquela que extraímos no Exemplo 10-3 e que encontra o maior `i32` em uma fatia. A função `largest_char` encontra o maior `char` em uma fatia. Os corpos das funções têm o mesmo código, então vamos eliminar a duplicação introduzindo um parâmetro de tipo genérico em uma única função.

Para parametrizar os tipos em uma nova função única, precisamos nomear o parâmetro de tipo, assim como fazemos com os parâmetros de valor de uma função. Você pode usar qualquer identificador como nome de parâmetro de tipo. No entanto, vamos usar `T` porque, por convenção, nomes de parâmetros de tipo em Rust são curtos, frequentemente apenas uma letra, e a convenção de nomenclatura de tipos em Rust é UpperCamelCase. Abreviado de "tipo," `T` é a escolha padrão da maioria dos programadores Rust.

Quando usamos um parâmetro no corpo da função, precisamos declarar o nome do parâmetro na assinatura para que o compilador saiba o que aquele nome significa. Da mesma forma, quando usamos um nome de parâmetro de tipo em uma assinatura de função, precisamos declarar o nome do parâmetro de tipo antes de usá-lo. Para definir a função genérica `maior`, coloque as declarações de nome de tipo dentro de colchetes angulares, `<>`, entre o nome da função e a lista de parâmetros, como mostrado abaixo:"

```rust,ignore
fn largest<T>(list: &[T]) -> &T {
```

Lemos esta definição da seguinte forma: a função `maior` é genérica em relação a algum tipo `T`. Essa função possui um parâmetro chamado `lista`, que é uma fatia de valores do tipo `T`. A função `maior` retornará uma referência a um valor do mesmo tipo `T`.

A Listagem 10-5 mostra a definição combinada da função `maior` usando o tipo de dado genérico em sua assinatura. A listagem também mostra como podemos chamar a função com uma fatia de valores `i32` ou valores `char`. Note que este código ainda não será compilado, mas iremos corrigi-lo posteriormente neste capítulo.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-05/src/main.rs}}
```

<span class="caption">Listagem 10-5: A função `maior` usando parâmetros de tipo genérico; isso ainda não compila</span>

Se compilarmos este código agora, receberemos este erro:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-05/output.txt}}
```

O texto de ajuda menciona `std::cmp::PartialOrd`, que é um *trait*, e vamos falar sobre traits na próxima seção. Por enquanto, saiba que esse erro afirma que o corpo da função `maior` não funcionará para todos os possíveis tipos que `T` poderia ser. Como queremos comparar valores do tipo `T` no corpo da função, só podemos usar tipos cujos valores possam ser ordenados. Para permitir comparações, a biblioteca padrão possui o trait `std::cmp::PartialOrd` que você pode implementar em tipos (consulte o Apêndice C para obter mais informações sobre este trait). Seguindo a sugestão do texto de ajuda, restringimos os tipos válidos para `T` apenas àqueles que implementam `PartialOrd`, e este exemplo será compilado, porque a biblioteca padrão implementa `PartialOrd` tanto para `i32` quanto para `char`.

### Nas Definições de Structs

Também podemos definir structs para usar um parâmetro de tipo genérico em um ou mais campos usando a sintaxe `< >`. A Listagem 10-6 define uma struct `Point<T>` para armazenar valores de coordenadas `x` e `y` de qualquer tipo.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-06/src/main.rs}}
```

<span class="caption">Listagem 10-6: Uma struct `Point<T>` que armazena valores `x` e `y` do tipo `T`</span>

A sintaxe para usar genéricos em definições de structs é semelhante à usada em definições de funções. Primeiro, declaramos o nome do parâmetro de tipo dentro de colchetes angulares logo após o nome da struct. Em seguida, usamos o tipo genérico na definição da struct, onde normalmente especificaríamos tipos de dados concretos.

Observe que, como usamos apenas um tipo genérico para definir `Point<T>`, essa definição indica que a struct `Point<T>` é genérica em relação a algum tipo `T`, e os campos `x` e `y` são *ambos* desse mesmo tipo, seja qual for esse tipo. Se criarmos uma instância de `Point<T>` com valores de tipos diferentes, como na Listagem 10-7, nosso código não será compilado.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-07/src/main.rs}}
```

<span class="caption">Listagem 10-7: Os campos `x` e `y` devem ser do mesmo tipo, porque ambos têm o mesmo tipo genérico `T`..</span>

Neste exemplo, quando atribuímos o valor inteiro 5 a `x`, informamos ao compilador que o tipo genérico `T` será um número inteiro para esta instância de `Point<T>`. Em seguida, quando especificamos 4.0 para `y`, que definimos para ter o mesmo tipo de `x`, receberemos um erro de incompatibilidade de tipos como este:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-07/output.txt}}
```

Para definir uma struct `Point` em que `x` e `y` são ambos genéricos, mas podem ter tipos diferentes, podemos usar múltiplos parâmetros de tipo genérico. Por exemplo, na Listagem 10-8, alteramos a definição de `Point` para ser genérica em relação aos tipos `T` e `U`, onde `x` é do tipo `T` e `y` é do tipo `U`.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-08/src/main.rs}}
```

<span class="caption">Listagem 10-8: Uma `Point<T, U>` genérica em relação a dois tipos, permitindo que `x` e `y` sejam valores de tipos diferentes.</span>

Agora todas as instâncias de `Point` mostradas são permitidas! Você pode usar quantos parâmetros de tipo genérico em uma definição quanto desejar, mas usar mais do que alguns torna seu código difícil de ler. Se você perceber que precisa de muitos tipos genéricos em seu código, isso pode indicar que seu código precisa ser reestruturado em partes menores.

### Em Definições de Enum

Assim como fizemos com as structs, podemos definir enums para armazenar tipos de dados genéricos em suas variantes. Vamos dar outra olhada no enum `Option<T>` que a biblioteca padrão fornece, o qual usamos no Capítulo 6:

```rust
enum Option<T> {
    Some(T),
    None,
}
```

Essa definição deve fazer mais sentido para você agora. Como você pode ver, o enum `Option<T>` é genérico em relação ao tipo `T` e possui duas variantes: `Some`, que contém um valor do tipo `T`, e a variante `None`, que não contém nenhum valor. Ao usar o enum `Option<T>`, podemos expressar o conceito abstrato de um valor opcional, e, como `Option<T>` é genérico, podemos usar essa abstração, independentemente do tipo do valor opcional.

Enums também podem usar múltiplos tipos genéricos. A definição do enum `Result` que usamos no Capítulo 9 é um exemplo disso:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

O enum `Result` é genérico em relação a dois tipos, `T` e `E`, e possui duas variantes: `Ok`, que contém um valor do tipo `T`, e `Err`, que contém um valor do tipo `E`. Essa definição torna conveniente usar o enum `Result` em qualquer lugar em que tenhamos uma operação que possa ter sucesso (retornar um valor de algum tipo `T`) ou falhar (retornar um erro de algum tipo `E`). Na verdade, é isso que usamos para abrir um arquivo na Listagem 9-3, onde `T` foi preenchido com o tipo `std::fs::File` quando o arquivo foi aberto com sucesso e `E` foi preenchido com o tipo `std::io::Error` quando houve problemas na abertura do arquivo.

Quando você identificar situações em seu código com múltiplas definições de structs ou enums que diferem apenas nos tipos dos valores que eles armazenam, você pode evitar a duplicação usando tipos genéricos.

### Em Definições de Métodos

Podemos implementar métodos em structs e enums (como fizemos no Capítulo 5) e usar tipos genéricos em suas definições também. A Listagem 10-9 mostra a struct `Point<T>` que definimos na Listagem 10-6 com um método chamado `x` implementado nela.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-09/src/main.rs}}
```

<span class="caption">Listagem 10-9: Implementando um método chamado `x` na struct `Point<T>` que retornará uma referência ao campo `x` do tipo `T`</span>

Aqui, definimos um método chamado `x` em `Point<T>` que retorna uma referência aos dados no campo `x`.

Observe que precisamos declarar `T` imediatamente após `impl` para que possamos usar `T` para especificar que estamos implementando métodos no tipo `Point<T>`. Ao declarar `T` como um tipo genérico após `impl`, o Rust pode identificar que o tipo entre colchetes angulares em `Point` é um tipo genérico em vez de um tipo concreto. Poderíamos ter escolhido um nome diferente para esse parâmetro genérico em relação ao parâmetro genérico declarado na definição da struct, mas usar o mesmo nome é convencional. Métodos escritos dentro de um `impl` que declara o tipo genérico serão definidos em qualquer instância do tipo, independentemente do tipo concreto que substitui o tipo genérico.

Também podemos especificar restrições nos tipos genéricos ao definir métodos no tipo. Poderíamos, por exemplo, implementar métodos apenas em instâncias de `Point<f32>` em vez de em instâncias de `Point<T>` com qualquer tipo genérico. Na Listagem 10-10, usamos o tipo concreto `f32`, o que significa que não declaramos nenhum tipo após `impl`.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-10/src/main.rs:here}}
```

<span class="caption">Listagem 10-10: Um bloco `impl` que se aplica apenas a uma struct com um tipo concreto específico para o parâmetro de tipo genérico `T`</span>

Este código significa que o tipo `Point<f32>` terá um método `distance_from_origin`; outras instâncias de `Point<T>` onde `T` não é do tipo `f32` não terão este método definido. O método mede a distância do nosso ponto ao ponto nas coordenadas (0.0, 0.0) e utiliza operações matemáticas que estão disponíveis apenas para tipos de ponto flutuante.

Os parâmetros de tipo genérico em uma definição de struct nem sempre são os mesmos que você usa nas assinaturas dos métodos dessa mesma struct. A Listagem 10-11 usa os tipos genéricos `X1` e `Y1` para a struct `Point` e `X2` e `Y2` para a assinatura do método `mistura` para tornar o exemplo mais claro. O método cria uma nova instância de `Point` com o valor de `x` do `self` `Point` (do tipo `X1`) e o valor de `y` do `Point` passado como argumento (do tipo `Y2`).

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-11/src/main.rs}}
```

<span class="caption">Listagem 10-11: Um método que utiliza tipos genéricos diferentes da definição de sua struct</span>

No `main`, definimos um `Point` que possui um `i32` para `x` (com valor `5`) e um `f64` para `y` (com valor `10.4`). A variável `p2` é uma struct `Point` que possui um slice de string para `x` (com valor `"Hello"`) e um `char` para `y` (com valor `c`). Chamando `mixup` em `p1` com o argumento `p2` nos dá `p3`, que terá um `i32` para `x`, porque `x` veio de `p1`. A variável `p3` terá um `char` para `y`, porque `y` veio de `p2`. A chamada da macro `println!` imprimirá `p3.x = 5, p3.y = c`.

O objetivo deste exemplo é demonstrar uma situação em que alguns parâmetros genéricos são declarados com `impl` e outros são declarados com a definição do método. Aqui, os parâmetros genéricos `X1` e `Y1` são declarados após `impl`, porque estão relacionados com a definição da struct. Os parâmetros genéricos `X2` e `Y2` são declarados após `fn mixup`, porque são relevantes apenas para o método.

### Desempenho de Código Usando Generics

Você pode estar se perguntando se há algum custo em tempo de execução ao usar parâmetros de tipo genérico. A boa notícia é que o uso de tipos genéricos não tornará seu programa mais lento do que seria com tipos concretos.

O Rust alcança isso realizando a *monomorfização* do código que usa genéricos durante o tempo de compilação. A *monomorfização* é o processo de transformar código genérico em código específico, preenchendo os tipos concretos que são usados durante a compilação. Nesse processo, o compilador faz o oposto das etapas que usamos para criar a função genérica no Exemplo 10-5: o compilador examina todos os lugares onde o código genérico é chamado e gera código para os tipos concretos com os quais o código genérico é chamado.

Vamos ver como isso funciona usando o tipo genérico `Option<T>` da biblioteca padrão:

```rust
let integer = Some(5);
let float = Some(5.0);
```

Quando o Rust compila este código, ele realiza a monomorfização. Durante esse processo, o compilador lê os valores que foram usados nas instâncias de `Option<T>` e identifica dois tipos de `Option<T>`: um é `i32` e o outro é `f64`. Como resultado, ele expande a definição genérica de `Option<T>` em duas definições especializadas para `i32` e `f64`, substituindo assim a definição genérica pelas específicas.

A versão monomorfizada do código se parece com o seguinte (o compilador utiliza nomes diferentes do que estamos usando aqui para ilustração):

<span class="filename">Filename: src/main.rs</span>

```rust
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

A definição genérica `Option<T>` é substituída pelas definições específicas criadas pelo compilador. Como o Rust compila código genérico em código que especifica o tipo em cada instância, não há custo em tempo de execução ao usar genéricos. Quando o código é executado, ele funciona da mesma forma que se tivéssemos duplicado cada definição manualmente. O processo de monomorfização torna os genéricos do Rust extremamente eficientes em tempo de execução.
