# Programando um Jogo de Adivinhação

Vamos entrar no mundo do Rust trabalhando em um projeto prático juntos! Este capítulo irá apresentá-lo a alguns conceitos comuns do Rust, mostrando como usá-los em um programa real. Você aprenderá sobre `let`, `match`, métodos, funções associadas, crates externos e muito mais! Nos capítulos seguintes, exploraremos essas ideias com mais detalhes. Neste capítulo, você praticará apenas os fundamentos.

Vamos implementar um problema clássico para iniciantes em programação: um jogo de adivinhação. Aqui está como ele funciona: o programa irá gerar um número inteiro aleatório entre 1 e 100. Em seguida, solicitará ao jogador que insira um palpite. Após o jogador inserir um palpite, o programa indicará se o palpite é muito baixo ou muito alto. Se o palpite estiver correto, o jogo imprimirá uma mensagem de parabéns e sairá.

## Configurando um Novo Projeto

Para configurar um novo projeto, vá para o diretório *projects* que você criou no Capítulo 1 e crie um novo projeto usando o Cargo, da seguinte forma:

```console
$ cargo new guessing_game
$ cd guessing_game
```

O primeiro comando, `cargo new`, recebe o nome do projeto (`guessing_game`) como o primeiro argumento. O segundo comando muda para o diretório do novo projeto.

Dê uma olhada no arquivo gerado *Cargo.toml*:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial
rm -rf no-listing-01-cargo-new
cargo new no-listing-01-cargo-new --name guessing_game
cd no-listing-01-cargo-new
cargo run > output.txt 2>&1
cd ../../..
-->

<span class="filename">Filename: Cargo.toml</span>

```toml
{{#include ../listings/ch02-guessing-game-tutorial/no-listing-01-cargo-new/Cargo.toml}}
```

Como você viu no Capítulo 1, `cargo new` gera um programa "Olá, mundo!" para você. Dê uma olhada no arquivo *src/main.rs*:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-01-cargo-new/src/main.rs}}
```

Agora vamos compilar este programa "Olá, mundo!" e executá-lo em um único passo usando o comando `cargo run`:

```console
{{#include ../listings/ch02-guessing-game-tutorial/no-listing-01-cargo-new/output.txt}}
```

O comando `run` é muito útil quando você precisa iterar rapidamente em um projeto, como faremos neste jogo, testando rapidamente cada iteração antes de prosseguir para a próxima.

Reabra o arquivo *src/main.rs*. Você escreverá todo o código neste arquivo.

## Processando um Palpite

A primeira parte do programa do jogo de adivinhação solicitará a entrada do usuário, processará essa entrada e verificará se a entrada está no formato esperado. Para começar, permitiremos que o jogador insira um palpite. Insira o código do Exemplo 2-1 no arquivo *src/main.rs*.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:all}}
```

<span class="caption">Exemplo 2-1: Código que obtém um palpite do usuário e o imprime</span>

Este código contém muitas informações, então vamos analisá-lo linha por linha. Para obter a entrada do usuário e, em seguida, imprimir o resultado como saída, precisamos trazer a biblioteca de entrada/saída `io` para o escopo. A biblioteca `io` vem da biblioteca padrão, conhecida como `std`:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:io}}
```

Por padrão, o Rust possui um conjunto de itens definidos na biblioteca padrão que são trazidos para o escopo de todo programa. Esse conjunto é chamado de *prelúdio*, e você pode ver tudo o que está nele [na documentação da biblioteca padrão][prelude].

Se um tipo que você deseja usar não estiver no prelúdio, você deve trazer esse tipo explicitamente para o escopo com uma declaração `use`. O uso da biblioteca `std::io` fornece várias funcionalidades úteis, incluindo a capacidade de aceitar a entrada do usuário.

Como você viu no Capítulo 1, a função `main` é o ponto de entrada do programa:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:main}}
```

A sintaxe `fn` declara uma nova função; os parênteses, `()`, indicam que não há parâmetros; e a chave, `{`, inicia o corpo da função.

Como você também aprendeu no Capítulo 1, `println!` é uma macro que imprime uma string na tela:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:print}}
```

Este código está imprimindo uma mensagem informando sobre o jogo e solicitando a entrada do usuário.

### Armazenando Valores com Variáveis

A seguir, vamos criar uma *variável* para armazenar a entrada do usuário, como segue:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:string}}
```

Agora o programa está ficando interessante! Há muita coisa acontecendo nesta pequena linha. Usamos a declaração `let` para criar a variável. Aqui está outro exemplo:

```rust,ignore
let apples = 5;
```

