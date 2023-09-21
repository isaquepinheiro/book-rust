## O que é a Propriedade?

A *propriedade* (ownership) é um conjunto de regras que governa como um programa Rust gerencia a memória. Todos os programas precisam gerenciar a forma como utilizam a memória de um computador enquanto estão em execução. Algumas linguagens possuem coletor de lixo (garbage collector) que regularmente procura por memória que não está mais em uso enquanto o programa é executado; em outras linguagens, o programador deve alocar e liberar a memória explicitamente. O Rust utiliza uma terceira abordagem: a memória é gerenciada por meio de um sistema de propriedade com um conjunto de regras que o compilador verifica. Se alguma das regras for violada, o programa não será compilado. Nenhuma das características da propriedade irá tornar o seu programa mais lento durante a execução.

Como a propriedade é um conceito novo para muitos programadores, leva algum tempo para se acostumar com ela. A boa notícia é que quanto mais experiente você se tornar com o Rust e as regras do sistema de propriedade, mais fácil será desenvolver naturalmente código que seja seguro e eficiente. Continue praticando!

Quando você entender a propriedade, terá uma base sólida para compreender as características que tornam o Rust único. Neste capítulo, você aprenderá sobre a propriedade trabalhando com exemplos que se concentram em uma estrutura de dados muito comum: strings.

> ### A Pilha e o Heap
>
> Muitas linguagens de programação não exigem que você pense muito na pilha (stack) e no heap (heap) com frequência. Mas em uma linguagem de programação de sistemas como o Rust, se um valor está na pilha ou no heap afeta como a linguagem se comporta e por que você deve tomar certas decisões. Partes da propriedade serão descritas em relação à pilha e ao heap mais adiante neste capítulo, então aqui está uma explicação breve para preparação.
>
> Tanto a pilha quanto o heap são partes da memória disponíveis para seu código usar em tempo de execução, mas eles têm estruturas diferentes. A pilha armazena valores na ordem em que os recebe e remove os valores na ordem oposta. Isso é chamado de *último a entrar, primeiro a sair*. Pense em uma pilha de pratos: quando você adiciona mais pratos, você os coloca no topo da pilha, e quando você precisa de um prato, pega um do topo. Adicionar ou remover pratos do meio ou da base não funcionaria tão bem! Adicionar dados é chamado de *empilhar na pilha* (push onto the stack), e remover dados é chamado de *desempilhar da pilha* (pop off the stack). Todos os dados armazenados na pilha devem ter um tamanho conhecido e fixo. Dados com um tamanho desconhecido na compilação ou um tamanho que pode mudar devem ser armazenados no heap.
>
> O heap é menos organizado: quando você coloca dados no heap, você solicita uma certa quantidade de espaço. O alocador de memória encontra um local vazio no heap que seja grande o suficiente, marca-o como em uso e retorna um *ponteiro*, que é o endereço desse local. Esse processo é chamado de *alocação no heap* e às vezes é abreviado apenas como *alocação* (colocar valores na pilha não é considerado alocação). Como o ponteiro para o heap tem um tamanho conhecido e fixo, você pode armazenar o ponteiro na pilha, mas quando desejar os dados reais, deve seguir o ponteiro. Pense em estar em um restaurante. Quando você entra, informa o número de pessoas em seu grupo, e o anfitrião encontra uma mesa vazia que acomode todos e os leva até lá. Se alguém do seu grupo chegar atrasado, pode perguntar onde vocês foram alocados para encontrá-los.

> Empilhar na pilha é mais rápido do que alocar no heap, porque o alocador nunca precisa procurar um local para armazenar novos dados; esse local está sempre no topo da pilha. Em comparação, a alocação de espaço no heap requer mais trabalho, porque o alocador deve primeiro encontrar um espaço grande o suficiente para armazenar os dados e depois realizar o registro para preparar a próxima alocação.
>
> Acesso a dados no heap é mais lento do que o acesso a dados na pilha porque você precisa seguir um ponteiro para chegar lá. Processadores contemporâneos são mais rápidos se pularem menos na memória. Continuando com a analogia, considere um servidor em um restaurante recebendo pedidos de várias mesas. É mais eficiente pegar todos os pedidos de uma mesa antes de passar para a próxima mesa. Pegar um pedido da mesa A, depois um pedido da mesa B, depois um da mesa A novamente e depois um da mesa B novamente seria um processo muito mais lento. Da mesma forma, um processador pode executar seu trabalho melhor se trabalhar com dados que estão próximos a outros dados (como na pilha) em vez de mais distantes (como pode estar no heap).

