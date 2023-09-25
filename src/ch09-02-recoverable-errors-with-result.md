## Erros Recuperáveis com `Result`

A maioria dos erros não é grave o suficiente para exigir que o programa pare completamente.
Às vezes, quando uma função falha, é por uma razão que você pode facilmente
interpretar e responder. Por exemplo, se você tentar abrir um arquivo e essa
operação falhar porque o arquivo não existe, você pode querer criar o
arquivo em vez de encerrar o processo.

Lembre-se de [“Lidando com Possíveis Falhas usando `Result`”][handle_failure]<!--
ignore --> no Capítulo 2 que o enum `Result` é definido como tendo dois
variantes, `Ok` e `Err`, da seguinte forma:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

O `T` e o `E` são parâmetros de tipo genérico: discutiremos genéricos com mais detalhes no Capítulo 10. O que você precisa saber agora é que `T` representa o tipo do valor que será retornado em um caso de sucesso dentro da variante `Ok`, e `E` representa o tipo do erro que será retornado em um caso de falha dentro da variante `Err`. Como o `Result` tem esses parâmetros de tipo genérico, podemos usar o tipo `Result` e as funções definidas nele em muitas situações diferentes, onde o valor de sucesso e o valor de erro que desejamos retornar podem ser diferentes.

Vamos chamar uma função que retorna um valor `Result`, porque a função pode falhar. Na Listagem 9-3, tentamos abrir um arquivo.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-03/src/main.rs}}
```

<span class="caption">Listagem 9-3: Abrindo um arquivo</span>

O tipo de retorno de `File::open` é um `Result<T, E>`. O parâmetro genérico `T`
foi preenchido pela implementação de `File::open` com o tipo do valor de sucesso, `std::fs::File`, que é um identificador de arquivo. O tipo de `E` usado no valor de erro é `std::io::Error`. Esse tipo de retorno significa que a chamada para `File::open` pode ter sucesso e retornar um identificador de arquivo com o qual podemos ler ou escrever. A chamada da função também pode falhar: por exemplo, o arquivo pode não existir ou podemos não ter permissão para acessá-lo. A função `File::open` precisa de uma maneira de nos dizer se teve sucesso ou falhou e, ao mesmo tempo, nos dar o identificador de arquivo ou informações de erro. Esta é exatamente a informação que o enum `Result` transmite.

No caso em que `File::open` tem sucesso, o valor na variável `greeting_file_result` será uma instância de `Ok` que contém um identificador de arquivo. No caso de falha, o valor em `greeting_file_result` será uma instância de `Err` que contém mais informações sobre o tipo de erro que ocorreu.

Precisamos adicionar código à Listagem 9-3 para tomar ações diferentes dependendo do valor que `File::open` retorna. A Listagem 9-4 mostra uma maneira de lidar com o `Result` usando uma ferramenta básica, a expressão `match` que discutimos no Capítulo 6.

<span class="filename">Filename: src/main.rs</span>

```rust,should_panic
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-04/src/main.rs}}
```

<span class="caption">Listagem 9-4: Usando uma expressão `match` para lidar com as variantes do `Result` que podem ser retornadas</span>

Observe que, assim como o enum `Option`, o enum `Result` e suas variantes foram trazidos para o escopo pelo prelúdio, então não precisamos especificar `Result::` antes das variantes `Ok` e `Err` nos braços do `match`.

Quando o resultado é `Ok`, este código retornará o valor interno `file` da variante `Ok`, e então atribuímos esse valor de identificador de arquivo à variável `greeting_file`. Após o `match`, podemos usar o identificador de arquivo para leitura ou gravação.

O outro braço do `match` lida com o caso em que obtemos um valor `Err` de `File::open`. Neste exemplo, escolhemos chamar a macro `panic!`. Se não houver um arquivo chamado *hello.txt* em nosso diretório atual e executarmos este código, veremos a seguinte saída da macro `panic!`:

```console
{{#include ../listings/ch09-error-handling/listing-09-04/output.txt}}
```

Como de costume, esta saída nos diz exatamente o que deu errado.

O código na Listagem 9-4 irá acionar o `panic!` independentemente do motivo pelo qual `File::open` falhou. No entanto, queremos tomar ações diferentes para diferentes motivos de falha: se `File::open` falhou porque o arquivo não existe, queremos criar o arquivo e retornar o identificador para o novo arquivo. Se `File::open` falhar por qualquer outro motivo - por exemplo, porque não tínhamos permissão para abrir o arquivo - ainda queremos que o código acione o `panic!` da mesma forma que fez na Listagem 9-4. Para isso, adicionamos uma expressão `match` interna, mostrada na Listagem 9-5.

<span class="filename">Filename: src/main.rs</span>

<!-- ignore this test because otherwise it creates hello.txt which causes other
tests to fail lol -->

```rust,ignore
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-05/src/main.rs}}
```

<span class="caption">Listagem 9-5: Lidando com diferentes tipos de erros de maneiras diferentes</span>

O tipo de valor que `File::open` retorna dentro da variante `Err` é `io::Error`, que é uma estrutura fornecida pela biblioteca padrão. Esta estrutura tem um método chamado `kind` que podemos chamar para obter um valor `io::ErrorKind`. O enum `io::ErrorKind` é fornecido pela biblioteca padrão e possui variantes que representam diferentes tipos de erros que podem resultar de uma operação `io`. A variante que queremos usar é `ErrorKind::NotFound`, que indica que o arquivo que estamos tentando abrir ainda não existe. Portanto, fazemos uma correspondência com `greeting_file_result`, mas também temos uma correspondência interna com `error.kind()`.

A condição que queremos verificar na correspondência interna é se o valor retornado por `error.kind()` é a variante `NotFound` do enum `ErrorKind`. Se for, tentamos criar o arquivo com `File::create`. No entanto, como `File::create` também pode falhar, precisamos de um segundo braço na expressão `match` interna. Quando o arquivo não pode ser criado, uma mensagem de erro diferente é impressa. O segundo braço da correspondência externa permanece o mesmo, para que o programa entre em pânico em qualquer erro que não seja o erro de arquivo ausente.

> ### Alternativas ao uso de `match` com `Result<T, E>`
>
> Isso é muita coisa de `match`! A expressão `match` é muito útil, mas também é muito primitiva. No Capítulo 13, você aprenderá sobre closures, que são usadas com muitos dos métodos definidos em `Result<T, E>`. Esses métodos podem ser mais concisos do que usar `match` ao lidar com valores `Result<T, E>` em seu código.
>
> Por exemplo, aqui está outra maneira de escrever a mesma lógica mostrada na Listagem 9-5, desta vez usando closures e o método `unwrap_or_else`:
>
> <!-- CAN'T EXTRACT SEE https://github.com/rust-lang/mdBook/issues/1127 -->
>
> ```rust,ignore
> use std::fs::File;
> use std::io::ErrorKind;
>
> fn main() {
>     let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
>         if error.kind() == ErrorKind::NotFound {
>             File::create("hello.txt").unwrap_or_else(|error| {
>                 panic!("Problem creating the file: {:?}", error);
>             })
>         } else {
>             panic!("Problem opening the file: {:?}", error);
>         }
>     });
> }
> ```
>
> Embora este código tenha o mesmo comportamento da Listagem 9-5, ele não contém nenhuma expressão `match` e é mais fácil de ler. Volte a este exemplo depois de ler o Capítulo 13 e procure o método `unwrap_or_else` na documentação da biblioteca padrão. Muitos desses métodos podem simplificar expressões `match` aninhadas extensas quando você está lidando com erros.

### Atalhos para Pânico em Erro: `unwrap` e `expect`

Usar `match` funciona bem, mas pode ser um pouco verboso e nem sempre comunica bem a intenção. O tipo `Result<T, E>` possui muitos métodos auxiliares definidos nele para realizar várias tarefas mais específicas. O método `unwrap` é um método de atalho implementado de forma semelhante à expressão `match` que escrevemos na Listagem 9-4. Se o valor `Result` for da variante `Ok`, o `unwrap` retornará o valor dentro do `Ok`. Se o `Result` for da variante `Err`, o `unwrap` chamará a macro `panic!` para nós. Aqui está um exemplo de `unwrap` em ação:

<span class="filename">Filename: src/main.rs</span>

```rust,should_panic
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-04-unwrap/src/main.rs}}
```

Se executarmos este código sem um arquivo *hello.txt*, veremos uma mensagem de erro da chamada `panic!` que o método `unwrap` faz:

<!-- manual-regeneration
cd listings/ch09-error-handling/no-listing-04-unwrap
cargo run
copy and paste relevant text
-->

```text
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Os {
code: 2, kind: NotFound, message: "No such file or directory" }',
src/main.rs:4:49
```

Da mesma forma, o método `expect` nos permite escolher a mensagem de erro `panic!`. Usar `expect` em vez de `unwrap` e fornecer boas mensagens de erro pode transmitir sua intenção e facilitar o rastreamento da origem de um pânico. A sintaxe do `expect` é a seguinte:

<span class="filename">Filename: src/main.rs</span>

```rust,should_panic
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-05-expect/src/main.rs}}
```

Usamos o `expect` da mesma forma que o `unwrap`: para retornar o identificador de arquivo ou chamar a macro `panic!`. A mensagem de erro usada pelo `expect` em sua chamada para `panic!` será o parâmetro que passamos para o `expect`, em vez da mensagem padrão do `panic!` que o `unwrap` usa. Eis como fica:

<!-- manual-regeneration
cd listings/ch09-error-handling/no-listing-05-expect
cargo run
copy and paste relevant text
-->

```text
thread 'main' panicked at 'hello.txt should be included in this project: Os {
code: 2, kind: NotFound, message: "No such file or directory" }',
src/main.rs:5:10
```

Em código de qualidade de produção, a maioria dos programadores Rust tende a escolher `expect` em vez de `unwrap` e fornecer mais contexto sobre por que a operação é esperada para sempre ter sucesso. Dessa forma, se suas suposições forem comprovadamente incorretas, você terá mais informações para usar na depuração.

### Propagando Erros

Quando a implementação de uma função chama algo que pode falhar, em vez de lidar com o erro dentro da função em si, você pode retornar o erro para o código que a chamou, permitindo que ele decida o que fazer. Isso é conhecido como *propagar* o erro e concede mais controle ao código que o chamou, onde pode haver mais informações ou lógica que determinam como o erro deve ser tratado do que você tem disponível no contexto do seu código.

Por exemplo, o Listagem 9-6 mostra uma função que lê um nome de usuário de um arquivo. Se o arquivo não existir ou não puder ser lido, essa função retornará esses erros para o código que a chamou.

<span class="filename">Filename: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-06/src/main.rs:here}}
```

