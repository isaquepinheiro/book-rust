Cada linguagem de programação possui ferramentas para lidar eficazmente com a duplicação de conceitos. No Rust, uma dessas ferramentas é *genéricos*: substituições abstratas para tipos concretos ou outras propriedades. Podemos expressar o comportamento de genéricos ou como eles se relacionam com outros genéricos sem saber o que estará no lugar deles quando o código for compilado e executado.

Funções podem receber parâmetros de algum tipo genérico, em vez de um tipo concreto como `i32` ou `String`, da mesma forma que uma função recebe parâmetros com valores desconhecidos para executar o mesmo código em múltiplos valores concretos. Na verdade, já usamos genéricos no Capítulo 6 com `Option<T>`, no Capítulo 8 com `Vec<T>` e `HashMap<K, V>`, e no Capítulo 9 com `Result<T, E>`. Neste capítulo, você explorará como definir seus próprios tipos, funções e métodos com genéricos!

Primeiro, revisaremos como extrair uma função para reduzir a duplicação de código. Em seguida, usaremos a mesma técnica para criar uma função genérica a partir de duas funções que diferem apenas nos tipos de seus parâmetros. Também explicaremos como usar tipos genéricos em definições de structs e enums.

Depois, você aprenderá como usar *traits* para definir comportamentos de maneira genérica. É possível combinar traits com tipos genéricos para restringir um tipo genérico a aceitar apenas aqueles tipos que possuem um comportamento específico, em vez de apenas qualquer tipo.

Por fim, discutiremos *lifetimes* (tempo de vida): uma variedade de genéricos que fornecem ao compilador informações sobre como as referências se relacionam entre si. Lifetimes nos permitem fornecer ao compilador informações suficientes sobre valores emprestados para que ele possa garantir que as referências serão válidas em mais situações do que poderia sem nossa ajuda.

**Removing Duplication by Extracting a Function**

Os genéricos nos permitem substituir tipos específicos por um espaço reservado que representa múltiplos tipos para remover a duplicação de código. Antes de mergulharmos na sintaxe de genéricos, vamos primeiro ver como remover a duplicação de código de uma maneira que não envolve tipos genéricos, extraindo uma função que substitui valores específicos por um espaço reservado que representa múltiplos valores. Em seguida, aplicaremos a mesma técnica para extrair uma função genérica! Ao aprender a reconhecer o código duplicado que pode ser extraído para uma função, você começará a identificar o código duplicado que pode usar genéricos.

Começamos com o pequeno programa na Listagem 10-1 que encontra o maior número em uma lista.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-01/src/main.rs:here}}
```

<span class="caption">Listagem 10-1: Encontrando o maior número em uma lista de números</span>

Armazenamos uma lista de números inteiros na variável `number_list` e colocamos uma referência ao primeiro número na lista em uma variável chamada `largest`. Em seguida, iteramos por todos os números na lista e, se o número atual for maior do que o número armazenado em `largest`, substituímos a referência naquela variável. No entanto, se o número atual for menor ou igual ao maior número visto até o momento, a variável não muda, e o código avança para o próximo número na lista. Depois de considerar todos os números na lista, `largest` deve se referir ao maior número, que neste caso é 100.

Agora, recebemos a tarefa de encontrar o maior número em duas listas diferentes de números. Para fazer isso, podemos escolher duplicar o código na Listagem 10-1 e usar a mesma lógica em dois lugares diferentes no programa, como mostrado na Listagem 10-2.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-02/src/main.rs}}
```

<span class="caption">Listagem 10-2: Código para encontrar o maior número em *duas* listas de números</span>

Embora este código funcione, duplicar o código é tedioso e propenso a erros. Também temos que lembrar de atualizar o código em vários lugares quando queremos modificá-lo.

Para eliminar essa duplicação, vamos criar uma abstração definindo uma função que opera em qualquer lista de números inteiros passada como parâmetro. Essa solução torna nosso código mais claro e nos permite expressar o conceito de encontrar o maior número em uma lista de forma abstrata.

Na Listagem 10-3, extraímos o código que encontra o maior número para uma função chamada `maior`. Em seguida, chamamos a função para encontrar o maior número nas duas listas da Listagem 10-2. Também poderíamos usar a função em qualquer outra lista de valores `i32` que possamos ter no futuro.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-03/src/main.rs:here}}
```

<span class="caption">Listagem 10-3: Código abstraído para encontrar o maior número em duas listas</span>

A função `maior` possui um parâmetro chamado `lista`, que representa qualquer fatia concreta de valores `i32` que podemos passar para a função. Como resultado, quando chamamos a função, o código é executado nos valores específicos que passamos.

Em resumo, aqui estão os passos que seguimos para mudar o código da Listagem 10-2 para a Listagem 10-3:

1. Identificar código duplicado.
2. Extrair o código duplicado para o corpo da função e especificar as entradas e saídas desse código na assinatura da função.
3. Atualizar as duas instâncias de código duplicado para chamar a função em vez disso.

A seguir, usaremos esses mesmos passos com genéricos para reduzir a duplicação de código. Da mesma forma que o corpo da função pode operar em uma `lista` abstrata em vez de valores específicos, os genéricos permitem que o código opere em tipos abstratos.

Por exemplo, digamos que tivéssemos duas funções: uma que encontra o maior item em uma fatia de valores `i32` e outra que encontra o maior item em uma fatia de valores `char`. Como eliminaríamos essa duplicação? Vamos descobrir!
