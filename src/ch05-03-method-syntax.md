## Sintaxe de Método

*Métodos* são semelhantes a funções: os declaramos com a palavra-chave `fn` e um nome, eles podem ter parâmetros e um valor de retorno, e contêm algum código que é executado quando o método é chamado de algum outro lugar. Ao contrário das funções, os métodos são definidos no contexto de uma struct (ou um enum ou um objeto de trait, que cobrimos nos [Capítulos 6][enums] e [17][trait-objects], respectivamente), e seu primeiro parâmetro é sempre `self`, que representa a instância da struct na qual o método está sendo chamado.

### Definindo Métodos

Vamos mudar a função `area`, que tem uma instância de `Rectangle` como parâmetro, e em vez disso criar um método `area` definido na struct `Rectangle`, como mostrado no Código 5-13.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-13/src/main.rs}}
```

<span class="caption">Código 5-13: Definindo um método `area` na struct `Rectangle`.</span>

Para definir a função dentro do contexto de `Rectangle`, começamos um bloco `impl` (implementação) para `Rectangle`. Tudo dentro deste bloco `impl` estará associado ao tipo `Rectangle`. Em seguida, movemos a função `area` dentro das chaves `impl` e alteramos o primeiro (e, neste caso, único) parâmetro para `self` na assinatura e em todos os lugares dentro do corpo. Em `main`, onde chamamos a função `area` e passamos `rect1` como argumento, podemos usar a *sintaxe de método* para chamar o método `area` em nossa instância de `Rectangle`. A sintaxe do método vem após uma instância: adicionamos um ponto seguido pelo nome do método, parênteses e quaisquer argumentos.

Na assinatura de `area`, usamos `&self` em vez de `rectangle: &Rectangle`. O `&self` é na verdade uma abreviação para `self: &Self`. Dentro de um bloco `impl`, o tipo `Self` é um alias para o tipo para o qual o bloco `impl` é destinado. Métodos devem ter um parâmetro chamado `self` do tipo `Self` como seu primeiro parâmetro, então o Rust permite que você abrevie isso com apenas o nome `self` no primeiro local de parâmetro. Observe que ainda precisamos usar o `&` na frente do `self` abreviado para indicar que este método faz uma referência à instância `Self`, assim como fizemos em `rectangle: &Rectangle`. Métodos podem tomar a propriedade de `self`, emprestar `self` imutavelmente, como fizemos aqui, ou emprestar `self` mutavelmente, assim como podem com qualquer outro parâmetro.

Escolhemos `&self` aqui pelo mesmo motivo que usamos `&Rectangle` na versão da função: não queremos assumir a propriedade e apenas queremos ler os dados na struct, sem escrever neles. Se quiséssemos alterar a instância na qual chamamos o método como parte do que o método faz, usaríamos `&mut self` como primeiro parâmetro. Ter um método que assume a propriedade da instância usando apenas `self` como primeiro parâmetro é raro; essa técnica é geralmente usada quando o método transforma `self` em algo mais e você deseja impedir que o chamador use a instância original após a transformação.

A principal razão para usar métodos em vez de funções, além de fornecer a sintaxe do método e não precisar repetir o tipo de `self` em todas as assinaturas de método, é para organização. Colocamos todas as coisas que podemos fazer com uma instância de um tipo em um único bloco `impl` em vez de fazer com que os futuros usuários de nosso código procurem as capacidades de `Rectangle` em vários lugares na biblioteca que fornecemos.

Observe que podemos escolher dar a um método o mesmo nome que um dos campos da struct. Por exemplo, podemos definir um método em `Rectangle` que também se chama `width`:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-06-method-field-interaction/src/main.rs:here}}
```

Aqui, estamos escolhendo fazer o método `width` retornar `true` se o valor no campo `width` da instância for maior que `0` e `false` se o valor for `0`: podemos usar um campo dentro de um método com o mesmo nome para qualquer finalidade. Em `main`, quando seguimos `rect1.width` com parênteses, o Rust sabe que estamos nos referindo ao método `width`. Quando não usamos parênteses, o Rust sabe que estamos nos referindo ao campo `width`.