<span class="caption">Listagem 9-6: Uma função que retorna erros ao código chamador usando `match`.</span>

Essa função pode ser escrita de uma maneira muito mais curta, mas vamos começar fazendo a maior parte dela manualmente para explorar o tratamento de erros. No final, mostraremos a maneira mais curta. Vamos analisar primeiro o tipo de retorno da função: `Result<String, io::Error>`. Isso significa que a função está retornando um valor do tipo `Result<T, E>`, onde o parâmetro genérico `T` foi preenchido com o tipo concreto `String`, e o tipo genérico `E` foi preenchido com o tipo concreto `io::Error`.

Se essa função for bem-sucedida sem problemas, o código que a chama receberá um valor `Ok` que contém uma `String` - o nome de usuário lido do arquivo por esta função. Se essa função encontrar algum problema, o código chamador receberá um valor `Err` que contém uma instância de `io::Error` com mais informações sobre os problemas encontrados. Escolhemos `io::Error` como o tipo de retorno desta função porque esse é o tipo do valor de erro retornado por ambas as operações que estamos chamando no corpo desta função e que podem falhar: a função `File::open` e o método `read_to_string`.

O corpo da função começa chamando a função `File::open`. Em seguida, lidamos com o valor `Result` usando um `match`, semelhante ao `match` na Listagem 9-4. Se `File::open` tiver sucesso, o identificador `file` no padrão se tornará o valor na variável mutável `username_file` e a função continuará. No caso de `Err`, em vez de chamar `panic!`, usamos a palavra-chave `return` para sair precocemente da função e passar o valor de erro de `File::open`, agora no identificador de padrão `e`, de volta ao código chamador como o valor de erro desta função.