Esta linha cria uma nova variável chamada `apples` e a associa ao valor 5. No Rust, as variáveis são imutáveis por padrão, o que significa que, uma vez que atribuímos um valor à variável, o valor não será alterado. Discutiremos esse conceito em detalhes na seção ["Variáveis e Mutabilidade"][variables-and-mutability] no Capítulo 3. Para tornar uma variável mutável, adicionamos `mut` antes do nome da variável:

```rust,ignore
let apples = 5; // immutable
let mut bananas = 5; // mutable
```

> Nota: A sintaxe `//` inicia um comentário que continua até o final da linha. O Rust ignora tudo o que está nos comentários. Discutiremos os comentários com mais detalhes no [Capítulo 3][comments].

Voltando ao programa do jogo de adivinhação, agora você sabe que `let mut guess` introduzirá uma variável mutável chamada `guess`. O sinal de igual (`=`) informa ao Rust que queremos vincular algo à variável agora. À direita do sinal de igual está o valor ao qual `guess` está vinculado, que é o resultado da chamada a `String::new`, uma função que retorna uma nova instância de uma `String`. [`String`][string] é um tipo de string fornecido pela biblioteca padrão que é uma sequência de texto codificado em UTF-8 que pode crescer dinamicamente.

A sintaxe `::` na linha `::new` indica que `new` é uma função associada ao tipo `String`. Uma *função associada* é uma função implementada em um tipo, neste caso, `String`. Esta função `new` cria uma nova string vazia. Você encontrará uma função `new` em muitos tipos porque é um nome comum para uma função que cria um novo valor de algum tipo.

Em resumo, a linha `let mut guess = String::new();` criou uma variável mutável que está atualmente vinculada a uma nova instância vazia de uma `String`. Ufa!

### Recebendo Entrada do Usuário

Lembre-se de que incluímos a funcionalidade de entrada/saída da biblioteca padrão com `use std::io;` na primeira linha do programa. Agora chamaremos a função `stdin` do módulo `io`, que nos permitirá lidar com a entrada do usuário:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:read}}
```

Se não tivéssemos importado a biblioteca `io` com `use std::io;` no início do programa, ainda poderíamos usar a função escrevendo-a como `std::io::stdin`. A função `stdin` retorna uma instância de [`std::io::Stdin`][iostdin], que é um tipo que representa um identificador para a entrada padrão do seu terminal.

Em seguida, a linha `.read_line(&mut guess)` chama o método [`read_line`][read_line] no identificador de entrada padrão para obter a entrada do usuário. Também estamos passando `&mut guess` como argumento para `read_line` para informar a ele em qual string armazenar a entrada do usuário. O trabalho completo de `read_line` é pegar o que o usuário digita na entrada padrão e anexá-lo a uma string (sem sobrescrever seu conteúdo), portanto, passamos essa string como um argumento. A string precisa ser mutável para que o método possa alterar o conteúdo da string.

O símbolo `&` indica que este argumento é uma *referência*, que oferece uma maneira de permitir que várias partes do seu código acessem um único dado sem a necessidade de copiar esse dado na memória várias vezes. Referências são um recurso complexo, e uma das principais vantagens do Rust é o quão seguro e fácil é usar referências. Você não precisa saber muitos detalhes para concluir este programa. Por enquanto, tudo o que você precisa saber é que, assim como as variáveis, as referências são imutáveis por padrão. Portanto, você precisa escrever `&mut guess` em vez de `&guess` para torná-la mutável. (O Capítulo 4 explicará referências de forma mais detalhada.)

<!-- Old heading. Do not remove or links may break. -->
<a id="handling-potential-failure-with-the-result-type"></a>

### Lidando com Possíveis Falhas com `Result`

Ainda estamos trabalhando nesta linha de código. Agora, estamos discutindo uma terceira linha de texto, mas observe que ela ainda faz parte de uma única linha lógica de código. A próxima parte é este método:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:expect}}
```

Poderíamos ter escrito este código da seguinte forma:

```rust,ignore
io::stdin().read_line(&mut guess).expect("Failed to read line");
```

No entanto, uma única linha longa é difícil de ler, por isso é melhor dividi-la. É frequentemente sábio introduzir uma quebra de linha e outros espaços em branco para ajudar a separar linhas longas quando você chama um método com a sintaxe `.method_name()`. Agora vamos discutir o que esta linha faz.

Como mencionado anteriormente, `read_line` coloca o que o usuário digita na string que passamos para ela, mas também retorna um valor `Result`. [`Result`][result] é uma [*enumeração*][enums], frequentemente chamada de *enum*, que é um tipo que pode estar em um de vários estados possíveis. Chamamos cada estado possível de *variante*.

[O Capítulo 6][enums]<!-- ignore --> abordará as enumerações de forma mais detalhada. O objetivo desses tipos `Result` é codificar informações de tratamento de erro.