Frequentemente, mas nem sempre, quando damos a um método o mesmo nome que um campo, queremos que ele apenas retorne o valor do campo e não faça mais nada. Métodos como esse são chamados de *getters*, e o Rust não os implementa automaticamente para campos de struct, como algumas outras linguagens fazem. Getters são úteis porque você pode tornar o campo privado e o método público, permitindo assim acesso somente leitura a esse campo como parte da API pública do tipo. Discutiremos o que é público e privado e como designar um campo ou método como público ou privado no [Capítulo 7][public].

>### Onde está o operador `->`?

>Em C e C++, são usados dois operadores diferentes para chamar métodos: você usa `.` se estiver chamando um método diretamente no objeto e `->` se estiver chamando o método em um ponteiro para o objeto e precisar desreferenciar o ponteiro primeiro. Em outras palavras, se `objeto` for um ponteiro, `objeto->algo()` é semelhante a `(*objeto).algo()`.

>O Rust não tem um equivalente ao operador `->`; em vez disso, o Rust possui um recurso chamado *referenciamento e desreferenciamento automático*. Chamar métodos é um dos poucos lugares no Rust que têm esse comportamento.

>Aqui está como funciona: quando você chama um método com `objeto.algo()`, o Rust automaticamente adiciona `&`, `&mut`, ou `*` para que `objeto` corresponda à assinatura do método. Em outras palavras, o seguinte são equivalentes:
>
> <!-- CAN'T EXTRACT SEE BUG https://github.com/rust-lang/mdBook/issues/1127 -->
> ```rust
> # #[derive(Debug,Copy,Clone)]
> # struct Point {
> #     x: f64,
> #     y: f64,
> # }
> #
> # impl Point {
> #    fn distance(&self, other: &Point) -> f64 {
> #        let x_squared = f64::powi(other.x - self.x, 2);
> #        let y_squared = f64::powi(other.y - self.y, 2);
> #
> #        f64::sqrt(x_squared + y_squared)
> #    }
> # }
> # let p1 = Point { x: 0.0, y: 0.0 };
> # let p2 = Point { x: 5.0, y: 6.5 };
> p1.distance(&p2);
> (&p1).distance(&p2);
> ```
>
> O primeiro parece muito mais limpo. Esse comportamento automático de referenciamento funciona porque os métodos têm um receptor claro - o tipo de `self`. Dado o receptor e o nome de um método, Rust pode determinar definitivamente se o método está lendo (`&self`), mutando (`&mut self`) ou consumindo (`self`). O fato de Rust tornar o empréstimo implícito para receptores de métodos é uma parte importante para tornar a propriedade (ownership) ergonomicamente viável na prática.

### Métodos com Mais Parâmetros

Vamos praticar o uso de métodos implementando um segundo método na estrutura `Rectangle`. Desta vez, queremos que uma instância de `Rectangle` aceite outra instância de `Rectangle` e retorne `true` se o segundo `Rectangle` puder se encaixar completamente dentro de `self` (o primeiro `Rectangle`); caso contrário, deve retornar `false`. Ou seja, uma vez que tenhamos definido o método `can_hold`, queremos ser capazes de escrever o programa mostrado no Exemplo 5-14.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-14/src/main.rs}}
```

<span class="caption">Exemplo 5-14: Usando o método `can_hold`, que ainda não foi escrito.</span>

A saída esperada se pareceria com o seguinte, porque ambas as dimensões de `rect2` são menores do que as dimensões de `rect1`, mas `rect3` é mais largo do que `rect1`:

```text
Can rect1 hold rect2? true
Can rect1 hold rect3? false
```

Entendemos que queremos definir um método, portanto, ele estará dentro do bloco `impl Rectangle`. O nome do método será `pode_conter`, e ele receberá um empréstimo imutável de outro `Rectangle` como parâmetro. Podemos determinar o tipo do parâmetro olhando o código que chama o método: `ret1.pode_conter(&ret2)` passa `&ret2`, que é um empréstimo imutável de `ret2`, uma instância de `Rectangle`. Isso faz sentido porque só precisamos ler `ret2` (em vez de escrever, o que exigiria um empréstimo mutável), e queremos que a função `main` mantenha a propriedade de `ret2` para que possamos usá-la novamente após chamar o método `pode_conter`. O valor de retorno de `pode_conter` será um booleano, e a implementação verificará se a largura e a altura de `self` são maiores do que a largura e a altura do outro `Rectangle`, respectivamente. Vamos adicionar o novo método `pode_conter` ao bloco `impl` do código do Exemplo 5-13, conforme mostrado no Exemplo 5-15.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-15/src/main.rs:here}}
```