> Quando seu código chama uma função, os valores passados para a função (incluindo, potencialmente, ponteiros para dados no heap) e as variáveis locais da função são colocados na pilha. Quando a função termina, esses valores são retirados da pilha.

> Acompanhar quais partes do código estão usando quais dados no heap, minimizar a quantidade de dados duplicados no heap e limpar os dados não utilizados no heap para que você não fique sem espaço são todos problemas que a propriedade aborda. Uma vez que você entenda a propriedade, não precisará pensar na pilha e no heap com muita frequência, mas saber que o objetivo principal da propriedade é gerenciar dados no heap pode ajudar a explicar por que ela funciona da maneira que funciona.

### Regras de Propriedade

Primeiro, vamos dar uma olhada nas regras de propriedade. Mantenha essas regras em mente enquanto trabalhamos nos exemplos que as ilustram:

* Cada valor em Rust tem um *dono*.
* Pode haver apenas um dono por vez.
* Quando o dono sai de escopo, o valor será liberado.

### Escopo de Variáveis

Agora que passamos pela sintaxe básica do Rust, não incluiremos mais todo o código `fn main() {` nos exemplos. Portanto, se você estiver acompanhando, certifique-se de colocar os seguintes exemplos manualmente dentro de uma função `main`. Como resultado, nossos exemplos serão um pouco mais concisos, permitindo que nos concentremos nos detalhes reais em vez do código de inicialização.

Como primeiro exemplo de propriedade (ownership), vamos analisar o *escopo* de algumas variáveis. Um escopo é a faixa dentro de um programa para a qual um item é válido. Considere a seguinte variável:

```rust
let s = "hello";
```

A variável `s` se refere a um literal de string, onde o valor da string está codificado diretamente no texto do nosso programa. A variável é válida a partir do ponto em que é declarada até o final do *escopo* atual. O Listagem 4-1 mostra um programa com comentários que indicam onde a variável `s` seria válida.

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-01/src/main.rs:here}}
```

<span class="caption">Listagem 4-1: Uma variável e o escopo em que é válida</span>

Em outras palavras, existem dois pontos importantes no tempo aqui:

- Quando `s` entra no *escopo*, ele é válido.
- Ele permanece válido até sair do *escopo*.

Neste ponto, a relação entre escopos e quando as variáveis são válidas é semelhante à de outras linguagens de programação. Agora, vamos aprofundar esse entendimento introduzindo o tipo `String`.

### O Tipo `String`

Para ilustrar as regras de propriedade, precisamos de um tipo de dados mais complexo do que os que abordamos na seção [“Tipos de Dados”][data-types]<!-- ignore --> do Capítulo 3. Os tipos abordados anteriormente têm um tamanho conhecido, podem ser armazenados na pilha e removidos da pilha quando seu escopo termina, e podem ser copiados rapidamente e trivialmente para criar uma nova instância independente se outra parte do código precisar usar o mesmo valor em um escopo diferente. Mas queremos analisar dados armazenados no heap e explorar como o Rust sabe quando limpar esses dados, e o tipo `String` é um ótimo exemplo.

Vamos nos concentrar nas partes de `String` que se relacionam com a propriedade. Esses aspectos também se aplicam a outros tipos de dados complexos, sejam eles fornecidos pela biblioteca padrão ou criados por você. Discutiremos `String` com mais profundidade no [Capítulo 8][ch8]<!-- ignore -->.

Já vimos literais de string, onde um valor de string é codificado diretamente em nosso programa. Literais de string são convenientes, mas não são adequados para todas as situações em que podemos querer usar texto. Uma razão é que eles são imutáveis. Outra razão é que nem todo valor de string pode ser conhecido quando escrevemos nosso código: por exemplo, e se quisermos receber entrada do usuário e armazená-la? Para essas situações, o Rust possui um segundo tipo de string, `String`. Este tipo gerencia dados alocados no heap e, como tal, é capaz de armazenar uma quantidade de texto que é desconhecida para nós em tempo de compilação. Você pode criar um `String` a partir de um literal de string usando a função `from`, assim:

```rust
let s = String::from("hello");
```

O operador de dois pontos duplos `::` nos permite colocar esta função `from` sob o espaço de nomes do tipo `String`, em vez de usar algum tipo de nome como `string_from`. Vamos discutir essa sintaxe com mais detalhes na seção [“Sintaxe de Método”][method-syntax]<!-- ignore --> do Capítulo 5 e quando falarmos sobre o espaço de nomes com módulos em [“Caminhos para Referenciar um Item na Árvore de Módulos”][paths-module-tree]<!-- ignore --> no Capítulo 7.

Esse tipo de string *pode* ser mutado:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-01-can-mutate-string/src/main.rs:here}}
```