As variantes de `Result` são `Ok` e `Err`. A variante `Ok` indica que a operação foi bem-sucedida, e dentro de `Ok` está o valor gerado com sucesso. A variante `Err` significa que a operação falhou, e `Err` contém informações sobre como ou por que a operação falhou.

Valores do tipo `Result`, assim como valores de qualquer tipo, têm métodos definidos para eles. Uma instância de `Result` possui um [método `expect`][expect]<!-- ignore --> que você pode chamar. Se esta instância de `Result` for um valor `Err`, `expect` fará com que o programa falhe e exiba a mensagem que você passou como argumento para `expect`. Se o método `read_line` retornar um `Err`, provavelmente será o resultado de um erro proveniente do sistema operacional subjacente. Se esta instância de `Result` for um valor `Ok`, `expect` pegará o valor de retorno que `Ok` está segurando e o retornará apenas para você, para que você possa usá-lo. Neste caso, esse valor é o número de bytes na entrada do usuário.

Se você não chamar `expect`, o programa será compilado, mas você receberá um aviso:

```console
{{#include ../listings/ch02-guessing-game-tutorial/no-listing-02-without-expect/output.txt}}
```

O Rust avisa que você não usou o valor `Result` retornado por `read_line`, indicando que o programa não tratou um possível erro.

A maneira correta de suprimir o aviso é escrever código de tratamento de erros, mas, no nosso caso, queremos que o programa pare quando ocorrer um problema, então podemos usar `expect`. Você aprenderá a lidar com erros em [Capítulo 9][recover]<!-- ignore -->.

### Imprimindo Valores com Marcadores de `println!`

Além da chave de fechamento, há apenas mais uma linha para discutir no código até agora:

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-01/src/main.rs:print_guess}}
```

Esta linha imprime a string que agora contém a entrada do usuário. O conjunto de chaves `{}` é um marcador de posição: pense em `{}` como pequenas pinças de caranguejo que seguram um valor no lugar. Ao imprimir o valor de uma variável, o nome da variável pode ser colocado dentro das chaves. Ao imprimir o resultado da avaliação de uma expressão, coloque chaves vazias na string de formato e, em seguida, siga a string de formato com uma lista separada por vírgulas de expressões para imprimir em cada marcador de posição de chaves vazias na mesma ordem. Imprimir uma variável e o resultado de uma expressão em uma única chamada a `println!` ficaria assim:

```rust
let x = 5;
let y = 10;

