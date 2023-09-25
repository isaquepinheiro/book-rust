## Armazenando Texto Codificado em UTF-8 com Strings

Falamos sobre strings no Capítulo 4, mas agora vamos explorá-las com mais profundidade. Novos programadores em Rust frequentemente enfrentam dificuldades com strings por três razões principais: a propensão do Rust para expor erros possíveis, as strings sendo uma estrutura de dados mais complicada do que muitos programadores imaginam, e o UTF-8. Esses fatores se combinam de uma maneira que pode parecer desafiadora quando você está vindo de outras linguagens de programação.

Discutimos as strings no contexto das coleções porque as strings são implementadas como uma coleção de bytes, com alguns métodos para fornecer funcionalidades úteis quando esses bytes são interpretados como texto. Nesta seção, falaremos sobre as operações em `String` que todo tipo de coleção possui, como criar, atualizar e ler. Também discutiremos as maneiras pelas quais `String` é diferente das outras coleções, principalmente como a indexação em uma `String` é complicada devido às diferenças na forma como as pessoas e os computadores interpretam os dados de `String`.

### O Que É uma String?

Primeiro, vamos definir o que queremos dizer com o termo *string*. Rust possui apenas um tipo de string no núcleo da linguagem, que é a *string slice* `str` geralmente vista em sua forma emprestada `&str`. No Capítulo 4, falamos sobre *string slices*, que são referências a algum dado de string codificado em UTF-8 armazenado em outro lugar. As literais de string, por exemplo, são armazenadas no binário do programa e, portanto, são *string slices*.

O tipo `String`, fornecido pela biblioteca padrão do Rust em vez de estar codificado no núcleo da linguagem, é um tipo de string mutável, crescível, de propriedade e codificado em UTF-8. Quando os programadores Rust se referem a "strings" em Rust, eles podem estar se referindo tanto ao tipo `String` quanto ao *string slice* `&str`, e não apenas a um desses tipos. Embora esta seção seja principalmente sobre `String`, ambos os tipos são amplamente usados na biblioteca padrão do Rust, e tanto `String` quanto *string slices* são codificados em UTF-8.

### Criando uma Nova String

Muitas das mesmas operações disponíveis com `Vec<T>` também estão disponíveis com `String`, porque `String` na verdade é implementada como um invólucro em torno de um vetor de bytes com algumas garantias, restrições e capacidades extras. Um exemplo de uma função que funciona da mesma forma com `Vec<T>` e `String` é a função `new` para criar uma instância, mostrada no Exemplo 8-11.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-11/src/main.rs:here}}
```

<span class="caption">Exemplo 8-11: Criando uma nova `String` vazia</span>

Esta linha cria uma nova string vazia chamada `s`, na qual podemos então carregar dados. Muitas vezes, teremos alguns dados iniciais que desejamos iniciar a string. Para isso, usamos o método `to_string`, que está disponível em qualquer tipo que implemente o traço `Display`, como literais de string fazem. O Exemplo 8-12 mostra dois exemplos.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-12/src/main.rs:here}}
```

<span class="caption">Exemplo 8-12: Usando o método `to_string` para criar uma `String` a partir de um literal de string</span>

Este código cria uma string contendo `initial contents`.

Também podemos usar a função `String::from` para criar uma `String` a partir de um literal de string. O código no Exemplo 8-13 é equivalente ao código do Exemplo 8-12 que usa `to_string`.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-13/src/main.rs:here}}
```

<span class="caption">Exemplo 8-13: Usando a função `String::from` para criar uma `String` a partir de um literal de string</span>

Devido ao fato de as strings serem usadas para tantas coisas diferentes, temos à disposição muitas APIs genéricas para strings, o que nos fornece várias opções. Algumas delas podem parecer redundantes, mas todas têm seu propósito! Neste caso, `String::from` e `to_string` fazem a mesma coisa, então a escolha entre eles é uma questão de estilo e legibilidade.

Lembre-se de que as strings são codificadas em UTF-8, portanto, podemos incluir qualquer dado devidamente codificado nelas, como mostrado no Exemplo 8-14.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-14/src/main.rs:here}}
```

<span class="caption">Exemplo 8-14: Armazenando saudações em diferentes idiomas em strings</span>

Todos esses são valores válidos de `String`.

