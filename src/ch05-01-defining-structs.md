## Definindo e Instanciando Estruturas (Structs)

As structs são semelhantes às tuplas, discutidas na seção ["O Tipo Tupla"][tuples], em que ambas contêm múltiplos valores relacionados. Como nas tuplas, os elementos de uma struct podem ser de tipos diferentes. No entanto, ao contrário das tuplas, em uma struct, você nomeará cada elemento de dados para que fique claro o significado dos valores. Adicionar esses nomes significa que as structs são mais flexíveis do que as tuplas: você não precisa depender da ordem dos dados para especificar ou acessar os valores de uma instância.

Para definir uma struct, utilizamos a palavra-chave `struct` seguida pelo nome completo da struct. O nome de uma struct deve descrever o significado dos elementos de dados que estão sendo agrupados. Em seguida, dentro de chaves, definimos os nomes e tipos dos elementos de dados, que chamamos de *campos*. Por exemplo, o Código 5-1 mostra uma struct que armazena informações sobre uma conta de usuário.

[tuples]: #link-para-tuplas

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-01/src/main.rs:here}}
```

<span class="caption">Código 5-1: Definição da struct User</span>

Para usar uma struct após tê-la definido, criamos uma *instância* daquela struct especificando valores concretos para cada um dos campos. Criamos uma instância declarando o nome da struct e, em seguida, adicionamos chaves contendo pares *chave: valor*, onde as chaves são os nomes dos campos e os valores são os dados que desejamos armazenar nesses campos. Não é necessário especificar os campos na mesma ordem em que os declaramos na struct. Em outras palavras, a definição da struct é como um modelo geral para o tipo, e as instâncias preenchem esse modelo com dados específicos para criar valores do tipo. Por exemplo, podemos declarar um usuário específico conforme mostrado no Código 5-2.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-02/src/main.rs:here}}
```

<span class="caption">Código 5-2: Criando uma instância da struct User</span>

Para obter um valor específico de uma struct, utilizamos a notação de ponto. Por exemplo, para acessar o endereço de e-mail deste usuário, usamos `user1.email`. Se a instância for mutável, podemos alterar um valor usando a notação de ponto e atribuindo a um campo específico. O Código 5-3 mostra como alterar o valor no campo `email` de uma instância mutável de `User`.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-03/src/main.rs:here}}
```

<span class="caption">Código 5-3: Alterando o valor no campo email de uma instância de User mutável</span>

Observe que toda a instância deve ser mutável; o Rust não nos permite marcar apenas determinados campos como mutáveis. Como em qualquer expressão, podemos construir uma nova instância da struct como a última expressão no corpo da função para retornar implicitamente essa nova instância.

A Listagem 5-4 mostra uma função `build_user` que retorna uma instância de `User` com o email e o nome de usuário fornecidos. O campo `active` recebe o valor `true` e o campo `sign_in_count` recebe o valor `1`.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-04/src/main.rs:here}}
```

<span class="caption">Código 5-4: Uma função `build_user` que recebe um email e um nome de usuário e retorna uma instância de `User`.</span>

Faz sentido nomear os parâmetros da função com o mesmo nome dos campos da struct, mas ter que repetir os nomes dos campos e variáveis `email` e `username` pode ser um pouco tedioso. Se a struct tiver mais campos, repetir cada nome se tornaria ainda mais irritante. Felizmente, há uma conveniente abreviação!

<!-- Old heading. Do not remove or links may break. -->
<a id="using-the-field-init-shorthand-when-variables-and-fields-have-the-same-name"></a>

### Usando a Abreviação de Inicialização de Campo