println!("x = {x} and y + 2 = {}", y + 2);
```

Este código imprimiria: `x = 5 and y + 2 = 12`.

### Testando a Primeira Parte

Vamos testar a primeira parte do jogo de adivinhação. Execute-o usando `cargo run`:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-01/
cargo clean
cargo run
input 6 -->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 6.44s
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

Neste ponto, a primeira parte do jogo está concluída: estamos obtendo a entrada do teclado e depois a imprimindo.

## Gerando um Número Secreto

A seguir, precisamos gerar um número secreto que o usuário tentará adivinhar. O número secreto deve ser diferente a cada vez para que o jogo seja divertido de jogar mais de uma vez. Vamos usar um número aleatório entre 1 e 100 para que o jogo não seja muito difícil. O Rust ainda não inclui funcionalidades de números aleatórios em sua biblioteca padrão. No entanto, a equipe do Rust fornece uma [`crate rand`][randcrate] com essa funcionalidade.

Lembre-se de que uma crate é uma coleção de arquivos de código-fonte Rust. O projeto que estamos construindo é uma *crate binária*, que é um executável. A crate `rand` é uma *crate de biblioteca*, que contém código destinado a ser usado em outros programas e não pode ser executado por si só.

A coordenação de crates externas pelo Cargo é onde o Cargo realmente brilha. Antes de podermos escrever código que use o `rand`, precisamos modificar o arquivo *Cargo.toml* para incluir a crate `rand` como uma dependência. Abra esse arquivo agora e adicione a seguinte linha ao final, abaixo do cabeçalho `[dependencies]` que o Cargo criou para você. Certifique-se de especificar `rand` exatamente como fizemos aqui, com este número de versão, ou os exemplos de código deste tutorial podem não funcionar:

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:
* ch07-04-bringing-paths-into-scope-with-the-use-keyword.md
* ch14-03-cargo-workspaces.md
-->

<span class="filename">Filename: Cargo.toml</span>

```toml
{{#include ../listings/ch02-guessing-game-tutorial/listing-02-02/Cargo.toml:8:}}
```

No arquivo *Cargo.toml*, tudo o que segue um cabeçalho faz parte dessa seção que continua até que outra seção comece. Em `[dependencies]`, você informa ao Cargo quais crates externas seu projeto depende e quais versões dessas crates você requer. Neste caso, especificamos a crate `rand` com o especificador de versão semântica `0.8.5`. O Cargo entende [Versionamento Semântico][semver] (às vezes chamado de *SemVer*), que é um padrão para escrever números de versão. O especificador `0.8.5` é na verdade uma forma abreviada de `^0.8.5`, o que significa qualquer versão a partir de 0.8.5 até abaixo de 0.9.0.

O Cargo considera essas versões como tendo APIs públicas compatíveis com a versão 0.8.5, e essa especificação garante que você obterá a versão mais recente de correções que ainda seja compatível com o código deste capítulo. Qualquer versão 0.9.0 ou superior não é garantida para ter a mesma API que os exemplos a seguir usam.

Agora, sem alterar nenhum código, vamos construir o projeto, como mostrado na Listagem 2-2.

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
rm Cargo.lock
cargo clean
cargo build -->

```console
$ cargo build
    Updating crates.io index
  Downloaded rand v0.8.5
  Downloaded libc v0.2.127
  Downloaded getrandom v0.2.7
  Downloaded cfg-if v1.0.0
  Downloaded ppv-lite86 v0.2.16
  Downloaded rand_chacha v0.3.1
  Downloaded rand_core v0.6.3
   Compiling libc v0.2.127
   Compiling getrandom v0.2.7
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.16
   Compiling rand_core v0.6.3
   Compiling rand_chacha v0.3.1
   Compiling rand v0.8.5
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53s
```

<span class="caption">Listagem 2-2: A saída ao executar `cargo build` após adicionar a crate `rand` como uma dependência</span>

Você pode ver números de versão diferentes (mas todos serão compatíveis com o código, graças ao SemVer!) e linhas diferentes (dependendo do sistema operacional), e as linhas podem estar em uma ordem diferente.

Quando incluímos uma dependência externa, o Cargo busca as versões mais recentes de tudo o que essa dependência precisa no *registro*, que é uma cópia de dados de [Crates.io][cratesio]. Crates.io é onde as pessoas no ecossistema Rust postam seus projetos Rust de código aberto para que outros os usem.

Após atualizar o registro, o Cargo verifica a seção `[dependencies]` e baixa quaisquer crates listadas que ainda não tenham sido baixadas. Neste caso, embora tenhamos listado apenas `rand` como uma dependência, o Cargo também pegou outras crates nas quais o `rand` depende para funcionar. Após baixar as crates, o Rust as compila e, em seguida, compila o projeto com as dependências disponíveis.

Se você executar imediatamente `cargo build` novamente sem fazer nenhuma alteração, você não receberá nenhuma saída além da linha `Finished`. O Cargo sabe que já baixou e compilou as dependências, e você não alterou nada sobre elas em seu arquivo *Cargo.toml*. O Cargo também sabe que você não alterou nada sobre seu código, então ele também não o recompila. Não tendo nada a fazer, ele simplesmente sai.

Se você abrir o arquivo *src/main.rs*, fizer uma alteração trivial, salvá-la e, em seguida, construir novamente, verá apenas duas linhas de saída:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
touch src/main.rs
cargo build -->

```console
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

Essas linhas mostram que o Cargo apenas atualiza a compilação com sua pequena alteração no arquivo *src/main.rs*. Suas dependências não foram alteradas, então o Cargo sabe que pode reutilizar o que já baixou e compilou para elas.

#### Garantindo Builds Reproduzíveis com o Arquivo *Cargo.lock*

O Cargo possui um mecanismo que garante que você possa reconstruir o mesmo artefato toda vez que você ou qualquer outra pessoa compilar seu código: o Cargo usará apenas as versões das dependências que você especificou até que você indique o contrário. Por exemplo, digamos que na próxima semana a versão 0.8.6 da crate `rand` seja lançada, e essa versão contenha uma correção de bug importante, mas também contenha uma regressão que quebrará seu código. Para lidar com isso, o Rust cria o arquivo *Cargo.lock* na primeira vez que você executa `cargo build`, e agora o temos no diretório *guessing_game*.

Quando você compila um projeto pela primeira vez, o Cargo determina todas as versões das dependências que se encaixam nos critérios e depois as escreve no arquivo *Cargo.lock*. Quando você compila seu projeto no futuro, o Cargo verá que o arquivo *Cargo.lock* existe e usará as versões especificadas lá, em vez de fazer todo o trabalho de determinar as versões novamente. Isso permite que você tenha uma compilação reproduzível automaticamente. Em outras palavras, seu projeto permanecerá na versão 0.8.5 até que você faça uma atualização explícita, graças ao arquivo *Cargo.lock*. Como o arquivo *Cargo.lock* é importante para builds reproduzíveis, ele geralmente é incluído no controle de versão junto com o restante do código em seu projeto.

#### Atualizando uma Crate para Obter uma Nova Versão

Quando você *desejar* atualizar uma crate, o Cargo fornece o comando `update`, que ignorará o arquivo *Cargo.lock* e determinará todas as versões mais recentes que se encaixam nas suas especificações no arquivo *Cargo.toml*. O Cargo, em seguida, escreverá essas versões no arquivo *Cargo.lock*. Caso contrário, por padrão, o Cargo procurará apenas por versões maiores que 0.8.5 e menores que 0.9.0. Se a crate `rand` tiver lançado as duas novas versões 0.8.6 e 0.9.0, você veria o seguinte se executasse `cargo update`:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-02/
cargo update
assuming there is a new 0.8.x version of rand; otherwise use another update
as a guide to creating the hypothetical output shown here -->

```console
$ cargo update
    Updating crates.io index
    Updating rand v0.8.5 -> v0.8.6
```

O Cargo ignora o lançamento 0.9.0. Neste ponto, você também notaria uma alteração no seu arquivo *Cargo.lock*, indicando que a versão da crate `rand` que você está usando agora é a 0.8.6. Para usar a versão 0.9.0 do `rand` ou qualquer versão na série 0.9.*x*, você teria que atualizar o arquivo *Cargo.toml* para se parecer com o seguinte:

```toml
[dependencies]
rand = "0.9.0"
```

Na próxima vez que você executar `cargo build`, o Cargo atualizará o registro de crates disponíveis e reavaliará os requisitos do `rand` de acordo com a nova versão que você especificou.

Há muito mais a dizer sobre [Cargo][doccargo]<!-- ignore --> e [seu ecossistema][doccratesio]<!-- ignore -->, o que discutiremos no Capítulo 14, mas, por enquanto, isso é tudo o que você precisa saber. O Cargo torna muito fácil reutilizar bibliotecas, permitindo que os Rustaceans criem projetos menores que são montados a partir de várias packages.

### Gerando um Número Aleatório

Vamos começar a usar o `rand` para gerar um número a ser adivinhado. O próximo passo é atualizar o arquivo *src/main.rs*, conforme mostrado no exemplo 2-3.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-03/src/main.rs:all}}
```

<span class="caption">Exemplo 2-3: Adicionando código para gerar um número aleatório</span>

Primeiro, adicionamos a linha `use rand::Rng;`. O traço `Rng` define métodos que os geradores de números aleatórios implementam, e este traço deve estar no escopo para que possamos usar esses métodos. O Capítulo 10 cobrirá detalhadamente os traços.

Em seguida, adicionamos duas linhas no meio do código. Na primeira linha, chamamos a função `rand::thread_rng`, que nos fornece o gerador de números aleatórios específico que vamos usar: um que é local para a thread de execução atual e é semeado pelo sistema operacional. Em seguida, chamamos o método `gen_range` no gerador de números aleatórios. Este método é definido pelo traço `Rng`, que trouxemos para o escopo com a declaração `use rand::Rng;`. O método `gen_range` recebe uma expressão de intervalo como argumento e gera um número aleatório dentro do intervalo. O tipo de expressão de intervalo que estamos usando aqui tem a forma `início..=fim` e é inclusivo nos limites inferior e superior, então precisamos especificar `1..=100` para solicitar um número entre 1 e 100.

> Observação: Você não vai saber quais traços usar e quais métodos e funções chamar de uma crate apenas, então cada crate possui documentação com instruções para seu uso. Outra característica interessante do Cargo é que executar o comando `cargo doc --open` construirá a documentação fornecida por todas as suas dependências localmente e a abrirá em seu navegador. Se você estiver interessado em outras funcionalidades na crate `rand`, por exemplo, execute `cargo doc --open` e clique em `rand` na barra lateral à esquerda.

A segunda linha nova imprime o número secreto. Isso é útil enquanto estamos desenvolvendo o programa para testá-lo, mas iremos removê-la da versão final. Não é muito divertido se o programa imprimir a resposta assim que ele começar!

Tente executar o programa algumas vezes:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-03/
cargo run
4
cargo run
5
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4

$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.02s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

Você deve obter números aleatórios diferentes, e todos eles devem estar entre 1 e 100. Ótimo trabalho!

## Comparando o Palpite com o Número Secreto

Agora que temos a entrada do usuário e um número aleatório, podemos compará-los. Esse passo é mostrado no Exemplo 2-4. Observe que este código ainda não irá compilar, como explicaremos.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-04/src/main.rs:here}}
```

