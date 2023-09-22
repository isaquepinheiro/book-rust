## Definindo uma Enumeração

Enquanto as structs oferecem uma maneira de agrupar campos e dados relacionados, como um `Rectangle` com suas propriedades `width` e `height`, as enums oferecem uma maneira de dizer que um valor pertence a um conjunto possível de valores. Por exemplo, podemos querer dizer que `Rectangle` é um dos possíveis formatos que também inclui `Circle` e `Triangle`. Para fazer isso, o Rust nos permite codificar essas possibilidades como uma enumeração.

Vamos examinar uma situação que podemos querer expressar em código e ver por que as enums são úteis e mais apropriadas do que as structs nesse caso. Digamos que precisemos lidar com endereços IP. Atualmente, dois padrões principais são usados para endereços IP: a versão quatro e a versão seis. Como essas são as únicas possibilidades para um endereço IP que nosso programa encontrará, podemos *enumerar* todas as variantes possíveis, que é de onde a enumeração tira seu nome.

Qualquer endereço IP pode ser tanto da versão quatro quanto da versão seis, mas não ambos ao mesmo tempo. Essa propriedade dos endereços IP torna a estrutura de dados enum apropriada, pois um valor de enumeração só pode ser uma de suas variantes. Tanto os endereços da versão quatro quanto os da versão seis ainda são fundamentalmente endereços IP, então eles devem ser tratados como o mesmo tipo quando o código está lidando com situações que se aplicam a qualquer tipo de endereço IP.

Podemos expressar esse conceito em código definindo uma enumeração `IpAddrKind` e listando os tipos possíveis que um endereço IP pode ser, `V4` e `V6`. Essas são as variantes da enumeração:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:def}}
```

`IpAddrKind` é agora um tipo de dado personalizado que podemos usar em outras partes do nosso código.

### Valores de Enumeração

Podemos criar instâncias de cada uma das duas variantes de `IpAddrKind` da seguinte maneira:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:instance}}
```

Observe que as variantes da enumeração estão dentro do espaço de nomes sob seu identificador, e usamos dois pontos duplos para separá-los. Isso é útil porque agora ambos os valores `IpAddrKind::V4` e `IpAddrKind::V6` são do mesmo tipo: `IpAddrKind`. Podemos, por exemplo, definir uma função que aceita qualquer `IpAddrKind`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:fn}}
```

E podemos chamar essa função com qualquer uma das variantes:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:fn_call}}
```

Usar enums tem ainda mais vantagens. Pensando mais sobre nosso tipo de endereço IP, no momento não temos uma maneira de armazenar os dados reais do endereço IP; apenas sabemos qual é o seu *tipo*. Dado que você acabou de aprender sobre structs no Capítulo 5, você pode ser tentado a abordar esse problema com structs, como mostrado no Exemplo 6-1.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-01/src/main.rs:here}}
```

<span class="caption">Exemplo 6-1: Armazenando os dados e a variante `IpAddrKind` de um endereço IP usando uma `struct`</span>

Aqui, definimos uma struct `IpAddr` que possui dois campos: um campo `kind` do tipo `IpAddrKind` (a enumeração que definimos anteriormente) e um campo `address` do tipo `String`. Temos duas instâncias dessa struct. A primeira é `home`, e ela tem o valor `IpAddrKind::V4` como seu `kind` com dados de endereço associados `127.0.0.1`. A segunda instância é `loopback`. Ela tem a outra variante de `IpAddrKind` como seu valor `kind`, `V6`, e possui o endereço `::1` associado a ela. Usamos uma struct para agrupar os valores `kind` e `address` juntos, de modo que agora a variante está associada ao valor.

No entanto, representar o mesmo conceito usando apenas uma enumeração é mais conciso: em vez de uma enumeração dentro de uma struct, podemos colocar dados diretamente em cada variante da enumeração. Essa nova definição da enumeração `IpAddr` diz que tanto as variantes `V4` quanto `V6` terão valores `String` associados:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-02-enum-with-data/src/main.rs:here}}
```

