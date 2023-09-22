## Pacotes e Crates

As primeiras partes do sistema de módulos que abordaremos são pacotes e crates.

Uma *crate* é a menor quantidade de código que o compilador Rust considera de uma vez. Mesmo se você executar `rustc` em vez de `cargo` e passar um único arquivo de código-fonte (como fizemos na seção "Escrevendo e Executando um Programa Rust" no Capítulo 1), o compilador considera esse arquivo como uma crate. Crates podem conter módulos, e os módulos podem ser definidos em outros arquivos que são compilados com a crate, como veremos nas próximas seções.

Uma crate pode se apresentar em uma de duas formas: uma crate binária ou uma crate de biblioteca. As *crates binárias* são programas que você pode compilar para um executável que pode ser executado, como um programa de linha de comando ou um servidor. Cada uma delas deve ter uma função chamada `main` que define o que acontece quando o executável é executado. Todas as crates que criamos até agora foram crates binárias.

As *crates de biblioteca* não possuem uma função `main` e não são compiladas para um executável. Em vez disso, elas definem funcionalidades destinadas a serem compartilhadas com vários projetos. Por exemplo, a crate `rand` que usamos no [Capítulo 2][rand]<!-- ignore --> fornece funcionalidades que geram números aleatórios. Na maioria das vezes, quando os Rustaceans dizem "crate", eles estão se referindo a crates de biblioteca, e usam "crate" de forma intercambiável com o conceito de programação geral de uma "biblioteca".

A *crate root* é um arquivo de origem a partir do qual o compilador Rust começa e constitui o módulo raiz de sua crate (explicaremos módulos em detalhes na seção [“Definindo Módulos para Controlar Escopo e Privacidade”][modules]<!-- ignore -->).

Um *pacote* é um conjunto de uma ou mais crates que fornecem um conjunto de funcionalidades. Um pacote contém um arquivo *Cargo.toml* que descreve como construir essas crates. O Cargo é na verdade um pacote que contém a crate binária para a ferramenta de linha de comando que você tem usado para construir seu código. O pacote do Cargo também contém uma crate de biblioteca na qual a crate binária depende. Outros projetos podem depender da crate de biblioteca do Cargo para usar a mesma lógica que a ferramenta de linha de comando do Cargo utiliza.

Um pacote pode conter quantas crates binárias você desejar, mas no máximo apenas uma crate de biblioteca. Um pacote deve conter pelo menos uma crate, seja ela uma crate de biblioteca ou binária.

Vamos seguir o que acontece quando criamos um pacote. Primeiro, digitamos o comando `cargo new`:

```console
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

Depois de executarmos `cargo new`, usamos `ls` para ver o que o Cargo cria. No diretório do projeto, há um arquivo *Cargo.toml*, que nos fornece um pacote. Há também um diretório *src* que contém *main.rs*. Abra o arquivo *Cargo.toml* no seu editor de texto e observe que não há menção a *src/main.rs*. O Cargo segue uma convenção em que *src/main.rs* é a crate root de uma crate binária com o mesmo nome do pacote. Da mesma forma, o Cargo sabe que se o diretório do pacote contiver *src/lib.rs*, o pacote conterá uma crate de biblioteca com o mesmo nome do pacote, e *src/lib.rs* é a sua crate root. O Cargo passa os arquivos de crate root para `rustc` para construir a biblioteca ou a binária.

Aqui, temos um pacote que contém apenas *src/main.rs*, o que significa que contém apenas uma crate binária chamada `my-project`. Se um pacote contiver *src/main.rs* e *src/lib.rs*, ele terá duas crates: uma binária e uma de biblioteca, ambas com o mesmo nome do pacote. Um pacote pode ter múltiplas crates binárias colocando arquivos no diretório *src/bin*: cada arquivo será uma crate binária separada.

[modules]: ch07-02-defining-modules-to-control-scope-and-privacy.html
[rand]: ch02-00-guessing-game-tutorial.html#generating-a-random-number