Portanto, se tivermos um identificador de arquivo em `username_file`, a função cria uma nova `String` na variável `username` e chama o método `read_to_string` no identificador de arquivo em `username_file` para ler o conteúdo do arquivo em `username`. O método `read_to_string` também retorna um `Result`, porque também pode falhar, mesmo que `File::open` tenha tido sucesso. Portanto, precisamos de outro `match` para lidar com esse `Result`: se `read_to_string` for bem-sucedido, então nossa função teve sucesso, e retornamos o nome de usuário do arquivo, que está agora em `username`, envolto em um `Ok`. Se `read_to_string` falhar, retornamos o valor de erro da mesma forma que retornamos o valor de erro no `match` que lidou com o valor de retorno de `File::open`. No entanto, não precisamos dizer explicitamente `return`, porque esta é a última expressão na função.

O código que chama esta função lidará então com a obtenção de um valor `Ok` que contém um nome de usuário ou um valor `Err` que contém um `io::Error`. Cabe ao código chamador decidir o que fazer com esses valores. Se o código chamador receber um valor `Err`, ele poderá chamar `panic!` e encerrar o programa, usar um nome de usuário padrão ou buscar o nome de usuário em algum lugar além de um arquivo, por exemplo. Não temos informações suficientes sobre o que o código chamador realmente está tentando fazer, então propagamos todas as informações de sucesso ou erro para ele lidar adequadamente.