### Atualizando uma String

Uma `String` pode aumentar de tamanho e seu conteúdo pode mudar, assim como o conteúdo de um `Vec<T>`, se você adicionar mais dados a ela. Além disso, você pode convenientemente usar o operador `+` ou a macro `format!` para concatenar valores `String`.

#### Acrescentando a uma String com `push_str` e `push`

Podemos aumentar uma `String` usando o método `push_str` para acrescentar uma *string slice*, como mostrado no Exemplo 8-15.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-15/src/main.rs:here}}
```

<span class="caption">Exemplo 8-15: Acrescentando um *string slice* a uma `String` usando o método `push_str`</span>

Após essas duas linhas, `s` conterá `foobar`. O método `push_str` recebe um *string slice* porque não necessariamente queremos tomar posse do parâmetro. Por exemplo, no código do Exemplo 8-16, queremos ser capazes de usar `s2` após anexar seu conteúdo a `s1`.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-16/src/main.rs:here}}
```

<span class="caption">Exemplo 8-16: Usando um *string slice* após anexar seu conteúdo a uma `String`</span>

Se o método `push_str` tomasse posse de `s2`, não poderíamos imprimir seu valor na última linha. No entanto, este código funciona como esperado!

O método `push` recebe um único caractere como parâmetro e o adiciona à `String`. O Exemplo 8-17 adiciona a letra "l" a uma `String` usando o método `push`.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-17/src/main.rs:here}}
```

<span class="caption">Exemplo 8-17: Adicionando um caractere a um valor `String` usando `push`</span>

Como resultado, `s` conterá `lol`.

#### Concatenação com o Operador `+` ou a Macro `format!`

Frequentemente, você desejará combinar duas strings existentes. Uma maneira de fazer isso é usar o operador `+`, como mostrado no Exemplo 8-18.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-18/src/main.rs:here}}
```

<span class="caption">Exemplo 8-18: Usando o operador `+` para combinar dois valores `String` em um novo valor `String`</span>

A string `s3` conterá `Hello, world!`. O motivo pelo qual `s1` não é mais válido após a adição, e o motivo pelo qual usamos uma referência a `s2`, tem a ver com a assinatura do método chamado quando usamos o operador `+`. O operador `+` usa o método `add`, cuja assinatura se parece com isso:

```rust,ignore
fn add(self, s: &str) -> String {
```

Na biblioteca padrão, você verá o `add` definido usando genéricos e tipos associados. Aqui, substituímos por tipos concretos, que é o que acontece quando chamamos este método com valores `String`. Discutiremos genéricos no Capítulo 10. Esta assinatura nos dá as pistas de que precisamos para entender as partes complicadas do operador `+`.

Primeiro, `s2` tem um `&`, o que significa que estamos adicionando uma *referência* da segunda string à primeira. Isso ocorre por causa do parâmetro `s` na função `add`: só podemos adicionar um `&str` a uma `String`; não podemos adicionar dois valores `String` juntos. Mas espere - o tipo de `&s2` é `&String`, não `&str`, como especificado no segundo parâmetro para `add`. Então, por que o Exemplo 8-18 compila?

A razão pela qual podemos usar `&s2` na chamada para `add` é que o compilador pode *coercionar* o argumento `&String` em um `&str`. Quando chamamos o método `add`, o Rust usa uma *coerção de desreferência*, que aqui transforma `&s2` em `&s2[..]`. Discutiremos a coerção de desreferência com mais detalhes no Capítulo 15. Como o `add` não toma posse do parâmetro `s`, `s2` ainda será uma `String` válida após essa operação.

Segundo, podemos ver na assinatura que o `add` toma posse de `self`, porque `self` *não* tem um `&`. Isso significa que `s1` no Exemplo 8-18 será movido para a chamada de `add` e não será mais válido após isso. Portanto, embora `let s3 = s1 + &s2;` pareça que copiará ambas as strings e criará uma nova, esta declaração na verdade toma posse de `s1`, acrescenta uma cópia do conteúdo de `s2` e depois retorna a propriedade do resultado. Em outras palavras, parece que está fazendo muitas cópias, mas não está; a implementação é mais eficiente do que a cópia.

