## O Tipo de Dados Slice

*Slice* permite que você faça referência a uma sequência contígua de elementos em uma coleção em vez de toda a coleção. Um slice é uma espécie de referência, portanto, ele não possui propriedade.

Aqui está um pequeno problema de programação: escreva uma função que receba uma sequência de palavras separadas por espaços e retorne a primeira palavra que encontrar nessa sequência. Se a função não encontrar um espaço na sequência, a sequência inteira deve ser considerada como uma única palavra, e a sequência completa deve ser retornada.

Vamos trabalhar na definição dessa função sem usar slices, para compreender o problema que os slices resolverão:

```rust,ignore
fn first_word(s: &String) -> ?
```

A função `first_word` tem um parâmetro `&String`. Não queremos a propriedade, então isso está bem. Mas o que devemos retornar? Na verdade, não temos uma maneira de falar sobre *parte* de uma string. No entanto, poderíamos retornar o índice do final da palavra, indicado por um espaço. Vamos tentar isso, como mostrado no exemplo 4-7.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:here}}
```

<span class="caption">Listagem 4-7: A função `first_word` que retorna um valor de índice de byte no parâmetro `String`</span>

Como precisamos percorrer o `String` elemento por elemento e verificar se um valor é um espaço, vamos converter nossa `String` em uma matriz de bytes usando o método `as_bytes`.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:as_bytes}}
```

Em seguida, criamos um iterador sobre a matriz de bytes usando o método `iter`:

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:iter}}
```

Vamos discutir iteradores com mais detalhes no [Capítulo 13][ch13]<!-- ignore -->. Por enquanto, saiba que `iter` é um método que retorna cada elemento em uma coleção e que `enumerate` envolve o resultado de `iter` e retorna cada elemento como parte de uma tupla. O primeiro elemento da tupla retornada por `enumerate` é o índice, e o segundo elemento é uma referência ao elemento. Isso é um pouco mais conveniente do que calcular o índice manualmente.

Como o método `enumerate` retorna uma tupla, podemos usar padrões para destruturar essa tupla. Discutiremos padrões com mais detalhes no [Capítulo 6][ch6]<!-- ignore -->. No loop `for`, especificamos um padrão que tem `i` para o índice na tupla e `&item` para o único byte na tupla. Como obtemos uma referência ao elemento de `.iter().enumerate()`, usamos `&` no padrão.

Dentro do loop `for`, procuramos pelo byte que representa o espaço usando a sintaxe de literal de byte. Se encontrarmos um espaço, retornamos a posição. Caso contrário, retornamos o comprimento da string usando `s.len()`.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:inside_for}}
```

Agora temos uma maneira de descobrir o índice do final da primeira palavra na string, mas há um problema. Estamos retornando um `usize` por si só, mas é apenas um número significativo no contexto do `&String`. Em outras palavras, porque é um valor separado da `String`, não há garantia de que ainda será válido no futuro. Considere o programa na Listagem 4-8 que utiliza a função `first_word` da Listagem 4-7.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-08/src/main.rs:here}}
```

<span class="caption">Listagem 4-8: Armazenando o resultado da chamada da função `first_word` e, em seguida, alterando o conteúdo da `String`</span>

Este programa é compilado sem erros e também o faria se usássemos `word` após chamar `s.clear()`. Porque `word` não está conectado ao estado de `s` de forma alguma, `word` ainda contém o valor `5`. Poderíamos usar esse valor `5` com a variável `s` para tentar extrair a primeira palavra, mas isso seria um erro porque o conteúdo de `s` mudou desde que salvamos `5` em `word`.

Ter que se preocupar com o índice em `word` ficando desatualizado com os dados em `s` é tedioso e propenso a erros! Gerenciar esses índices é ainda mais frágil se escrevermos uma função `second_word`. Sua assinatura teria que ser assim:

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

Agora estamos acompanhando um índice de início *e* um índice de fim, e temos ainda mais valores que foram calculados a partir de dados em um estado específico, mas não estão ligados a esse estado de forma alguma. Temos três variáveis não relacionadas flutuando por aí que precisam ser mantidas em sincronia.

Felizmente, o Rust tem uma solução para esse problema: slices de string.

### Slices de String

Um *slice de string* é uma referência a uma parte de uma `String` e se parece com isto:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-17-slice/src/main.rs:here}}
```