Nós associamos dados diretamente a cada variante da enumeração, então não há necessidade de uma struct adicional. Além disso, é mais fácil ver outro detalhe de como as enumerações funcionam: o nome de cada variante da enumeração que definimos também se torna uma função que constrói uma instância da enumeração. Ou seja, `IpAddr::V4()` é uma chamada de função que recebe um argumento `String` e retorna uma instância do tipo `IpAddr`. Obtemos automaticamente essa função construtora definida como resultado da definição da enumeração.

Há outra vantagem em usar uma enumeração em vez de uma struct: cada variante pode ter tipos e quantidades diferentes de dados associados. Endereços IP da versão quatro sempre terão quatro componentes numéricos com valores entre 0 e 255. Se quiséssemos armazenar endereços `V4` como quatro valores `u8`, mas ainda expressar endereços `V6` como um valor `String`, não poderíamos fazer isso com uma struct. As enums lidam com esse caso com facilidade:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-03-variants-with-different-data/src/main.rs:here}}
```

Mostramos várias maneiras diferentes de definir estruturas de dados para armazenar endereços IP das versões quatro e seis. No entanto, acontece que desejar armazenar endereços IP e codificar qual tipo eles são é tão comum que [a biblioteca padrão tem uma definição que podemos usar!][IpAddr]<!-- ignore --> Vamos ver como a biblioteca padrão define `IpAddr`: ela possui a enumeração exata e as variantes que definimos e usamos, mas incorpora os dados do endereço dentro das variantes na forma de duas structs diferentes, que são definidas de maneira diferente para cada variante:

```rust
struct Ipv4Addr {
    // --snip--
}

struct Ipv6Addr {
    // --snip--
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

Este código ilustra que você pode colocar qualquer tipo de dados dentro de uma variante de enumeração: strings, tipos numéricos ou structs, por exemplo. Você até pode incluir outra enumeração! Além disso, os tipos da biblioteca padrão geralmente não são muito mais complicados do que o que você pode criar.

Observe que, mesmo que a biblioteca padrão contenha uma definição para `IpAddr`, ainda podemos criar e usar nossa própria definição sem conflitos porque não trouxemos a definição da biblioteca padrão para o nosso escopo. Falaremos mais sobre a inclusão de tipos no escopo no Capítulo 7.

Vamos dar uma olhada em outro exemplo de enumeração no Exemplo 6-2: este tem uma variedade de tipos incorporados em suas variantes.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-02/src/main.rs:here}}
```

<span class="caption">Exemplo 6-2: Uma enumeração `Message` cujas variantes armazenam diferentes quantidades e tipos de valores</span>

Esta enumeração possui quatro variantes com diferentes tipos:

- `Quit` não tem dados associados a ela.
- `Move` possui campos nomeados, como uma struct.
- `Write` inclui uma única `String`.
- `ChangeColor` inclui três valores `i32`.

Definir uma enumeração com variantes como as do Exemplo 6-2 é semelhante à definição de diferentes tipos de structs, exceto que a enumeração não usa a palavra-chave `struct` e todas as variantes são agrupadas sob o tipo `Message`. As structs a seguir poderiam armazenar os mesmos dados que as variantes da enumeração anterior:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-04-structs-similar-to-message-enum/src/main.rs:here}}
```

Mas se usássemos as diferentes structs, cada uma das quais tem seu próprio tipo, não seria tão fácil definir uma função para aceitar qualquer um desses tipos de mensagens como poderíamos com a enumeração `Message` definida no Exemplo 6-2, que é um único tipo.