<span class="caption">Listagem 2-4: Tratando os possíveis valores de retorno ao comparar dois números</span>

Primeiro, adicionamos mais uma declaração `use`, trazendo um tipo chamado `std::cmp::Ordering` para o escopo da biblioteca padrão. O tipo `Ordering` é outro enum e possui as variantes `Less` (Menor), `Greater` (Maior) e `Equal` (Igual). Esses são os três resultados possíveis ao comparar dois valores.

Em seguida, adicionamos cinco novas linhas na parte inferior que utilizam o tipo `Ordering`. O método `cmp` compara dois valores e pode ser chamado em qualquer coisa que possa ser comparada. Ele recebe uma referência ao que você deseja comparar: aqui, estamos comparando `guess` com `secret_number`. Em seguida, ele retorna uma variante do enum `Ordering` que trouxemos para o escopo com a declaração `use`. Usamos uma expressão [`match`][match]<!-- ignore --> para decidir o que fazer em seguida, com base na variante de `Ordering` que foi retornada pela chamada a `cmp` com os valores em `guess` e `secret_number`.

Uma expressão `match` é composta por *ramificações* (*arms*). Uma ramificação consiste em um *padrão* a ser correspondido e o código que deve ser executado se o valor fornecido à `match` se encaixar no padrão dessa ramificação. Rust pega o valor fornecido à `match` e verifica cada padrão de ramificação sequencialmente. Os padrões e a construção `match` são recursos poderosos do Rust: eles permitem que você expresse uma variedade de situações que seu código pode encontrar e garantem que você as trate todas. Esses recursos serão detalhadamente abordados no Capítulo 6 e no Capítulo 18, respectivamente.