Esse padrão de propagar erros é tão comum em Rust que Rust fornece o operador de interrogação `?` para facilitar isso.

#### Um Atalho para Propagar Erros: o Operador `?`

A Listagem 9-7 apresenta uma implementação de `read_username_from_file` que possui a mesma funcionalidade da Listagem 9-6, mas esta implementação utiliza o operador `?`.

<span class="filename">Filename: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-07/src/main.rs:here}}
```

<span class="caption">Listagem 9-7: Uma função que retorna erros ao
código chamador usando o operador `?`</span>

O `?` colocado após um valor `Result` é definido para funcionar de quase a mesma maneira que as expressões `match` que definimos para lidar com os valores `Result` na Listagem 9-6. Se o valor do `Result` for um `Ok`, o valor dentro do `Ok` será retornado desta expressão, e o programa continuará. Se o valor for um `Err`, o `Err` será retornado de toda a função como se tivéssemos usado a palavra-chave `return`, para que o valor de erro seja propagado para o código chamador.

Existe uma diferença entre o que a expressão `match` da Listagem 9-6 faz e o que o operador `?` faz: os valores de erro que têm o operador `?` chamado passam pela função `from`, definida no trait `From` na biblioteca padrão, que é usada para converter valores de um tipo em outro. Quando o operador `?` chama a função `from`, o tipo de erro recebido é convertido no tipo de erro definido no tipo de retorno da função atual. Isso é útil quando uma função retorna um tipo de erro para representar todas as maneiras pelas quais uma função pode falhar, mesmo que partes dela possam falhar por muitos motivos diferentes.

Por exemplo, poderíamos alterar a função `read_username_from_file` na Listagem 9-7 para retornar um tipo de erro personalizado chamado `OurError` que definimos. Se também definirmos `impl From<io::Error> for OurError` para construir uma instância de `OurError` a partir de um `io::Error`, então as chamadas de `?` no corpo de `read_username_from_file` chamarão `from` e converterão os tipos de erro sem a necessidade de adicionar mais código à função.

No contexto da Listagem 9-7, o `?` no final da chamada `File::open` retornará o valor dentro de um `Ok` para a variável `username_file`. Se ocorrer um erro, o operador `?` retornará precocemente de toda a função e dará qualquer valor `Err` ao código chamador. A mesma coisa se aplica ao `?` no final da chamada `read_to_string`.

O operador `?` elimina muita redundância e torna a implementação dessa função mais simples. Até poderíamos encurtar ainda mais esse código encadeando chamadas de método imediatamente após o `?`, como mostrado na Listagem 9-8.

<span class="filename">Filename: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-08/src/main.rs:here}}
```