Como os nomes dos parâmetros e os nomes dos campos da struct são exatamente os mesmos no Código 5-4, podemos usar a sintaxe da *abreviação de inicialização de campo* para reescrever a função `build_user` de forma que ela se comporte exatamente da mesma maneira, mas sem a repetição de `username` e `email`, como mostrado no Código 5-5.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-05/src/main.rs:here}}
```

<span class="caption">Código 5-5: Função `build_user` que utiliza a abreviação de inicialização de campo porque os parâmetros `username` e `email` têm o mesmo nome que os campos da struct.</span>

Aqui, estamos criando uma nova instância da struct `User`, que tem um campo chamado `email`. Queremos definir o valor do campo `email` com o valor do parâmetro `email` da função `build_user`. Como o campo `email` e o parâmetro `email` têm o mesmo nome, precisamos escrever apenas `email` em vez de `email: email`.

### Criando Instâncias a partir de Outras Instâncias com a Sintaxe de Atualização de Struct

É frequentemente útil criar uma nova instância de uma struct que inclua a maioria dos valores de outra instância, mas altere alguns deles. Isso pode ser feito usando a *sintaxe de atualização de struct*.

Primeiro, no Código 5-6, mostramos como criar uma nova instância de `User` em `user2` de forma regular, sem usar a sintaxe de atualização. Definimos um novo valor para `email`, mas, de outra forma, usamos os mesmos valores de `user1` que criamos no Código 5-2.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-06/src/main.rs:here}}
```

<span class="caption">Código 5-6: Criando uma nova instância de `User` usando um dos valores de `user1`.</span>

Usando a sintaxe de atualização de struct, podemos obter o mesmo efeito com menos código, como mostrado no Código 5-7. A sintaxe `..` especifica que os campos restantes que não foram definidos explicitamente devem ter o mesmo valor dos campos na instância fornecida.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-07/src/main.rs:here}}
```

<span class="caption">Código 5-7: Utilizando a sintaxe de atualização de struct para definir um novo valor para `email` de uma instância de `User`, mas usando o restante dos valores de `user1`.</span>

O código no Código 5-7 também cria uma instância em `user2` que possui um valor diferente para `email`, mas possui os mesmos valores para os campos `username`, `active` e `sign_in_count` de `user1`. O `..user1` deve vir por último para especificar que quaisquer campos restantes devem obter seus valores dos campos correspondentes em `user1`. No entanto, podemos escolher especificar valores para quantos campos desejarmos, em qualquer ordem, independentemente da ordem dos campos na definição da struct.

Observe que a sintaxe de atualização de struct usa `=` como uma atribuição; isso ocorre porque ela move os dados, assim como vimos na seção [“Variáveis e Dados Interagindo com Move”][move]. Neste exemplo, não podemos mais usar `user1` como um todo após criar `user2`, porque a `String` no campo `username` de `user1` foi movida para `user2`. Se tivéssemos dado a `user2` novos valores `String` tanto para `email` quanto para `username`, e, portanto, usássemos apenas os valores `active` e `sign_in_count` de `user1`, então `user1` ainda seria válido após a criação de `user2`. Tanto `active` quanto `sign_in_count` são tipos que implementam o traço `Copy`, portanto, o comportamento que discutimos na seção [“Dados Apenas na Pilha: Copy”][copy] se aplicaria.

### Usando Tuplas de Structs Sem Campos Nomeados para Criar Tipos Diferentes

Rust também suporta structs que se parecem com tuplas, chamadas de *tuplas de structs*. Tuplas de structs têm o significado adicional fornecido pelo nome da struct, mas não têm nomes associados aos seus campos; em vez disso, têm apenas os tipos dos campos. Tuplas de structs são úteis quando você deseja dar a toda a tupla um nome e tornar a tupla um tipo diferente de outras tuplas, e quando nomear cada campo, como em uma struct regular, seria verboso ou redundante.

Para definir uma tupla de struct, comece com a palavra-chave `struct` e o nome da struct, seguido pelos tipos na tupla. Por exemplo, aqui definimos e usamos duas tuplas de structs chamadas `Color` e `Point`:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-01-tuple-structs/src/main.rs}}
```

Observe que os valores `black` e `origin` são tipos diferentes porque são instâncias de tuplas de structs diferentes. Cada struct que você define é seu próprio tipo, mesmo que os campos dentro da struct possam ter os mesmos tipos. Por exemplo, uma função que recebe um parâmetro do tipo `Color` não pode receber um `Point` como argumento, mesmo que ambos os tipos sejam compostos por três valores `i32`. Caso contrário, as instâncias de tuplas de structs são semelhantes a tuplas no sentido de que você pode destruturá-las em suas partes individuais e pode usar um `.` seguido do índice para acessar um valor individual.