Vamos dar uma olhada em um exemplo com a expressão `match` que usamos aqui. Suponha que o usuário tenha adivinhado 50 e o número secreto gerado aleatoriamente desta vez seja 38.

Quando o código compara 50 com 38, o método `cmp` retornará `Ordering::Greater` porque 50 é maior que 38. A expressão `match` recebe o valor `Ordering::Greater` e começa a verificar os padrões de cada ramificação. Ela olha para o padrão da primeira ramificação, `Ordering::Less`, e percebe que o valor `Ordering::Greater` não corresponde a `Ordering::Less`, então ela ignora o código nessa ramificação e passa para a próxima. O padrão da próxima ramificação é `Ordering::Greater`, que *corresponde* a `Ordering::Greater`! O código associado a essa ramificação será executado e imprimirá `Muito grande!` na tela. A expressão `match` termina após o primeiro casamento bem-sucedido, portanto, não examinará a última ramificação neste cenário.

No entanto, o código na Listagem 2-4 ainda não será compilado. Vamos tentar:

<!--
The error numbers in this output should be that of the code **WITHOUT** the
anchor or snip comments
-->

```console
{{#include ../listings/ch02-guessing-game-tutorial/listing-02-04/output.txt}}
```

O core do erro indica que há *tipos incompatíveis*. Rust possui um sistema de tipos forte e estático. No entanto, também possui inferência de tipos. Quando escrevemos `let mut guess = String::new()`, Rust conseguiu inferir que `guess` deveria ser uma `String` e não nos obrigou a escrever o tipo. O `secret_number`, por outro lado, é um tipo numérico. Alguns dos tipos numéricos em Rust podem ter um valor entre 1 e 100: `i32`, um número de 32 bits; `u32`, um número sem sinal de 32 bits; `i64`, um número de 64 bits; bem como outros. A menos que seja especificado de outra forma, Rust assume por padrão um `i32`, que é o tipo de `secret_number` a menos que você adicione informações de tipo em outro lugar que façam Rust inferir um tipo numérico diferente. O motivo do erro é que Rust não pode comparar uma string e um tipo numérico.

Em última análise, queremos converter a `String` que o programa lê como entrada em um tipo numérico real para que possamos compará-lo numericamente com o número secreto. Fazemos isso adicionando esta linha ao corpo da função `main`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-03-convert-string-to-number/src/main.rs:here}}
```

A linha é:

```rust,ignore
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

Criamos uma variável chamada `guess`. Mas espere, o programa já não tem uma variável chamada `guess`? De fato, tem, mas de forma útil, o Rust nos permite sombrear o valor anterior de `guess` com um novo. *Sombrear* nos permite reutilizar o nome da variável `guess` em vez de nos forçar a criar duas variáveis únicas, como `guess_str` e `guess`, por exemplo. Abordaremos isso com mais detalhes no [Capítulo 3][shadowing]<!-- ignore -->, mas por agora, saiba que esse recurso é frequentemente usado quando você deseja converter um valor de um tipo para outro.

