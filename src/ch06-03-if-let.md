## Fluxo de Controle Conciso com `if let`

A sintaxe `if let` permite combinar `if` e `let` de uma maneira menos verbose para lidar com valores que correspondem a um padrão específico, enquanto ignora o restante. Considere o programa no Exemplo 6-6 que faz correspondência com um valor `Option<u8>` na variável `config_max`, mas deseja executar código apenas se o valor for da variante `Some`.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-06/src/main.rs:here}}
```

<span class="caption">Exemplo 6-6: Um `match` que se preocupa apenas em executar
código quando o valor é `Some`</span>

Se o valor for `Some`, imprimimos o valor contido na variante `Some` vinculando
o valor à variável `max` no padrão. Não queremos fazer nada com o valor `None`. 
Para satisfazer a expressão `match`, precisamos adicionar `_ => ()` após o processamento de apenas uma variante, o que é um código boilerplate irritante de adicionar.

Em vez disso, podemos escrever isso de forma mais concisa usando `if let`. O código a seguir tem o mesmo comportamento que o `match` no Exemplo 6-6:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-12-if-let/src/main.rs:here}}
```

A sintaxe `if let` recebe um padrão e uma expressão separados por um sinal de igual. Ela funciona da mesma maneira que um `match`, onde a expressão é dada ao `match` e o padrão é o primeiro braço. Neste caso, o padrão é `Some(max)`, e `max` vincula-se ao valor dentro do `Some`. Podemos, então, usar `max` no corpo do bloco `if let` da mesma forma que usamos `max` no braço correspondente do `match`. O código dentro do bloco `if let` não é executado se o valor não corresponder ao padrão.

Usar `if let` significa menos digitação, menos recuo e menos código boilerplate. No entanto, você perde a verificação exaustiva que o `match` impõe. A escolha entre `match` e `if let` depende do que você está fazendo em sua situação específica e se ganhar concisão é uma troca apropriada para perder a verificação exaustiva.

Em outras palavras, você pode pensar no `if let` como uma sintaxe açucarada para um `match` que executa código quando o valor corresponde a um padrão e ignora todos os outros valores.

Podemos incluir um `else` com um `if let`. O bloco de código que acompanha o `else` é o mesmo que o bloco de código que acompanharia o caso `_` na expressão `match`, que é equivalente ao `if let` e `else`. Lembre-se da definição da enumeração `Coin` no Exemplo 6-4, onde a variante `Quarter` também continha um valor `UsState`. Se quisermos contar todas as moedas que não sejam quartos que encontrarmos, ao mesmo tempo em que anunciamos o estado das moedas de 25 centavos, podemos fazer isso com uma expressão `match`, como esta:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-13-count-and-announce-match/src/main.rs:here}}
```

Ou poderíamos usar uma expressão `if let` com `else`, assim:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-14-count-and-announce-if-let-else/src/main.rs:here}}
```

Se você estiver em uma situação em que a lógica do seu programa se torna muito verbosa para ser expressa usando um `match`, lembre-se de que o `if let` também está disponível na sua caixa de ferramentas Rust.

## Resumo

Agora, abordamos como usar enums para criar tipos personalizados que podem ser um dos valores enumerados de um conjunto. Mostramos como o tipo `Option<T>` da biblioteca padrão ajuda a utilizar o sistema de tipos para evitar erros. Quando os valores da enumeração contêm dados em seu interior, você pode usar `match` ou `if let` para extrair e usar esses valores, dependendo de quantos casos precisa manipular.

Seus programas Rust agora podem expressar conceitos em seu domínio usando structs e enums. Criar tipos personalizados para usar em sua API garante a segurança de tipos: o compilador se certificará de que suas funções recebam apenas valores do tipo que cada função espera.

Para fornecer uma API bem organizada aos seus usuários, que seja fácil de usar e exponha apenas o necessário, agora vamos nos voltar para os módulos do Rust.