Então, qual é a diferença aqui? Por que `String` pode ser mutado, mas literais não podem? A diferença está em como esses dois tipos lidam com a memória.

### Memória e Alocação

No caso de um literal de string, conhecemos o conteúdo em tempo de compilação, então o texto é codificado diretamente no executável final. É por isso que literais de string são rápidos e eficientes. No entanto, essas propriedades derivam apenas da imutabilidade do literal de string. Infelizmente, não podemos incluir um bloco de memória no binário para cada pedaço de texto cujo tamanho é desconhecido em tempo de compilação e cujo tamanho pode mudar durante a execução do programa.

Com o tipo `String`, para suportar um pedaço de texto mutável e expansível, precisamos alocar uma quantidade de memória no heap, desconhecida em tempo de compilação, para armazenar o conteúdo. Isso significa:

* A memória deve ser solicitada do alocador de memória em tempo de execução.
* Precisamos de uma maneira de devolver essa memória ao alocador quando terminarmos de usar nossa `String`.

A primeira parte é feita por nós: quando chamamos `String::from`, sua implementação solicita a memória de que precisa. Isso é praticamente universal em linguagens de programação.

No entanto, a segunda parte é diferente. Em linguagens com um *coletor de lixo (GC)*, o GC rastreia e libera a memória que não está mais em uso, e não precisamos pensar nisso. Na maioria das linguagens sem GC, é nossa responsabilidade identificar quando a memória não está mais sendo usada e chamar código para liberá-la explicitamente, assim como fizemos para solicitá-la. Fazer isso corretamente historicamente tem sido um problema de programação difícil. Se esquecermos, desperdiçaremos memória. Se o fizermos muito cedo, teremos uma variável inválida. Se o fizermos duas vezes, isso também é um bug. Precisamos emparelhar exatamente uma `alocação` com exatamente uma `liberação`.

O Rust segue um caminho diferente: a memória é devolvida automaticamente quando a variável que a possui sai de escopo. Aqui está uma versão do nosso exemplo de escopo do Capítulo 4 usando um `String` em vez de um literal de string:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-02-string-scope/src/main.rs:here}}
```

Existe um ponto natural em que podemos devolver a memória que nossa `String` precisa ao alocador: quando `s` sai de escopo. Quando uma variável sai de escopo, o Rust chama uma função especial para nós. Essa função é chamada de [`drop`][drop]<!-- ignore -->, e é onde o autor de `String` pode colocar o código para devolver a memória. O Rust chama o `drop` automaticamente no fechamento da chave.

> Observação: Em C++, esse padrão de desalocação de recursos no final da vida útil de um item é às vezes chamado de *Resource Acquisition Is Initialization (RAII)*. A função `drop` no Rust será familiar para você se você já usou padrões RAII.

Esse padrão tem um impacto profundo na forma como o código Rust é escrito. Pode parecer simples agora, mas o comportamento do código pode ser inesperado em situações mais complicadas, quando queremos que várias variáveis usem os dados que alocamos no heap. Vamos explorar algumas dessas situações agora.

<!-- Old heading. Do not remove or links may break. -->
<a id="ways-variables-and-data-interact-move"></a>

#### Variables and Data Interacting with Move

Múltiplas variáveis podem interagir com os mesmos dados de maneiras diferentes em Rust. Vamos ver um exemplo usando um número inteiro no Exemplo 4-2.

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-02/src/main.rs:here}}
```

