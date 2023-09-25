## Erros Irrecuperáveis com `panic!`

Às vezes, coisas ruins acontecem no seu código e não há nada que você possa fazer a respeito. Nestes casos, o Rust possui o macro `panic!`. Existem duas maneiras de causar um pânico na prática: ao tomar uma ação que faz nosso código entrar em pânico (como acessar um array além do final) ou chamando explicitamente o macro `panic!`. Em ambos os casos, causamos um pânico em nosso programa. Por padrão, esses panics imprimirão uma mensagem de falha, desenrolarão, limparão a pilha e encerrarão o programa. Por meio de uma variável de ambiente, você também pode fazer com que o Rust exiba a pilha de chamadas quando ocorrer um pânico, facilitando o rastreamento da origem do pânico.

> ### Desenrolando a Pilha ou Abortando em Resposta a um Pânico
>
> Por padrão, quando ocorre um pânico, o programa começa a *desenrolar*, o que significa que o Rust volta pela pilha e limpa os dados de cada função que encontra. No entanto, esse processo de volta e limpeza é um trabalho árduo. Portanto, o Rust permite que você escolha a alternativa de *abortar* imediatamente, o que encerra o programa sem fazer a limpeza.
>
> A memória que o programa estava usando precisará ser limpa pelo sistema operacional. Se em seu projeto você precisar tornar o binário resultante o menor possível, você pode alternar de desenrolar para abortar em caso de pânico adicionando `panic = 'abort'` nas seções `[profile]` apropriadas em seu arquivo *Cargo.toml*. Por exemplo, se você deseja abortar em caso de pânico no modo de lançamento, adicione o seguinte:
>
> ```toml
> [profile.release]
> panic = 'abort'
> ```

Vamos tentar chamar `panic!` em um programa simples:

<span class="filename">Filename: src/main.rs</span>

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-01-panic/src/main.rs}}
```

Quando você executar o programa, verá algo semelhante a isto:

```console
{{#include ../listings/ch09-error-handling/no-listing-01-panic/output.txt}}
```

A chamada para `panic!` causa a mensagem de erro contida nas duas últimas linhas. A primeira linha mostra nossa mensagem de pânico e o local em nosso código-fonte onde o pânico ocorreu: *src/main.rs:2:5* indica que é a segunda linha, quinto caractere do nosso arquivo *src/main.rs*.

Neste caso, a linha indicada faz parte do nosso código e, se formos para essa linha, veremos a chamada do macro `panic!`. Em outros casos, a chamada `panic!` pode estar no código que nosso código chama, e o nome do arquivo e o número da linha relatados pela mensagem de erro serão do código de outra pessoa, onde o macro `panic!` é chamado, não a linha do nosso código que eventualmente levou à chamada `panic!`. Podemos usar o rastreamento de volta das funções das quais a chamada `panic!` veio para descobrir a parte do nosso código que está causando o problema. Discutiremos rastreamentos de volta com mais detalhes a seguir.

### Usando um Rastreamento de Volta do `panic!`

Vamos olhar outro exemplo para ver como é quando uma chamada `panic!` vem de uma biblioteca devido a um erro em nosso código, em vez de ser chamada diretamente pelo nosso código. A Listagem 9-1 possui algum código que tenta acessar um índice em um vetor além do intervalo de índices válidos.

<span class="filename">Filename: src/main.rs</span>

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-01/src/main.rs}}
```

<span class="caption">Listagem 9-1: Tentando acessar um elemento além do fim de um vetor, o que causará uma chamada para `panic!`</span>

Aqui, estamos tentando acessar o 100º elemento do nosso vetor (que está no índice 99, porque a indexação começa em zero), mas o vetor tem apenas 3 elementos. Nesta situação, o Rust entrará em pânico. Usar `[]` deveria retornar um elemento, mas se você passar um índice inválido, não há elemento que o Rust poderia retornar aqui que estaria correto.

Em C, tentar ler além do final de uma estrutura de dados é comportamento indefinido. Você pode obter o que estiver na localização da memória que corresponderia a esse elemento na estrutura de dados, mesmo que a memória não pertença a essa estrutura. Isso é chamado de *buffer overread* e pode levar a vulnerabilidades de segurança se um atacante conseguir manipular o índice de tal forma a ler dados que não deveriam ser permitidos e que estão armazenados após a estrutura de dados.

Para proteger seu programa contra esse tipo de vulnerabilidade, se você tentar ler um elemento em um índice que não existe, o Rust interromperá a execução e se recusará a continuar. Vamos tentar e ver:

```console
{{#include ../listings/ch09-error-handling/listing-09-01/output.txt}}
```

Este erro aponta para a linha 4 do nosso arquivo `main.rs`, onde tentamos acessar o índice 99. A próxima linha de nota nos diz que podemos definir a variável de ambiente `RUST_BACKTRACE` para obter um rastreamento de volta exatamente do que aconteceu para causar o erro. Um *rastreamento de volta* é uma lista de todas as funções que foram chamadas para chegar a este ponto. Os rastreamentos de volta no Rust funcionam como em outras linguagens: a chave para ler o rastreamento de volta é começar do topo e ler até ver arquivos que você escreveu. Esse é o ponto onde o problema se originou. As linhas acima desse ponto são código que seu código chamou; as linhas abaixo são código que chamou seu código. Essas linhas antes e depois podem incluir código do Rust central, código da biblioteca padrão ou crates que você está usando. Vamos tentar obter um rastreamento de volta definindo a variável de ambiente `RUST_BACKTRACE` com qualquer valor exceto 0. A Listagem 9-2 mostra uma saída semelhante ao que você verá.

<!-- manual-regeneration
cd listings/ch09-error-handling/listing-09-01
RUST_BACKTRACE=1 cargo run
copy the backtrace output below
check the backtrace number mentioned in the text below the listing
-->

```console
$ RUST_BACKTRACE=1 cargo run
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 99', src/main.rs:4:5
stack backtrace:
   0: rust_begin_unwind
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/std/src/panicking.rs:584:5
   1: core::panicking::panic_fmt
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/panicking.rs:142:14
   2: core::panicking::panic_bounds_check
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/panicking.rs:84:5
   3: <usize as core::slice::index::SliceIndex<[T]>>::index
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/slice/index.rs:242:10
   4: core::slice::index::<impl core::ops::index::Index<I> for [T]>::index
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/slice/index.rs:18:9
   5: <alloc::vec::Vec<T,A> as core::ops::index::Index<I>>::index
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/alloc/src/vec/mod.rs:2591:9
   6: panic::main
             at ./src/main.rs:4:5
   7: core::ops::function::FnOnce::call_once
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/ops/function.rs:248:5
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

<span class="caption">Listagem 9-2: O rastreamento gerado por uma chamada a `panic!` exibido quando a variável de ambiente `RUST_BACKTRACE` está definida</span>

Isso é muita saída! A saída exata que você vê pode ser diferente dependendo
do seu sistema operacional e da versão do Rust. Para obter rastreios com esta
informação, os símbolos de depuração devem estar habilitados. Os símbolos de
depuração são habilitados por padrão ao usar `cargo build` ou `cargo run`
sem a bandeira `--release`, como estamos fazendo aqui.

Na saída da Listagem 9-2, a linha 6 do rastreamento aponta para a linha em nosso
projeto que está causando o problema: linha 4 de *src/main.rs*. Se não quisermos
que nosso programa entre em pânico, devemos começar nossa investigação no local apontado
pela primeira linha que menciona um arquivo que escrevemos. Na Listagem 9-1, onde
deliberadamente escrevemos código que entraria em pânico, a maneira de corrigir o pânico
é não solicitar um elemento além do alcance dos índices do vetor. Quando seu código
entrar em pânico no futuro, você precisará descobrir que ação o código está tomando
com quais valores para causar o pânico e o que o código deveria fazer em vez disso.

Voltaremos ao `panic!` e quando devemos ou não usá-lo para
lidar com condições de erro na seção [“Usar `panic!` ou não
usar `panic!`”][to-panic-or-not-to-panic]<!-- ignore --> mais adiante neste
capítulo. Em seguida, veremos como recuperar de um erro usando `Result`.

[to-panic-or-not-to-panic]:
ch09-03-to-panic-or-not-to-panic.html#to-panic-or-not-to-panic