<span class="caption">Listagem 9-8: Encadeando chamadas de método após o operador `?`</span>

Movemos a criação da nova `String` em `username` para o início da função; essa parte não mudou. Em vez de criar uma variável `username_file`, encadeamos a chamada para `read_to_string` diretamente no resultado de `File::open("hello.txt")?`. Ainda temos um `?` no final da chamada `read_to_string`, e ainda retornamos um valor `Ok` contendo `username` quando tanto `File::open` quanto `read_to_string` têm sucesso, em vez de retornar erros. A funcionalidade é novamente a mesma que nas Listagens 9-6 e 9-7; esta é apenas uma maneira diferente e mais ergonômica de escrevê-la.

A Listagem 9-9 mostra uma maneira de tornar isso ainda mais curto usando `fs::read_to_string`.

<span class="filename">Filename: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-09/src/main.rs:here}}
```

<span class="caption">Listagem 9-9: Usando `fs::read_to_string` em vez de
abrir e depois ler o arquivo</span>

Ler um arquivo em uma string é uma operação bastante comum, portanto, a biblioteca padrão fornece a conveniente função `fs::read_to_string` que abre o arquivo, cria uma nova `String`, lê o conteúdo do arquivo, coloca o conteúdo nessa `String` e a retorna. Claro, usar `fs::read_to_string` não nos dá a oportunidade de explicar todo o tratamento de erros, então fizemos da maneira mais longa primeiro.

#### Onde o Operador `?` Pode Ser Usado

O operador `?` só pode ser usado em funções cujo tipo de retorno seja compatível com o valor em que o `?` é aplicado. Isso ocorre porque o operador `?` está definido para realizar um retorno precoce de um valor da função, da mesma forma que a expressão `match` que definimos na Listagem 9-6. Na Listagem 9-6, o `match` estava usando um valor `Result`, e o braço de retorno precoce retornava um valor `Err(e)`. O tipo de retorno da função deve ser um `Result` para que seja compatível com esse `return`.

Na Listagem 9-10, vamos examinar o erro que receberemos se usarmos o operador `?` em uma função `main` com um tipo de retorno incompatível com o tipo do valor em que usamos `?`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-10/src/main.rs}}
```

<span class="caption">Listagem 9-10: Tentativa de usar o operador `?` na função `main` que retorna `()` não irá compilar</span>

Esse código abre um arquivo, o que pode falhar. O operador `?` segue o valor `Result` retornado por `File::open`, mas essa função `main` tem um tipo de retorno de `()`, não de `Result`. Quando compilamos esse código, obtemos a seguinte mensagem de erro:

```console
{{#include ../listings/ch09-error-handling/listing-09-10/output.txt}}
```

Este erro aponta que só podemos usar o operador `?` em uma função que retorne `Result`, `Option` ou outro tipo que implemente `FromResidual`.

Para corrigir o erro, você tem duas opções. Uma escolha é alterar o tipo de retorno da sua função para ser compatível com o valor em que você está usando o operador `?`, desde que não haja restrições que impeçam isso. A outra técnica é usar um `match` ou um dos métodos de `Result<T, E>` para lidar com o `Result<T, E>` da maneira apropriada.

A mensagem de erro também mencionou que o `?` pode ser usado com valores `Option<T>`. Assim como usar o `?` em `Result`, você só pode usar o `?` em `Option` em uma função que retorne um `Option`. O comportamento do operador `?` quando chamado em um `Option<T>` é semelhante ao seu comportamento quando chamado em um `Result<T, E>`: se o valor for `None`, o `None` será retornado precocemente da função naquele ponto. Se o valor for `Some`, o valor dentro de `Some` é o valor resultante da expressão e a função continua. A Listagem 9-11 tem um exemplo de uma função que encontra o último caractere da primeira linha no texto fornecido:

```rust
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-11/src/main.rs:here}}
```

<span class="caption">Listagem 9-11: Usando o operador `?` em um valor `Option<T>`</span>

