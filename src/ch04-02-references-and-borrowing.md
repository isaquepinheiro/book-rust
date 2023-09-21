## Referências e Empréstimos

O problema com o código de tupla no Exemplo 4-5 é que precisamos devolver a `String` para a função chamadora para que possamos ainda usá-la após a chamada para `calculate_length`, porque a `String` foi movida para dentro de `calculate_length`. Em vez disso, podemos fornecer uma referência ao valor da `String`. Uma *referência* é como um ponteiro, pois é um endereço que podemos seguir para acessar os dados armazenados nesse endereço; esses dados são de propriedade de alguma outra variável. Ao contrário de um ponteiro, uma referência é garantida para apontar para um valor válido de um tipo específico durante a vida dessa referência.

Aqui está como você definiria e usaria uma função `calculate_length` que tem uma referência a um objeto como parâmetro, em vez de assumir a propriedade do valor:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-07-reference/src/main.rs:all}}
```

Primeiro, observe que todo o código de tupla na declaração da variável e no valor de retorno da função desapareceu. Segundo, observe que passamos `&s1` para `calculate_length` e, em sua definição, pegamos `&String` em vez de `String`. Esses símbolos de ampersand representam *referências*, e eles permitem que você se refira a um valor sem assumir a propriedade dele. A Figura 4-5 ilustra esse conceito.

<img alt="Três tabelas: a tabela para `s` contém apenas um ponteiro para a tabela de `s1`. A tabela de `s1` contém os dados da pilha para `s1` e aponta para os dados da string no heap." src="img/trpl04-05.svg" class="center" />

<span class="caption">Figura 4-5: Um diagrama de `&String s` apontando para `String s1`</span>

> Observação: O oposto de fazer referência usando `&` é a *desreferenciação*, que é realizada com o operador de desreferenciação, `*`. Veremos alguns usos do operador de desreferenciação no Capítulo 8 e discutiremos detalhes da desreferenciação no Capítulo 15.

Vamos dar uma olhada mais de perto na chamada da função aqui:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-07-reference/src/main.rs:here}}
```

A sintaxe `&s1` nos permite criar uma referência que *se refere* ao valor de `s1`, mas não o possui. Como ela não possui o valor, o valor ao qual aponta não será liberado quando a referência deixar de ser usada.

Da mesma forma, a assinatura da função usa `&` para indicar que o tipo do parâmetro `s` é uma referência. Vamos adicionar algumas anotações explicativas:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-08-reference-with-annotations/src/main.rs:here}}
```

O escopo no qual a variável `s` é válida é o mesmo que o escopo de qualquer parâmetro de função, mas o valor apontado pela referência não é liberado quando `s` deixa de ser usada, porque `s` não tem a propriedade. Quando as funções têm referências como parâmetros em vez dos valores reais, não precisamos retornar os valores para devolver a propriedade, porque nunca a tivemos.

Chamamos a ação de criar uma referência de *empréstimo*. Como na vida real, se alguém possui algo, você pode pegar emprestado deles. Quando terminar, você tem que devolver. Você não é o proprietário.

Então, o que acontece se tentarmos modificar algo que estamos emprestando? Tente o código no Exemplo 4-6. Alerta de spoiler: não funciona!

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-06/src/main.rs}}
```

<span class="caption">Exemplo 4-6: Tentativa de modificar um valor emprestado</span>

Aqui está o erro:

```console
{{#include ../listings/ch04-understanding-ownership/listing-04-06/output.txt}}
```

Assim como as variáveis são imutáveis por padrão, as referências também são. Não é permitido modificar algo para o qual temos uma referência.

### Referências Mutáveis

Podemos corrigir o código do Exemplo 4-6 para nos permitir modificar um valor emprestado com apenas algumas pequenas alterações que usam, em vez disso, uma *referência mutável*:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-09-fixes-listing-04-06/src/main.rs}}
```

Primeiro, alteramos `s` para ser `mut`. Em seguida, criamos uma referência mutável com `&mut s` onde chamamos a função `change` e atualizamos a assinatura da função para aceitar uma referência mutável com `some_string: &mut String`. Isso deixa muito claro que a função `change` vai modificar o valor que ela empresta.

Referências mutáveis têm uma grande restrição: se você tem uma referência mutável para um valor, você não pode ter outras referências para esse valor. Este código que tenta criar duas referências mutáveis para `s` falhará:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-10-multiple-mut-not-allowed/src/main.rs:here}}
```

