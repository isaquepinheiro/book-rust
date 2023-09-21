## Apêndice D - Ferramentas de Desenvolvimento Úteis

Neste apêndice, discutiremos algumas ferramentas de desenvolvimento úteis fornecidas pelo projeto Rust. Vamos abordar a formatação automática, maneiras rápidas de aplicar correções em avisos, um verificador de código (linter) e integração com IDEs.

### Formatação Automática com `rustfmt`

A ferramenta `rustfmt` reformata seu código de acordo com o estilo de código da comunidade. Muitos projetos colaborativos utilizam o `rustfmt` para evitar discussões sobre qual estilo usar ao escrever em Rust: todos formatam seu código usando a ferramenta.

Para instalar o `rustfmt`, siga os passos abaixo:

```console
$ rustup component add rustfmt
```

Este comando lhe fornece o `rustfmt` e o `cargo-fmt`, de forma semelhante a como o Rust fornece tanto o `rustc` quanto o `cargo`. Para formatar qualquer projeto Cargo, insira o seguinte comando:

```console
$ cargo fmt
```

A execução deste comando reformata todo o código Rust na criação atual (crate) em uso. Isso deve apenas alterar o estilo do código, sem afetar a semântica do código. Para obter mais informações sobre o `rustfmt`, consulte [a documentação correspondente][rustfmt].

[rustfmt]: https://github.com/rust-lang/rustfmt

### Corrigir seu Código com `rustfix`

A ferramenta `rustfix` está incluída nas instalações do Rust e pode corrigir automaticamente os avisos do compilador que têm uma maneira clara de corrigir o problema, o que é provavelmente o que você deseja. É provável que você já tenha visto avisos do compilador antes. Por exemplo, considere este código:

<span class="filename">Filename: src/main.rs</span>

```rust
fn do_something() {}

fn main() {
    for i in 0..100 {
        do_something();
    }
}
```

Aqui, estamos chamando a função `do_something` 100 vezes, mas nunca usamos a variável `i` no corpo do loop `for`. Rust nos alerta sobre isso:

```console
$ cargo build
   Compiling myprogram v0.1.0 (file:///projects/myprogram)
warning: unused variable: `i`
 --> src/main.rs:4:9
  |
4 |     for i in 0..100 {
  |         ^ help: consider using `_i` instead
  |
  = note: #[warn(unused_variables)] on by default

    Finished dev [unoptimized + debuginfo] target(s) in 0.50s
```

A advertência sugere que usemos `_i` como nome em vez disso: o sublinhado indica que pretendemos que essa variável seja não utilizada. Podemos aplicar automaticamente essa sugestão usando a ferramenta `rustfix`, executando o comando `cargo fix`:

```console
$ cargo fix
    Checking myprogram v0.1.0 (file:///projects/myprogram)
      Fixing src/main.rs (1 fix)
    Finished dev [unoptimized + debuginfo] target(s) in 0.59s
```

Quando olhamos novamente para *src/main.rs*, veremos que o `cargo fix` alterou o código:

<span class="filename">Filename: src/main.rs</span>

```rust
fn do_something() {}

fn main() {
    for _i in 0..100 {
        do_something();
    }
}
```

A variável do loop `for` agora é chamada `_i`, e o aviso não aparece mais.

Você também pode usar o comando `cargo fix` para fazer a transição do seu código entre diferentes edições do Rust. As edições são abordadas no Apêndice E.

### Mais Lints com o Clippy

A ferramenta Clippy é uma coleção de lints (verificadores estáticos) para analisar seu código, permitindo que você identifique erros comuns e melhore seu código Rust.

Para instalar o Clippy, insira o seguinte comando:

```console
$ rustup component add clippy
```

Para executar os lints do Clippy em qualquer projeto Cargo, insira o seguinte comando:

```console
$ cargo clippy
```

Por exemplo, suponha que você escreva um programa que utiliza uma aproximação de uma constante matemática, como π, como faz este programa:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 3.1415;
    let r = 8.0;
    println!("the area of the circle is {}", x * r * r);
}
```

Executar `cargo clippy` neste projeto resulta no seguinte erro:

```text
error: approximate value of `f{32, 64}::consts::PI` found
 --> src/main.rs:2:13
  |
2 |     let x = 3.1415;
  |             ^^^^^^
  |
  = note: `#[deny(clippy::approx_constant)]` on by default
  = help: consider using the constant directly
  = help: for further information visit https://rust-lang.github.io/rust-clippy/master/index.html#approx_constant
```

Este erro informa que o Rust já possui uma constante `PI` mais precisa definida e que seu programa estaria mais correto se você a utilizasse em vez disso. Você deve então alterar seu código para usar a constante `PI`. O código a seguir não gera nenhum erro ou aviso do Clippy:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = std::f64::consts::PI;
    let r = 8.0;
    println!("the area of the circle is {}", x * r * r);
}
```

Para obter mais informações sobre o Clippy, consulte [a documentação correspondente][clippy].

[clippy]: https://github.com/rust-lang/rust-clippy

### Integração com IDEs usando `rust-analyzer`

Para facilitar a integração com IDEs, a comunidade Rust recomenda o uso do [`rust-analyzer`][rust-analyzer]<!-- ignore -->. Essa ferramenta é um conjunto de utilitários centrados no compilador que utiliza o [Protocolo de Servidor de Linguagem][lsp]<!-- ignore -->, que é uma especificação para que IDEs e linguagens de programação possam se comunicar entre si. Diferentes clientes podem usar o `rust-analyzer`, como [o plug-in do analisador Rust para o Visual Studio Code][vscode].

[lsp]: http://langserver.org/
[vscode]: https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer

Visite a [página inicial do projeto `rust-analyzer`][rust-analyzer]<!-- ignore --> para obter instruções de instalação e, em seguida, instale o suporte ao servidor de linguagem em sua IDE específica. Sua IDE adquirirá funcionalidades como autocompletar, ir para a definição e erros em linha.

[rust-analyzer]: https://rust-analyzer.github.io