<span class="caption">Listagem 4-2: Atribuindo o valor inteiro da variável `x` a `y``</span>

Provavelmente, podemos adivinhar o que está acontecendo aqui: "vincule o valor `5` a `x`; em seguida, faça uma cópia do valor em `x` e vincule-o a `y`". Agora temos duas variáveis, `x` e `y`, e ambas são iguais a `5`. Isso é de fato o que está acontecendo, porque os inteiros são valores simples com um tamanho conhecido e fixo, e esses dois valores `5` são colocados na pilha.

Agora, vamos dar uma olhada na versão com `String`:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-03-string-move/src/main.rs:here}}
```

Isso parece muito semelhante, então poderíamos supor que a maneira como funciona seria a mesma: ou seja, a segunda linha faria uma cópia do valor em `s1` e o vincularia a `s2`. Mas isso não é exatamente o que acontece.

Dê uma olhada na Figura 4-1 para ver o que está acontecendo com `String` por baixo dos panos. Um `String` é composto por três partes, mostradas à esquerda: um ponteiro para a memória que contém o conteúdo da string, um comprimento e uma capacidade. Esse grupo de dados é armazenado na pilha. À direita está a memória no heap que contém o conteúdo.

![Figura 4-1: Representação em memória de uma `String` que contém o valor `"hello"` vinculado a `s1`](img/trpl04-01.svg)

<span class="caption">Figura 4-1: Representação em memória de uma `String` que contém o valor `"hello"` vinculado a `s1`</span>

O comprimento representa a quantidade de memória, em bytes, que o conteúdo da `String` está usando atualmente. A capacidade é a quantidade total de memória, em bytes, que a `String` recebeu do alocador. A diferença entre o comprimento e a capacidade é importante, mas não neste contexto, então, por enquanto, é bom ignorar a capacidade.

Quando atribuímos `s1` a `s2`, os dados da `String` são copiados, o que significa que copiamos o ponteiro, o comprimento e a capacidade que estão na pilha. Não copiamos os dados no heap para os quais o ponteiro faz referência. Em outras palavras, a representação dos dados na memória se parece com a Figura 4-2.

![Figura 4-2: Representação em memória da variável `s2` que possui uma cópia do ponteiro, comprimento e capacidade de `s1`](img/trpl04-02.svg)

<span class="caption">Figura 4-2: Representação em memória da variável `s2` que possui uma cópia do ponteiro, comprimento e capacidade de `s1`</span>

A representação *não* se parece com a Figura 4-3, que é como a memória se pareceria se o Rust copiasse também os dados no heap. Se o Rust fizesse isso, a operação `s2 = s1` poderia ser muito cara em termos de desempenho em tempo de execução se os dados no heap fossem grandes.

![Figura 4-3: Outra possibilidade do que `s2 = s1` poderia fazer se o Rust também copiasse os dados no heap](img/trpl04-03.svg)

<span class="caption">Figura 4-3: Outra possibilidade do que `s2 = s1` poderia fazer se o Rust também copiasse os dados no heap</span>

Anteriormente, mencionamos que quando uma variável sai de escopo, o Rust chama automaticamente a função `drop` e limpa a memória do heap para essa variável. No entanto, a Figura 4-2 mostra ambos os ponteiros de dados apontando para a mesma localização. Isso é um problema: quando `s2` e `s1` saírem de escopo, ambos tentarão liberar a mesma memória. Isso é conhecido como um erro de *dupla liberação* e é um dos bugs de segurança de memória que mencionamos anteriormente. Liberar a memória duas vezes pode levar à corrupção de memória, o que pode potencialmente resultar em vulnerabilidades de segurança.