Esta função retorna `Option<char>` porque é possível que haja um caractere lá, mas também é possível que não haja. Este código pega o argumento de fatia de string `text` e chama o método `lines` nele, que retorna um iterador sobre as linhas na string. Como esta função deseja examinar a primeira linha, ela chama `next` no iterador para obter o primeiro valor do iterador. Se `text` for uma string vazia, esta chamada para `next` retornará `None`, caso em que usamos `?` para parar e retornar `None` de `last_char_of_first_line`. Se `text` não for uma string vazia, `next` retornará um valor `Some` contendo uma fatia de string da primeira linha em `text`.

O operador `?` extrai a fatia de string, e podemos chamar `chars` nessa fatia de string para obter um iterador de seus caracteres. Estamos interessados no último caractere desta primeira linha, então chamamos `last` para retornar o último item no iterador. Isso é um `Option` porque é possível que a primeira linha seja uma string vazia, por exemplo, se `text` começar com uma linha em branco, mas tiver caracteres em outras linhas, como em `"\nhi"`. No entanto, se houver um último caractere na primeira linha, ele será retornado na variante `Some`. O operador `?` no meio nos dá uma maneira concisa de expressar essa lógica, permitindo-nos implementar a função em uma linha. Se não pudéssemos usar o operador `?` em `Option`, teríamos que implementar essa lógica usando mais chamadas de método ou uma expressão `match`.

Observe que você pode usar o operador `?` em um `Result` em uma função que retorna `Result`, e você pode usar o operador `?` em um `Option` em uma função que retorna `Option`, mas você não pode misturá-los. O operador `?` não converterá automaticamente um `Result` em um `Option` ou vice-versa; nesses casos, você pode usar métodos como o método `ok` em `Result` ou o método `ok_or` em `Option` para fazer a conversão explicitamente.

Até agora, todas as funções `main` que usamos retornaram `()`. A função `main` é especial porque é o ponto de entrada e saída de programas executáveis, e há restrições sobre qual pode ser o tipo de retorno para que os programas se comportem como o esperado.

Felizmente, `main` também pode retornar um `Result<(), E>`. A Listagem 9-12 possui o código da Listagem 9-10, mas mudamos o tipo de retorno de `main` para ser `Result<(), Box<dyn Error>>` e adicionamos um valor de retorno `Ok(())` ao final. Este código agora será compilado:

```rust,ignore
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-12/src/main.rs}}
```

<span class="caption">Listagem 9-12: Alterando `main` para retornar `Result<(), E>` permite o uso do operador `?` em valores `Result`.</span>

O tipo `Box<dyn Error>` é um *objeto de traço*, sobre o qual discutiremos na seção [“Usando Objetos de Traço que Permitem Valores de Tipos Diferentes”][trait-objects]<!-- ignore --> no Capítulo 17. Por enquanto, você pode ler `Box<dyn Error>` como significando "qualquer tipo de erro". Usar o operador `?` em um valor `Result` em uma função `main` com o tipo de erro `Box<dyn Error>` é permitido, porque ele permite que qualquer valor `Err` seja retornado antecipadamente. Mesmo que o corpo desta função `main` só retorne erros do tipo `std::io::Error`, ao especificar `Box<dyn Error>`, esta assinatura continuará correta mesmo que mais código que retorne outros erros seja adicionado ao corpo de `main`.

Quando uma função `main` retorna um `Result<(), E>`, o executável sairá com um valor de `0` se `main` retornar `Ok(())` e sairá com um valor diferente de zero se `main` retornar um valor `Err`. Executáveis escritos em C retornam inteiros ao sair: programas que saem com sucesso retornam o inteiro `0`, e programas que encontram erros retornam algum inteiro diferente de `0`. Rust também retorna inteiros de executáveis para ser compatível com essa convenção.

A função `main` pode retornar qualquer tipo que implemente [o traço `std::process::Termination`][termination]<!-- ignore -->, que contém uma função `report` que retorna um `ExitCode`. Consulte a documentação da biblioteca padrão para obter mais informações sobre como implementar o traço `Termination` para seus próprios tipos.

Agora que discutimos os detalhes de chamar `panic!` ou retornar `Result`, voltemos ao tópico de como decidir qual é apropriado usar em quais casos.

[handle_failure]: ch02-00-guessing-game-tutorial.html#handling-potential-failure-with-result
[trait-objects]: ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types
[termination]: ../std/process/trait.Termination.html