Ligamos essa nova variável à expressão `guess.trim().parse()`. O `guess` na expressão se refere ao `guess` original que continha a entrada como uma string. O método `trim` em uma instância de `String` eliminará qualquer espaço em branco no início e no final, o que precisamos fazer para poder comparar a string com o `u32`, que só pode conter dados numéricos. O usuário deve pressionar <span class="keystroke">enter</span> para atender ao `read_line` e inserir sua suposição, o que adiciona um caractere de nova linha à string. Por exemplo, se o usuário digitar <span class="keystroke">5</span> e pressionar <span class="keystroke">enter</span>, `guess` ficará assim: `5\n`. O `\n` representa "nova linha". (No Windows, pressionar <span class="keystroke">enter</span> resulta em um retorno de carro e uma nova linha, `\r\n`). O método `trim` elimina `\n` ou `\r\n`, resultando apenas em `5`.

O método [`parse` em strings][parse]<!-- ignore --> converte uma string em outro tipo. Aqui, o usamos para converter de uma string para um número. Precisamos informar ao Rust o tipo exato de número que desejamos usando `let guess: u32`. Os dois pontos (`:`) após `guess` informam ao Rust que vamos anotar o tipo da variável. Rust possui alguns tipos numéricos integrados; o `u32` visto aqui é um inteiro sem sinal de 32 bits. É uma boa escolha padrão para um número pequeno e positivo. Você aprenderá sobre outros tipos numéricos no [Capítulo 3][integers]<!-- ignore -->.

Além disso, a anotação `u32` neste exemplo de programa e a comparação com `secret_number` significam que o Rust inferirá que `secret_number` também deve ser um `u32`. Portanto, agora a comparação será entre dois valores do mesmo tipo!

O método `parse` funcionará apenas em caracteres que podem ser logicamente convertidos em números e, portanto, pode facilmente causar erros. Se, por exemplo, a string contiver `A👍%`, não haverá como convertê-la em um número. Como pode falhar, o método `parse` retorna um tipo `Result`, assim como o método `read_line` faz (discutido anteriormente em ["Manejando Possíveis Falhas com `Result`"](##handling-potential-failure-with-result)<!-- ignore -->). Trataremos esse `Result` da mesma maneira, usando o método `expect` novamente. Se `parse` retornar uma variante `Err` de `Result` porque não pôde criar um número a partir da string, a chamada `expect` interromperá o jogo e imprimirá a mensagem que fornecemos. Se `parse` conseguir converter com sucesso a string em um número, ele retornará a variante `Ok` de `Result`, e `expect` retornará o número que desejamos a partir do valor `Ok`.

Vamos executar o programa agora:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/no-listing-03-convert-string-to-number/
cargo run
  76
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

Ótimo! Mesmo que espaços tenham sido adicionados antes da suposição, o programa ainda conseguiu determinar que o usuário adivinhou 76. Execute o programa algumas vezes para verificar o comportamento diferente com diferentes tipos de entrada: adivinhe o número corretamente, adivinhe um número muito alto e adivinhe um número muito baixo.

Agora, a maior parte do jogo está funcionando, mas o usuário só pode fazer uma suposição. Vamos mudar isso adicionando um loop!

## Permitindo Múltiplas Suposições com Looping

A palavra-chave `loop` cria um loop infinito. Vamos adicionar um loop para dar aos usuários mais chances de adivinhar o número:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-04-looping/src/main.rs:here}}
```

Como você pode ver, movemos tudo, desde a solicitação de entrada da suposição em diante, para um loop. Certifique-se de recuar as linhas dentro do loop com mais quatro espaços cada e execute o programa novamente. Agora, o programa solicitará outra suposição indefinidamente, o que na verdade introduz um novo problema. Parece que o usuário não pode sair!

O usuário sempre pode interromper o programa usando o atalho do teclado <span class="keystroke">ctrl-c</span>. Mas existe outra maneira de escapar desse monstro insaciável, como mencionado na discussão sobre `parse` em [“Comparando a Suposição com o Número Secreto”](#comparing-the-guess-to-the-secret-number)<!-- ignore -->: se o usuário inserir uma resposta que não seja um número, o programa irá travar. Podemos aproveitar isso para permitir que o usuário saia, como mostrado aqui:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/no-listing-04-looping/
cargo run
(too small guess)
(too big guess)
(correct guess)
quit
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/main.rs:28:47
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Digitar `quit` encerrará o jogo, mas, como você perceberá, qualquer outra entrada que não seja um número também encerrará o jogo. Isso está longe de ser ideal; queremos que o jogo pare quando o número correto for adivinhado.

### Saindo Após um Palpite Correto

Vamos programar o jogo para sair quando o usuário vencer, adicionando uma declaração `break`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/no-listing-05-quitting/src/main.rs:here}}
```