Para garantir a segurança de memória, após a linha `let s2 = s1;`, o Rust considera `s1` como não mais válida. Portanto, o Rust não precisa liberar nada quando `s1` sai de escopo. Veja o que acontece quando você tenta usar `s1` após criar `s2`; não funcionará:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-04-cant-use-after-move/src/main.rs:here}}
```

Você receberá um erro como este porque o Rust impede que você use a referência invalidada:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-04-cant-use-after-move/output.txt}}
```

Se você já ouviu os termos *cópia rasa* (*shallow copy*) e *cópia profunda* (*deep copy*) ao trabalhar com outras linguagens, o conceito de copiar o ponteiro, comprimento e capacidade sem copiar os dados provavelmente parece uma cópia rasa. Mas, como o Rust também invalida a primeira variável, em vez de ser chamado de cópia rasa, é conhecido como uma *movimentação* (*move*). Neste exemplo, diríamos que `s1` foi *movida* para `s2`. Portanto, o que realmente acontece é mostrado na Figura 4-4.

![Três tabelas: as tabelas s1 e s2 representam essas strings na pilha, respectivamente, e ambas apontam para os mesmos dados de string no heap. A tabela s1 está esmaecida porque s1 não é mais válida; apenas s2 pode ser usada para acessar os dados do heap.](img/trpl04-04.svg)

<span class="caption">Figura 4-4: Representação em memória após a movimentação de `s1` para `s2`</span>

<span class="caption">Figura 4-4: Representação em memória após `s1` ter sido invalidada</span>

That solves our problem! With only `s2` valid, when it goes out of scope it
alone will free the memory, and we’re done.

Além disso, existe uma escolha de design que é implicada por isso: o Rust nunca criará automaticamente cópias "profundas" dos seus dados. Portanto, qualquer cópia *automática* pode ser considerada como de baixo custo em termos de desempenho em tempo de execução.

<!-- Old heading. Do not remove or links may break. -->
<a id="ways-variables-and-data-interact-clone"></a>

#### Variáveis e Dados Interagindo com Clone

Se *quisermos* fazer uma cópia profunda dos dados no heap da `String`, não apenas dos dados na pilha, podemos usar um método comum chamado `clone`. Discutiremos a sintaxe de métodos no Capítulo 5, mas como os métodos são uma característica comum em muitas linguagens de programação, você provavelmente já os viu antes.

Aqui está um exemplo do método `clone` em ação:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-05-clone/src/main.rs:here}}
```

Isso funciona muito bem e produz explicitamente o comportamento mostrado na Figura 4-3, onde os dados no heap *são* copiados.

Quando você vê uma chamada para `clone`, sabe que algum código arbitrário está sendo executado e que esse código pode ser caro em termos de desempenho. É um indicador visual de que algo diferente está acontecendo.

#### Dados Apenas na Pilha: Copy

Há outro detalhe que ainda não discutimos. Este código que usa inteiros - parte do qual foi mostrado na Listagem 4-2 - funciona e é válido:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-06-copy/src/main.rs:here}}
```

Mas este código parece contradizer o que acabamos de aprender: não temos uma chamada para `clone`, mas `x` ainda é válido e não foi movido para `y`.

A razão para isso é que tipos como inteiros, que têm um tamanho conhecido em tempo de compilação, são armazenados inteiramente na pilha, então as cópias dos valores reais são rápidas de fazer. Isso significa que não há motivo para impedir que `x` seja válido depois de criarmos a variável `y`. Em outras palavras, não há diferença entre cópia profunda e rasa aqui, portanto, chamar `clone` não faria nada diferente da cópia rasa usual, e podemos deixá-lo de fora.