<span class="caption">Exemplo 5-15: Implementação do método `pode_conter` em `Rectangle` que recebe outra instância de `Rectangle` como parâmetro</span>

Quando executamos este código com a função `main` do Exemplo 5-14, obteremos a saída desejada. Os métodos podem receber vários parâmetros que adicionamos à assinatura após o parâmetro `self`, e esses parâmetros funcionam da mesma forma que os parâmetros em funções.

### Funções Associadas

Todas as funções definidas dentro de um bloco `impl` são chamadas de *funções associadas* porque estão associadas ao tipo nomeado após o `impl`. Podemos definir funções associadas que não têm `self` como seu primeiro parâmetro (e, portanto, não são métodos) porque não precisam de uma instância do tipo para funcionar. Já usamos uma função assim: a função `String::from` que é definida no tipo `String`.

Funções associadas que não são métodos são frequentemente usadas para construtores que retornarão uma nova instância da estrutura. Muitas vezes, esses construtores são chamados de `new`, mas `new` não é um nome especial e não está embutido na linguagem. Por exemplo, poderíamos escolher fornecer uma função associada chamada `quadrado` que teria um parâmetro de dimensão e a usaria tanto para largura quanto para altura, tornando assim mais fácil criar um `Rectangle` quadrado sem precisar especificar o mesmo valor duas vezes:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-03-associated-functions/src/main.rs:here}}
```

As palavras-chave `Self` no tipo de retorno e no corpo da função são sinônimos para o tipo que aparece após a palavra-chave `impl`, que neste caso é `Rectangle`.

Para chamar essa função associada, usamos a sintaxe `::` com o nome da estrutura; `let sq = Rectangle::square(3);` é um exemplo. Esta função está no espaço de nomes da estrutura: a sintaxe `::` é usada tanto para funções associadas quanto para espaços de nomes criados por módulos. Discutiremos módulos no [Capítulo 7][modules]<!-- ignore -->.

### Múltiplos Blocos `impl`

Cada struct pode ter múltiplos blocos `impl`. Por exemplo, o código do Exemplo 5-15 é equivalente ao código mostrado no Exemplo 5-16, que possui cada método em seu próprio bloco `impl`.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-16/src/main.rs:here}}
```

<span class="caption">Exemplo 5-16: Reescrevendo o Exemplo 5-15 usando múltiplos blocos `impl`</span>

Não há motivo para separar esses métodos em múltiplos blocos `impl` aqui, mas essa é uma sintaxe válida. Veremos um caso em que múltiplos blocos `impl` são úteis no Capítulo 10, quando discutiremos tipos genéricos e traits.

## Resumo

As structs permitem que você crie tipos personalizados que são significativos para seu domínio. Usando structs, você pode manter peças associadas de dados conectadas entre si e nomear cada peça para tornar seu código claro. Em blocos `impl`, você pode definir funções que estão associadas ao seu tipo, e os métodos são um tipo de função associada que permite especificar o comportamento que as instâncias de suas structs têm.

Mas as structs não são a única maneira de criar tipos personalizados. Vamos agora explorar o recurso de enumeração do Rust para adicionar mais uma ferramenta ao seu arsenal.

[enums]: ch06-00-enums.html
[trait-objects]: ch17-02-trait-objects.md
[public]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#exposing-paths-with-the-pub-keyword
[modules]: ch07-02-defining-modules-to-control-scope-and-privacy.html