Há mais uma semelhança entre enums e structs: assim como podemos definir métodos em structs usando `impl`, também podemos definir métodos em enums. Aqui está um método chamado `call` que poderíamos definir em nossa enumeração `Message`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-05-methods-on-enums/src/main.rs:here}}
```

O corpo do método usaria `self` para obter o valor no qual chamamos o método. Neste exemplo, criamos uma variável `m` que possui o valor `Message::Write(String::from("hello"))`, e é isso que `self` será no corpo do método `call` quando `m.call()` for executado.

Vamos dar uma olhada em outra enumeração na biblioteca padrão que é muito comum e útil: `Option`.

### A Enumeração `Option` e suas Vantagens sobre Valores Nulos

Esta seção explora um estudo de caso do `Option`, que é outra enumeração definida pela biblioteca padrão. O tipo `Option` codifica o cenário muito comum em que um valor pode ser algo ou pode ser nada.

Por exemplo, se você solicitar o primeiro item em uma lista não vazia, você obterá um valor. Se você solicitar o primeiro item em uma lista vazia, você obterá nada. Expressar esse conceito em termos do sistema de tipos significa que o compilador pode verificar se você tratou todos os casos que deveria estar tratando; essa funcionalidade pode evitar erros que são extremamente comuns em outras linguagens de programação.

O design de linguagens de programação muitas vezes é pensado em termos de quais recursos você inclui, mas os recursos que você exclui também são importantes. O Rust não possui o recurso nulo que muitas outras linguagens têm. *Nulo* é um valor que significa que não há valor ali. Em linguagens com nulo, as variáveis podem sempre estar em um de dois estados: nulo ou não nulo.

Em sua apresentação de 2009, "Referências Nulas: O Erro de Um Bilhão de Dólares", Tony Hoare, o inventor do nulo, tem o seguinte a dizer:

> Eu o chamo de meu erro de um bilhão de dólares. Naquela época, eu estava projetando o primeiro sistema de tipos abrangente para referências em uma linguagem orientada a objetos. Meu objetivo era garantir que todo uso de referências fosse absolutamente seguro, com verificação realizada automaticamente pelo compilador. Mas eu não consegui resistir à tentação de colocar uma referência nula, simplesmente porque era tão fácil de implementar. Isso levou a inúmeros erros, vulnerabilidades e falhas no sistema, que provavelmente causaram um bilhão de dólares em dor e prejuízo nos últimos quarenta anos.

O problema com os valores nulos é que se você tentar usar um valor nulo como um valor não nulo, você receberá algum tipo de erro. Como essa propriedade de nulo ou não nulo é onipresente, é extremamente fácil cometer esse tipo de erro.

No entanto, o conceito que o nulo está tentando expressar ainda é útil: um nulo é um valor que está atualmente inválido ou ausente por algum motivo.

O problema não está realmente no conceito, mas na implementação específica. Como tal, o Rust não possui nulos, mas possui uma enumeração que pode codificar o conceito de um valor presente ou ausente. Esta enumeração é `Option<T>`, e ela é [definida pela biblioteca padrão][option]<!-- ignore --> da seguinte forma:

```rust
enum Option<T> {
    None,
    Some(T),
}
```

A enumeração `Option<T>` é tão útil que é incluída no prelude; você não precisa trazê-la explicitamente para o escopo. Suas variantes também estão incluídas no prelude: você pode usar `Some` e `None` diretamente, sem o prefixo `Option::`. A enumeração `Option<T>` ainda é apenas uma enumeração regular, e `Some(T)` e `None` ainda são variantes do tipo `Option<T>`.

A sintaxe `<T>` é um recurso do Rust sobre o qual ainda não falamos. É um parâmetro de tipo genérico, e abordaremos os genéricos com mais detalhes no Capítulo 10. Por enquanto, tudo o que você precisa saber é que `<T>` significa que a variante `Some` da enumeração `Option` pode conter um único dado de qualquer tipo, e que cada tipo concreto usado em vez de `T` torna o tipo geral `Option<T>` um tipo diferente. Aqui estão alguns exemplos de uso de valores `Option` para armazenar tipos numéricos e tipos de string:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-06-option-examples/src/main.rs:here}}
```