O Rust possui uma anotação especial chamada trait `Copy` que podemos colocar em tipos armazenados na pilha, como os inteiros (falaremos mais sobre traits no [Capítulo 10][traits]<!-- ignore -->). Se um tipo implementa o trait `Copy`, as variáveis que o utilizam não são movidas, mas são copiadas trivialmente, o que as mantém válidas após a atribuição a outra variável.

O Rust não nos permite anotar um tipo com `Copy` se o tipo, ou qualquer uma de suas partes, tiver implementado o trait `Drop`. Se o tipo precisar de algo especial para acontecer quando o valor sair de escopo e adicionarmos a anotação `Copy` a esse tipo, receberemos um erro de compilação. Para aprender como adicionar a anotação `Copy` ao seu tipo para implementar o trait, consulte ["Traits Deriváveis"][derivable-traits]<!-- ignore --> no Apêndice C.

Então, quais tipos implementam o trait `Copy`? Você pode verificar a documentação do tipo em questão para ter certeza, mas, como regra geral, qualquer grupo de valores escalares simples pode implementar o `Copy`, e nada que exija alocação ou seja alguma forma de recurso pode implementar o `Copy`. Aqui estão alguns dos tipos que implementam o `Copy`:

* Todos os tipos de inteiros, como `u32`.
* O tipo booleano, `bool`, com os valores `true` e `false`.
* Todos os tipos de ponto flutuante, como `f64`.
* O tipo de caractere, `char`.
* Tuplas, se contiverem apenas tipos que também implementam o `Copy`. Por exemplo, `(i32, i32)` implementa o `Copy`, mas `(i32, String)` não implementa.

### Propriedade e Funções

Os mecanismos de passagem de um valor para uma função são semelhantes aos da atribuição de um valor a uma variável. Passar uma variável para uma função moverá ou copiará, assim como a atribuição faz. O Exemplo 4-3 possui um exemplo com algumas anotações mostrando onde as variáveis entram e saem de escopo.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-03/src/main.rs}}
```

<span class="caption">Exemplo 4-3: Funções com propriedade e escopo (anotado)</span>

Se tentássemos usar `s` após a chamada para `takes_ownership`, o Rust lançaria um erro de compilação. Essas verificações estáticas nos protegem contra erros. Tente adicionar código à função `main` que utiliza `s` e `x` para ver onde você pode usá-los e onde as regras de propriedade impedem que você o faça.

### Valores de Retorno e Escopo

Retornar valores também pode transferir a propriedade. O Exemplo 4-4 mostra uma função que retorna algum valor, com anotações semelhantes às do Exemplo 4-3.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-04/src/main.rs}}
```

<span class="caption">Exemplo 4-4: Transferindo a propriedade dos valores de retorno</span>

A propriedade de uma variável segue o mesmo padrão toda vez: atribuir um valor a outra variável a move. Quando uma variável que inclui dados no heap sai de escopo, o valor será liberado pelo `drop` a menos que a propriedade dos dados tenha sido transferida para outra variável.

Embora isso funcione, assumir a propriedade e depois devolvê-la a cada função é um pouco tedioso. E se quisermos permitir que uma função use um valor sem assumir a propriedade? É bastante irritante que tudo o que passarmos também precise ser devolvido se quisermos usá-lo novamente, além de quaisquer dados resultantes do corpo da função que também possamos querer retornar.

Rust nos permite retornar múltiplos valores usando uma tupla, como mostrado no Exemplo 4-5.

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-05/src/main.rs}}
```

<span class="caption">Exemplo 4-5: Retornando a propriedade dos parâmetros</span>

Mas isso é muito cerimonial e requer muito trabalho para um conceito que deveria ser comum. Felizmente, o Rust possui um recurso para usar um valor sem transferir a propriedade, chamado *referências*.

[data-types]: ch03-02-data-types.html#data-types
[ch8]: ch08-02-strings.html
[traits]: ch10-02-traits.html
[derivable-traits]: appendix-03-derivable-traits.html
[method-syntax]: ch05-03-method-syntax.html#method-syntax
[paths-module-tree]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html
[drop]: ../std/ops/trait.Drop.html#tymethod.drop