Em vez de uma referência à `String` inteira, `hello` é uma referência a uma parte da `String`, especificada na parte adicional `[0..5]`. Criamos slices usando uma faixa dentro de colchetes, especificando `[starting_index..ending_index]`, onde `starting_index` é a primeira posição no slice e `ending_index` é uma a mais do que a última posição no slice. Internamente, a estrutura de dados do slice armazena a posição de início e o comprimento do slice, que corresponde a `ending_index` menos `starting_index`. Portanto, no caso de `let world = &s[6..11];`, `world` seria um slice que contém um ponteiro para o byte no índice 6 de `s` com um valor de comprimento de `5`.

A Figura 4-6 mostra isso em um diagrama.

<img alt="Três tabelas: uma tabela representando os dados da pilha de `s`, que aponta para o byte no índice 0 em uma tabela de dados de string "&quot;hello world&quot;" no heap. A terceira tabela representa os dados da pilha do slice `world`, que tem um valor de comprimento de 5 e aponta para o byte 6 na tabela de dados do heap."
src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<span class="caption">Figura 4-6: Slice de string referindo-se a uma parte de uma `String`</span>

Com a sintaxe de intervalo `..` do Rust, se você quiser começar no índice 0, pode omitir o valor antes dos dois pontos. Em outras palavras, essas duas formas são equivalentes:

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

Da mesma forma, se o seu slice incluir o último byte da `String`, você pode omitir o número final. Isso significa que essas duas formas são equivalentes:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

Você também pode omitir ambos os valores para obter um slice da string inteira. Portanto, essas duas formas são equivalentes:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

> Observação: Os índices de intervalo de um slice de string devem ocorrer em fronteiras válidas de caracteres UTF-8. Se você tentar criar um slice de string no meio de um caractere multibyte, seu programa será encerrado com um erro. Para fins de introdução de slices de string, estamos assumindo apenas ASCII nesta seção; uma discussão mais completa sobre o manuseio de UTF-8 está na seção ["Armazenando Texto Codificado em UTF-8 com Strings"][strings]<!-- ignore --> do Capítulo 8.