### Structs Semelhantes a Unidades Sem Campos

Você também pode definir structs que não têm campos! Essas são chamadas de *structs semelhantes a unidades* porque se comportam de forma semelhante a `()`, o tipo de unidade que mencionamos na seção [“O Tipo Tupla”][tuples]. Structs semelhantes a unidades podem ser úteis quando você precisa implementar um trait em algum tipo, mas não tem nenhum dado que deseja armazenar no próprio tipo. Discutiremos traits no Capítulo 10. Aqui está um exemplo de declaração e instanciação de uma struct semelhante a unidade chamada `AlwaysEqual`:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-04-unit-like-structs/src/main.rs}}
```

Para definir `AlwaysEqual`, usamos a palavra-chave `struct`, o nome desejado e, em seguida, um ponto e vírgula. Não é necessário usar chaves ou parênteses! Em seguida, podemos obter uma instância de `AlwaysEqual` na variável `subject` de maneira semelhante: usando o nome que definimos, sem chaves ou parênteses. Imagine que mais tarde implementaremos um comportamento para esse tipo, de modo que cada instância de `AlwaysEqual` seja sempre igual a qualquer instância de qualquer outro tipo, talvez para ter um resultado conhecido para fins de teste. Não precisaríamos de nenhum dado para implementar esse comportamento! Você verá no Capítulo 10 como definir traits e implementá-los em qualquer tipo, incluindo structs semelhantes a unidades.

> ### Propriedade dos Dados da Struct
>
> Na definição da struct `User` no Código 5-1, utilizamos o tipo `String` de propriedade em vez do tipo `&str` de fatia de string. Isso é uma escolha deliberada, porque queremos que cada instância desta struct possua todos os seus dados e que esses dados sejam válidos enquanto a struct inteira for válida.
>
> Também é possível para as structs armazenarem referências a dados de propriedade de algo diferente, mas para fazer isso é necessário o uso de *lifetimes*, um recurso do Rust que discutiremos no Capítulo 10. Os lifetimes garantem que os dados referenciados por uma struct sejam válidos enquanto a struct existir. Digamos que você tente armazenar uma referência em uma struct sem especificar lifetimes, como no exemplo a seguir; isso não funcionará:
>
> <span class="filename">Filename: src/main.rs</span>
>
> <!-- CAN'T EXTRACT SEE https://github.com/rust-lang/mdBook/issues/1127 -->
>
> ```rust,ignore,does_not_compile
> struct User {
>     active: bool,
>     username: &str,
>     email: &str,
>     sign_in_count: u64,
> }
>
> fn main() {
>     let user1 = User {
>         active: true,
>         username: "someusername123",
>         email: "someone@example.com",
>         sign_in_count: 1,
>     };
> }
> ```
>
> O compilador reclamará que são necessários especificadores de lifetimes:
>
> ```console
> $ cargo run
>    Compiling structs v0.1.0 (file:///projects/structs)
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:3:15
>   |
> 3 |     username: &str,
>   |               ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 ~     username: &'a str,
>   |
>
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:4:12
>   |
> 4 |     email: &str,
>   |            ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 |     username: &str,
> 4 ~     email: &'a str,
>   |
>
> For more information about this error, try `rustc --explain E0106`.
> error: could not compile `structs` due to 2 previous errors
> ```
>
> No Capítulo 10, discutiremos como corrigir esses erros para que você possa armazenar referências em structs, mas por enquanto, corrigiremos erros como esses usando tipos de propriedade, como `String`, em vez de referências como `&str`.

<!-- manual-regeneration
for the error above
after running update-rustc.sh:
pbcopy < listings/ch05-using-structs-to-structure-related-data/no-listing-02-reference-in-struct/output.txt
paste above
add `> ` before every line -->

[tuples]: ch03-02-data-types.html#the-tuple-type
[move]: ch04-01-what-is-ownership.html#variables-and-data-interacting-with-move
[copy]: ch04-01-what-is-ownership.html#stack-only-data-copy