Adicionar a linha `break` após `Você venceu!` faz com que o programa saia do loop quando o usuário adivinha o número secreto corretamente. Sair do loop também significa sair do programa, porque o loop é a última parte da função `main`.

### Lidando com Entradas Inválidas

Para refinar ainda mais o comportamento do jogo, em vez de fazer o programa travar quando o usuário inserir algo que não seja um número, vamos fazer o jogo ignorar entrada não numérica para que o usuário possa continuar adivinhando. Podemos fazer isso alterando a linha em que `guess` é convertido de uma `String` para um `u32`, conforme mostrado na Listagem 2-5.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-05/src/main.rs:here}}
```

<span class="caption">Listagem 2-5: Ignorando uma suposição que não é um número e pedindo outra suposição em vez de travar o programa</span>

Mudamos de uma chamada `expect` para uma expressão `match` para passar de um travamento em caso de erro para o tratamento do erro. Lembre-se de que `parse` retorna um tipo `Result`, e `Result` é um enum que possui as variantes `Ok` e `Err`. Estamos usando uma expressão `match` aqui, assim como fizemos com o resultado `Ordering` do método `cmp`.

Se `parse` conseguir transformar com sucesso a string em um número, ele retornará um valor `Ok` que contém o número resultante. Esse valor `Ok` corresponderá ao padrão da primeira ramificação, e a expressão `match` simplesmente retornará o valor `num` que `parse` produziu e colocou dentro do valor `Ok`. Esse número acabará exatamente onde queremos, na nova variável `guess` que estamos criando.

Se `parse` *não* conseguir transformar a string em um número, ele retornará um valor `Err` que contém mais informações sobre o erro. O valor `Err` não corresponde ao padrão `Ok(num)` na primeira ramificação do `match`, mas corresponde ao padrão `Err(_)` na segunda ramificação. O sublinhado, `_`, é um valor curinga; neste exemplo, estamos dizendo que queremos corresponder a todos os valores `Err`, não importa que informações eles contenham. Portanto, o programa executará o código da segunda ramificação, `continue`, que indica ao programa para ir para a próxima iteração do `loop` e pedir outra suposição. Portanto, efetivamente, o programa ignora todos os erros que `parse` possa encontrar!

Agora, tudo no programa deve funcionar conforme o esperado. Vamos experimentar:

<!-- manual-regeneration
cd listings/ch02-guessing-game-tutorial/listing-02-05/
cargo run
(too small guess)
(too big guess)
foo
(correct guess)
-->

```console
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 4.45s
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

Incrível! Com um pequeno ajuste final, terminaremos o jogo de adivinhação. Lembre-se de que o programa ainda está imprimindo o número secreto. Isso funcionou bem para testes, mas estraga o jogo. Vamos excluir o `println!` que exibe o número secreto. A Listagem 2-6 mostra o código final.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch02-guessing-game-tutorial/listing-02-06/src/main.rs}}
```

<span class="caption">Listing 2-6: Complete guessing game code</span>

At this point, you’ve successfully built the guessing game. Congratulations!

## Resumo

Este projeto foi uma forma prática de introduzi-lo a muitos novos conceitos do Rust: `let`, `match`, funções, o uso de crates externas e muito mais. Nos próximos capítulos, você aprenderá sobre esses conceitos com mais detalhes. O Capítulo 3 aborda conceitos que a maioria das linguagens de programação possui, como variáveis, tipos de dados e funções, e mostra como usá-los em Rust. O Capítulo 4 explora a propriedade, uma característica que torna o Rust diferente de outras linguagens. O Capítulo 5 discute structs e a sintaxe de métodos, e o Capítulo 6 explica como enums funcionam.

[prelude]: ../std/prelude/index.html
[variables-and-mutability]: ch03-01-variables-and-mutability.html#variables-and-mutability
[comments]: ch03-04-comments.html
[string]: ../std/string/struct.String.html
[iostdin]: ../std/io/struct.Stdin.html
[read_line]: ../std/io/struct.Stdin.html#method.read_line
[result]: ../std/result/enum.Result.html
[enums]: ch06-00-enums.html
[expect]: ../std/result/enum.Result.html#method.expect
[recover]: ch09-02-recoverable-errors-with-result.html
[randcrate]: https://crates.io/crates/rand
[semver]: http://semver.org
[cratesio]: https://crates.io/
[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html
[match]: ch06-02-match.html
[shadowing]: ch03-01-variables-and-mutability.html#shadowing
[parse]: ../std/primitive.str.html#method.parse
[integers]: ch03-02-data-types.html#integer-types