Com todas essas informações em mente, vamos reescrever a função `first_word` para retornar um slice. O tipo que representa "slice de string" é escrito como `&str`:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-18-first-word-slice/src/main.rs:here}}
```

Obtemos o índice para o final da palavra da mesma maneira que fizemos na Listagem 4-7, procurando a primeira ocorrência de um espaço. Quando encontramos um espaço, retornamos um slice de string usando o início da string e o índice do espaço como os índices de início e término.

Agora, quando chamamos `first_word`, obtemos de volta um único valor que está ligado aos dados subjacentes. O valor é composto por uma referência ao ponto de início do slice e pelo número de elementos no slice.

Retornar um slice também funcionaria para uma função `second_word`:

```rust,ignore
fn second_word(s: &String) -> &str {
```

Agora temos uma API simples que é muito mais difícil de ser errada porque o compilador garantirá que as referências para dentro da `String` permaneçam válidas. Lembre-se do erro no programa da Listagem 4-8, quando obtivemos o índice para o final da primeira palavra, mas depois limpamos a string, tornando nosso índice inválido? Esse código estava logicamente incorreto, mas não mostrou erros imediatos. Os problemas surgiriam mais tarde se continuássemos tentando usar o índice da primeira palavra com uma string esvaziada. Slices tornam esse erro impossível e nos informam que temos um problema em nosso código muito mais cedo. Usar a versão de slice de `first_word` resultaria em um erro de compilação:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-19-slice-error/src/main.rs:here}}
```

Aqui está o erro do compilador:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-19-slice-error/output.txt}}
```

Lembre-se das regras de empréstimo que se tivermos uma referência imutável para algo, não podemos também ter uma referência mutável. Como `clear` precisa encurtar a `String`, ela precisa de uma referência mutável. O `println!` após a chamada para `clear` utiliza a referência em `word`, então a referência imutável ainda deve estar ativa nesse ponto. Rust não permite que a referência mutável em `clear` e a referência imutável em `word` existam ao mesmo tempo, e a compilação falha. Não apenas o Rust tornou nossa API mais fácil de usar, mas também eliminou uma classe inteira de erros no momento da compilação!

<!-- Old heading. Do not remove or links may break. -->
<a id="string-literals-are-slices"></a>

#### String Literals as Slices

Lembre-se de que falamos sobre literais de string sendo armazenados dentro do binário. Agora que sabemos sobre slices, podemos entender adequadamente os literais de string:

```rust
let s = "Hello, world!";
```

O tipo de `s` aqui é `&str`: é um slice que aponta para um ponto específico do binário. Isso também é o motivo pelo qual literais de string são imutáveis; `&str` é uma referência imutável.

#### Slices de String como Parâmetros

Saber que você pode criar slices de literais e valores `String` nos leva a mais uma melhoria na função `first_word`, e essa é sua assinatura:

```rust,ignore
fn first_word(s: &String) -> &str {
```

Um programador Rust mais experiente escreveria a assinatura mostrada na Listagem 4-9 em vez disso, porque ela nos permite usar a mesma função tanto em valores `&String` quanto em valores `&str`.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-09/src/main.rs:here}}
```

<span class="caption">Listagem 4-9: Melhorando a função `first_word` ao usar um slice de string como tipo do parâmetro `s`</span>

Se tivermos um slice de string, podemos passá-lo diretamente. Se tivermos uma `String`, podemos passar um slice da `String` ou uma referência à `String`. Essa flexibilidade aproveita as *coerções de dereferência*, um recurso que abordaremos na seção ["Coerções de Dereferência Implícitas com Funções e Métodos"][deref-coercions]<!--ignore--> do Capítulo 15.

Definir uma função para receber um slice de string em vez de uma referência a uma `String` torna nossa API mais geral e útil sem perder funcionalidade:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-09/src/main.rs:usage}}
```

### Outros Slices

Slices de string, como você pode imaginar, são específicos para strings. Mas também existe um tipo de slice mais geral. Considere este array:

```rust
let a = [1, 2, 3, 4, 5];
```

Assim como podemos querer nos referir a uma parte de uma string, podemos querer nos referir a uma parte de um array. Fariamos isso assim:

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

assert_eq!(slice, &[2, 3]);
```

Este slice tem o tipo `&[i32]`. Ele funciona da mesma forma que os slices de string, armazenando uma referência para o primeiro elemento e um comprimento. Você usará esse tipo de slice para todos os tipos de outras coleções. Discutiremos essas coleções em detalhes quando falarmos sobre vetores no Capítulo 8.

## Resumo

Os conceitos de propriedade, empréstimo e slices garantem a segurança de memória em programas Rust durante o tempo de compilação. A linguagem Rust oferece controle sobre o uso de memória da mesma forma que outras linguagens de programação de sistemas, mas ter o proprietário dos dados automaticamente limpando esses dados quando o proprietário sai de escopo significa que você não precisa escrever e depurar código extra para obter esse controle.

A propriedade afeta muitas outras partes do Rust, por isso falaremos mais sobre esses conceitos ao longo do restante do livro. Vamos avançar para o Capítulo 5 e aprender sobre a agrupamento de pedaços de dados em uma `struct`.

[ch13]: ch13-02-iterators.html
[ch6]: ch06-02-match.html#patterns-that-bind-to-values
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[deref-coercions]: ch15-02-deref.html#implicit-deref-coercions-with-functions-and-methods