O tipo de `some_number` é `Option<i32>`. O tipo de `some_char` é `Option<char>`, que é um tipo diferente. O Rust pode inferir esses tipos porque especificamos um valor dentro da variante `Some`. Para `absent_number`, o Rust nos obriga a anotar o tipo geral `Option`: o compilador não pode inferir o tipo que a variante `Some` correspondente conterá olhando apenas para um valor `None`. Aqui, informamos ao Rust que pretendemos que `absent_number` seja do tipo `Option<i32>`.

Quando temos um valor `Some`, sabemos que um valor está presente e o valor está contido dentro do `Some`. Quando temos um valor `None`, em certo sentido, isso significa a mesma coisa que nulo: não temos um valor válido. Então, por que usar `Option<T>` é melhor do que usar nulo?

Em resumo, porque `Option<T>` e `T` (onde `T` pode ser qualquer tipo) são tipos diferentes, o compilador não nos permite usar um valor `Option<T>` como se fosse definitivamente um valor válido. Por exemplo, este código não será compilado, pois está tentando adicionar um `i8` a um `Option<i8>`:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-07-cant-use-option-directly/src/main.rs:here}}
```

Se executarmos este código, receberemos uma mensagem de erro como esta:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-07-cant-use-option-directly/output.txt}}
```

Intenso! Na prática, esta mensagem de erro significa que o Rust não entende como adicionar um `i8` e um `Option<i8>`, porque eles são tipos diferentes. Quando temos um valor de um tipo como `i8` no Rust, o compilador garante que sempre tenhamos um valor válido. Podemos prosseguir com confiança sem precisar verificar nulo antes de usar esse valor. Somente quando temos um `Option<i8>` (ou qualquer tipo de valor com o qual estamos trabalhando) é que precisamos nos preocupar em possivelmente não ter um valor, e o compilador garantirá que tratemos esse caso antes de usar o valor.

Em outras palavras, você precisa converter um `Option<T>` em um `T` antes de poder realizar operações `T` com ele. Geralmente, isso ajuda a evitar um dos problemas mais comuns com nulos: supor que algo não é nulo quando na verdade é.

Eliminar o risco de assumir incorretamente um valor não nulo ajuda você a ter mais confiança em seu código. Para ter um valor que possa ser nulo, você deve optar explicitamente por tornar o tipo desse valor `Option<T>`. Em seguida, ao usar esse valor, você é obrigado a lidar explicitamente com o caso em que o valor é nulo. Em todos os lugares em que um valor tem um tipo que não seja `Option<T>`, você *pode* com segurança assumir que o valor não é nulo. Esta foi uma decisão de design deliberada para o Rust limitar a pervasividade do nulo e aumentar a segurança do código Rust.

Então, como você obtém o valor `T` de uma variante `Some` quando tem um valor do tipo `Option<T>` para que possa usá-lo? A enumeração `Option<T>` possui uma grande quantidade de métodos úteis em várias situações; você pode conferir na [documentação][docs]<!-- ignore -->. Se familiarizar com os métodos em `Option<T>` será extremamente útil em sua jornada com o Rust.

Em geral, para usar um valor `Option<T>`, você deseja ter código que lide com cada variante. Você deseja algum código que seja executado apenas quando você tem um valor `Some(T)`, e esse código pode usar o `T` interno. Você deseja que algum outro código seja executado apenas se você tiver um valor `None`, e esse código não terá um valor `T` disponível. A expressão `match` é uma construção de fluxo de controle que faz exatamente isso quando usada com enumerações: ela executará código diferente dependendo da variante da enumeração que possui e esse código pode usar os dados dentro do valor correspondente.

[IpAddr]: ../std/net/enum.IpAddr.html
[option]: ../std/option/enum.Option.html
[docs]: ../std/option/enum.Option.html