Aqui está o erro:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-10-multiple-mut-not-allowed/output.txt}}
```

Esse erro diz que o código é inválido porque não podemos emprestar `s` como mutável mais de uma vez ao mesmo tempo. O primeiro empréstimo mutável está em `r1` e deve durar até ser usado no `println!`, mas entre a criação dessa referência mutável e seu uso, tentamos criar outra referência mutável em `r2` que empresta os mesmos dados que `r1`.

A restrição que impede múltiplas referências mutáveis aos mesmos dados ao mesmo tempo permite a mutação, mas de maneira muito controlada. Isso é algo com o qual novos programadores Rust podem ter dificuldade, porque a maioria das linguagens permite que você faça mutações sempre que desejar. O benefício dessa restrição é que o Rust pode evitar corridas de dados em tempo de compilação. Uma *corrida de dados* é semelhante a uma condição de corrida e ocorre quando ocorrem os três comportamentos a seguir:

* Dois ou mais ponteiros acessam os mesmos dados ao mesmo tempo.
* Pelo menos um dos ponteiros está sendo usado para escrever nos dados.
* Não há mecanismo sendo usado para sincronizar o acesso aos dados.

Corridas de dados causam comportamento indefinido e podem ser difíceis de diagnosticar e corrigir quando você está tentando identificá-las em tempo de execução; o Rust evita esse problema recusando-se a compilar código com corridas de dados!

Como sempre, podemos usar chaves (chaves curvas) para criar um novo escopo, permitindo várias referências mutáveis, mas não *simultâneas*:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-11-muts-in-separate-scopes/src/main.rs:here}}
```

O Rust aplica uma regra semelhante para combinar referências mutáveis e imutáveis. Este código resulta em um erro:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-12-immutable-and-mutable-not-allowed/src/main.rs:here}}
```

Aqui está o erro:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-12-immutable-and-mutable-not-allowed/output.txt}}
```

Ufa! Também não podemos ter uma referência mutável enquanto temos uma referência imutável para o mesmo valor.

Os usuários de uma referência imutável não esperam que o valor mude subitamente sob eles! No entanto, múltiplas referências imutáveis são permitidas porque ninguém que está apenas lendo os dados tem a capacidade de afetar a leitura dos dados de outra pessoa.

Observe que o escopo de uma referência começa a partir de onde ela é introduzida e continua até a última vez que a referência é usada. Por exemplo, este código será compilado porque o último uso das referências imutáveis, o `println!`, ocorre antes que a referência mutável seja introduzida:

```rust,edition2021
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-13-reference-scope-ends/src/main.rs:here}}
```

Os escopos das referências imutáveis `r1` e `r2` terminam após o `println!` onde elas são usadas pela última vez, o que ocorre antes da criação da referência mutável `r3`. Esses escopos não se sobrepõem, então este código é permitido: o compilador pode determinar que a referência não está mais sendo usada antes do final do escopo.

Embora erros de empréstimo possam ser frustrantes às vezes, lembre-se de que é o compilador do Rust apontando um possível erro precocemente (em tempo de compilação, em vez de em tempo de execução) e mostrando exatamente onde está o problema. Assim, você não precisa rastrear por que seus dados não são o que você pensava que eram.

### Referências Penduradas

Em linguagens com ponteiros, é fácil erroneamente criar um *ponteiro pendurado* - um ponteiro que faz referência a uma localização na memória que pode ter sido fornecida a outra pessoa - liberando alguma memória enquanto se preserva um ponteiro para essa memória. No entanto, no Rust, o compilador garante que as referências nunca serão referências penduradas: se você tem uma referência a alguns dados, o compilador garantirá que os dados não sairão de escopo antes que a referência aos dados saia de escopo.

Vamos tentar criar uma referência pendurada para ver como o Rust as impede com um erro de compilação:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-14-dangling-reference/src/main.rs}}
```

Aqui está o erro:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-14-dangling-reference/output.txt}}
```

Esta mensagem de erro se refere a um recurso que ainda não cobrimos: lifetimes (tempo de vida). Discutiremos lifetimes em detalhes no Capítulo 10. Mas, se desconsiderarmos as partes sobre lifetimes, a mensagem contém a chave para entender por que esse código é um problema:

```text
this function's return type contains a borrowed value, but there is no value
for it to be borrowed from
```

Vamos analisar mais de perto o que está acontecendo em cada estágio do nosso código `dangle`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-15-dangling-reference-annotated/src/main.rs:here}}
```

Porque `s` é criada dentro de `dangle`, quando o código de `dangle` é concluído, `s` será desalocada. Mas tentamos retornar uma referência a ela. Isso significa que esta referência estaria apontando para uma `String` inválida. Isso não é bom! O Rust não nos permite fazer isso.

A solução aqui é retornar a `String` diretamente:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-16-no-dangle/src/main.rs:here}}
```

Isso funciona sem nenhum problema. A propriedade é transferida para fora, e nada é desalocado.

### As Regras das Referências

Vamos recapitular o que discutimos sobre referências:

* Em um determinado momento, você pode ter *ou* uma referência mutável *ou* qualquer quantidade de referências imutáveis.
* As referências devem sempre ser válidas.

Em seguida, vamos examinar um tipo diferente de referência: slices (fatiamentos).