Se precisarmos concatenar várias strings, o comportamento do operador `+` pode se tornar difícil de gerenciar:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/no-listing-01-concat-multiple-strings/src/main.rs:here}}
```

Neste ponto, `s` será `tic-tac-toe`. Com todos os caracteres `+` e `"` fica difícil entender o que está acontecendo. Para combinações de strings mais complicadas, podemos, em vez disso, usar a macro `format!`:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/no-listing-02-format/src/main.rs:here}}
```

Este código também define `s` como `tic-tac-toe`. A macro `format!` funciona como `println!`, mas em vez de imprimir a saída na tela, ela retorna uma `String` com o conteúdo. A versão do código que utiliza `format!` é muito mais fácil de ler, e o código gerado pela macro `format!` utiliza referências para que essa chamada não tome posse de nenhum de seus parâmetros.

### Acessando Strings por Índice

Em muitas outras linguagens de programação, acessar caracteres individuais em uma string, referenciando-os por índice, é uma operação válida e comum. No entanto, se você tentar acessar partes de uma `String` usando a sintaxe de indexação em Rust, receberá um erro. Considere o código inválido no Exemplo 8-19.

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-19/src/main.rs:here}}
```

<span class="caption">Exemplo 8-19: Tentando usar a sintaxe de indexação com uma String</span>

Esse código resultará no seguinte erro:

```console
{{#include ../listings/ch08-common-collections/listing-08-19/output.txt}}
```

O erro e a observação contam a história: As strings em Rust não suportam indexação. Mas por que não? Para responder a essa pergunta, precisamos discutir como Rust armazena strings na memória.

#### Representação Interna

Uma `String` é um invólucro sobre um `Vec<u8>`. Vamos examinar algumas de nossas strings de exemplo codificadas corretamente em UTF-8 do Exemplo 8-14. Primeiro, esta:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-14/src/main.rs:spanish}}
```

Neste caso, `len` será 4, o que significa que o vetor que armazena a string "Hola" possui 4 bytes de comprimento. Cada uma dessas letras ocupa 1 byte quando codificada em UTF-8. A linha a seguir, no entanto, pode surpreendê-lo. (Observe que esta string começa com a letra cirílica maiúscula Ze, não com o número 3.)

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-14/src/main.rs:russian}}
```

Perguntado qual é o comprimento da string, você pode dizer 12. Na verdade, a resposta do Rust é 24: esse é o número de bytes necessários para codificar "Здравствуйте" em UTF-8, porque cada valor escalar Unicode nessa string ocupa 2 bytes de armazenamento. Portanto, um índice nos bytes da string nem sempre corresponderá a um valor escalar Unicode válido. Para demonstrar, considere este código Rust inválido:

```rust,ignore,does_not_compile
let hello = "Здравствуйте";
let answer = &hello[0];
```

Você já sabe que `answer` não será `З`, a primeira letra. Quando codificado em UTF-8, o primeiro byte de `З` é `208` e o segundo é `151`, então pareceria que `answer` deveria ser `208`, mas `208` não é um caractere válido por si só. Retornar `208` provavelmente não é o que um usuário desejaria se pedisse a primeira letra dessa string; no entanto, esses são os únicos dados que o Rust tem no índice de byte 0. Geralmente, os usuários não desejam o valor do byte retornado, mesmo se a string contiver apenas letras latinas: se `&"hello"[0]` fosse um código válido que retornasse o valor do byte, ele retornaria `104`, não `h`.

A resposta, portanto, é que, para evitar retornar um valor inesperado e causar bugs que podem não ser descobertos imediatamente, o Rust não compila esse código e previne mal-entendidos logo no início do processo de desenvolvimento.

#### Bytes e Valores Escalares e Aglomerados de Grafemas! Oh, Meu Deus!

Outro ponto sobre UTF-8 é que existem, na verdade, três maneiras relevantes de
analisar strings do ponto de vista do Rust: como bytes, valores escalares e aglomerados de grafemas (a coisa mais próxima do que chamaríamos de *letras*).

Se olharmos para a palavra em Hindi "नमस्ते" escrita no script Devanagari, ela é
armazenada como um vetor de valores `u8` que se parece com isso:

```text
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

São 18 bytes e é assim que os computadores armazenam esses dados. Se olharmos para eles como valores escalares Unicode, que são o que o tipo `char` do Rust representa, esses bytes se parecem com isso:

```text
['न', 'म', 'स', '्', 'त', 'े']
```

Existem seis valores `char` aqui, mas o quarto e o sexto não são letras: são diacríticos que não fazem sentido por si só. Finalmente, se os considerarmos como aglomerados de grafemas, obteríamos o que uma pessoa chamaria de quatro letras que compõem a palavra em Hindi:

```text
["न", "म", "स्", "ते"]
```

Rust oferece diferentes maneiras de interpretar os dados brutos de strings que os computadores armazenam, para que cada programa possa escolher a interpretação de que precisa, independentemente do idioma humano em que os dados estejam.

Uma razão final pela qual o Rust não nos permite indexar uma `String` para obter um caractere é que as operações de indexação são esperadas para sempre ter um tempo constante (O(1)). Mas não é possível garantir esse desempenho com uma `String`, porque o Rust teria que percorrer o conteúdo desde o início até o índice para determinar quantos caracteres válidos havia.

### Cortando Strings

Fazer indexação em uma string muitas vezes não é uma boa ideia porque não está claro qual deve ser o tipo de retorno da operação de indexação da string: um valor de byte, um caractere, um aglomerado de grafemas ou uma fatia de string. Se você realmente precisa usar índices para criar fatias de string, o Rust pede que você seja mais específico.

Em vez de usar `[]` com um único número para indexação, você pode usar `[]` com um intervalo para criar uma fatia de string contendo bytes específicos:

```rust
let hello = "Здравствуйте";

let s = &hello[0..4];
```

Aqui, `s` será um `&str` que contém os primeiros 4 bytes da string.
Anteriormente, mencionamos que cada um desses caracteres tinha 2 bytes, o que significa que `s` será `Зд`.

Se tentássemos cortar apenas parte dos bytes de um caractere com algo como `&hello[0..1]`, o Rust geraria um erro em tempo de execução da mesma maneira que se um índice inválido fosse acessado em um vetor:

```console
{{#include ../listings/ch08-common-collections/output-only-01-not-char-boundary/output.txt}}
```

Você deve usar intervalos (ranges) com cuidado para criar fatias de strings, pois fazê-lo pode causar a falha do seu programa.

### Métodos para Iterar sobre Strings

A melhor maneira de operar em partes de strings é ser explícito sobre se você deseja caracteres ou bytes. Para valores escalares Unicode individuais, use o método `chars`. Chamar `chars` em "Зд" separa e retorna dois valores do tipo `char`, e você pode iterar sobre o resultado para acessar cada elemento:

```rust
for c in "Зд".chars() {
    println!("{c}");
}
```

This code will print the following:

```text
З
д
```

Alternativamente, o método `bytes` retorna cada byte bruto, o que pode ser apropriado para o seu domínio:

```rust
for b in "Зд".bytes() {
    println!("{b}");
}
```

Este código imprimirá os quatro bytes que compõem esta string:

```text
208
151
208
180
```

Mas lembre-se de que valores escalares Unicode válidos podem ser compostos por mais de 1 byte.

Obter grupos de grafemas de strings, como no caso do script Devanagari, é complexo, portanto, essa funcionalidade não é fornecida pela biblioteca padrão. Existem crates disponíveis em [crates.io](https://crates.io/) se essa for a funcionalidade de que você precisa.

Para resumir, strings são complicadas. Linguagens de programação diferentes fazem escolhas diferentes sobre como apresentar essa complexidade ao programador. Rust optou por tornar o tratamento correto de dados `String` o comportamento padrão para todos os programas Rust, o que significa que os programadores precisam pensar mais na manipulação de dados UTF-8 desde o início. Essa compensação expõe mais da complexidade das strings do que é aparente em outras linguagens de programação, mas evita que você tenha que lidar com erros envolvendo caracteres não ASCII mais tarde no ciclo de vida do seu desenvolvimento.

A boa notícia é que a biblioteca padrão oferece muitas funcionalidades construídas com base nos tipos `String` e `&str` para ajudar a lidar corretamente com essas situações complexas. Certifique-se de verificar a documentação para métodos úteis, como `contains` para busca em uma string e `replace` para substituir partes de uma string por outra string.

Vamos mudar para algo um pouco menos complexo: mapas de hash!
