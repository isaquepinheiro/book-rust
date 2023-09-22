## Caminhos para Referenciar um Item na Árvore de Módulos

Para mostrar ao Rust onde encontrar um item em uma árvore de módulos, usamos um caminho da mesma forma que usamos um caminho ao navegar em um sistema de arquivos. Para chamar uma função, precisamos conhecer seu caminho.

Um caminho pode ter duas formas:

* Um *caminho absoluto* é o caminho completo a partir da raiz da crate; para código de uma crate externa, o caminho absoluto começa com o nome da crate, e para código da crate atual, começa com o literal `crate`.
* Um *caminho relativo* começa a partir do módulo atual e usa `self`, `super` ou um identificador no módulo atual.

Tanto caminhos absolutos quanto caminhos relativos são seguidos por um ou mais identificadores separados por dois pontos duplos (`::`).

Voltando à Listagem 7-1, digamos que queremos chamar a função `add_to_waitlist`. Isso é o mesmo que perguntar: qual é o caminho da função `add_to_waitlist`? A Listagem 7-3 contém a Listagem 7-1 com alguns dos módulos e funções removidos.

Mostraremos duas maneiras de chamar a função `add_to_waitlist` a partir de uma nova função `eat_at_restaurant` definida na raiz da crate. Esses caminhos estão corretos, mas há outro problema que impedirá que este exemplo seja compilado como está. Explicaremos o motivo em breve.

A função `eat_at_restaurant` faz parte da API pública de nossa crate de biblioteca, então a marcamos com a palavra-chave `pub`. Na seção [“Expondo Caminhos com a Palavra-Chave `pub`"][pub]<!-- ignore -->, entraremos em mais detalhes sobre `pub`.

<span class="filename">Filename: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-03/src/lib.rs}}
```

<span class="caption">Listagem 7-3: Chamando a função `add_to_waitlist` usando
caminhos absolutos e relativos</span>

A primeira vez que chamamos a função `add_to_waitlist` em `eat_at_restaurant`,
usamos um caminho absoluto. A função `add_to_waitlist` é definida na mesma crate que `eat_at_restaurant`, o que significa que podemos usar a palavra-chave `crate` para iniciar um caminho absoluto. Em seguida, incluímos cada um dos módulos sucessivos até chegarmos a `add_to_waitlist`. Você pode imaginar um sistema de arquivos com a mesma estrutura: especificaríamos o caminho `/front_of_house/hosting/add_to_waitlist` para executar o programa `add_to_waitlist`; usar o nome da `crate` para iniciar a partir da raiz da crate é como usar `/` para iniciar a partir da raiz do sistema de arquivos no seu shell.

A segunda vez que chamamos `add_to_waitlist` em `eat_at_restaurant`, usamos um caminho relativo. O caminho começa com `front_of_house`, o nome do módulo definido no mesmo nível da árvore de módulos que `eat_at_restaurant`. Aqui, o equivalente no sistema de arquivos seria usar o caminho `front_of_house/hosting/add_to_waitlist`. Começar com o nome do módulo significa que o caminho é relativo.

Escolher se usar um caminho relativo ou absoluto é uma decisão que você tomará com base em seu projeto e depende se é mais provável que você mova o código de definição de itens separadamente ou junto com o código que usa o item. Por exemplo, se movermos o módulo `front_of_house` e a função `eat_at_restaurant` para um módulo chamado `customer_experience`, precisaríamos atualizar o caminho absoluto para `add_to_waitlist`, mas o caminho relativo ainda seria válido. No entanto, se movermos a função `eat_at_restaurant` separadamente para um módulo chamado `dining`, o caminho absoluto para a chamada de `add_to_waitlist` permaneceria o mesmo, mas o caminho relativo precisaria ser atualizado. Nossa preferência geral é especificar caminhos absolutos porque é mais provável que queiramos mover as definições de código e chamadas de itens independentemente um do outro.

Vamos tentar compilar a Listagem 7-3 e descobrir por que ainda não compila! O erro que obtemos é mostrado na Listagem 7-4.

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-03/output.txt}}
```

<span class="caption">Listagem 7-4: Mensagens de erro do compilador ao compilar o código na Listagem 7-3</span>

As mensagens de erro indicam que o módulo `hosting` é privado. Em outras palavras, temos os caminhos corretos para o módulo `hosting` e a função `add_to_waitlist`, mas o Rust não nos permite usá-los porque não tem acesso às seções privadas. No Rust, todos os itens (funções, métodos, structs, enums, módulos e constantes) são privados para os módulos pai por padrão. Se você deseja tornar um item como uma função ou struct privado, você o coloca em um módulo.

Itens em um módulo pai não podem usar os itens privados dentro de módulos filhos, mas itens em módulos filhos podem usar os itens em seus módulos ancestrais. Isso ocorre porque módulos filhos encapsulam e ocultam os detalhes de implementação, mas os módulos filhos podem ver o contexto em que são definidos. Continuando com nossa metáfora, pense nas regras de privacidade como sendo como o escritório de trás de um restaurante: o que acontece lá dentro é privado para os clientes do restaurante, mas os gerentes de escritório podem ver e fazer tudo no restaurante que eles operam.

O Rust escolheu que o sistema de módulos funcione dessa maneira para que a ocultação de detalhes de implementação interna seja o padrão. Dessa forma, você sabe quais partes do código interno pode alterar sem quebrar o código externo. No entanto, o Rust lhe dá a opção de expor partes internas do código de módulos filhos para módulos ancestrais externos usando a palavra-chave `pub` para tornar um item público.

### Expondo Caminhos com a Palavra-Chave `pub`

Vamos voltar ao erro na Listagem 7-4 que nos disse que o módulo `hosting` é privado. Queremos que a função `eat_at_restaurant` no módulo pai tenha acesso à função `add_to_waitlist` no módulo filho, então marcamos o módulo `hosting` com a palavra-chave `pub`, como mostrado na Listagem 7-5.

<span class="filename">Filename: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-05/src/lib.rs}}
```

<span class="caption">Listagem 7-5: Declarando o módulo `hosting` como `pub` para
usá-lo a partir de `eat_at_restaurant`</span>

Infelizmente, o código na Listagem 7-5 ainda resulta em um erro, como mostrado na Listagem 7-6.

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-05/output.txt}}
```

<span class="caption">Listagem 7-6: Erros do compilador ao compilar o código na Listagem 7-5</span>

O que aconteceu? Adicionar a palavra-chave `pub` na frente de `mod hosting` torna o módulo público. Com essa mudança, se pudermos acessar `front_of_house`, podemos acessar `hosting`. Mas o *conteúdo* de `hosting` ainda é privado; tornar o módulo público não torna seu conteúdo público. A palavra-chave `pub` em um módulo permite apenas que o código em seus módulos ancestrais se refira a ele, mas não acesse seu código interno. Como os módulos são contêineres, não há muito que possamos fazer apenas tornando o módulo público; precisamos ir além e escolher tornar um ou mais dos itens dentro do módulo públicos também.

Os erros na Listagem 7-6 indicam que a função `add_to_waitlist` é privada. As regras de privacidade se aplicam a structs, enums, funções e métodos, bem como a módulos.

Vamos também tornar a função `add_to_waitlist` pública adicionando a palavra-chave `pub` antes de sua definição, como na Listagem 7-7.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-07/src/lib.rs}}
```

<span class="caption">Listagem 7-7: Adicionando a palavra-chave `pub` para `mod hosting`
e `fn add_to_waitlist` nos permite chamar a função a partir de
`eat_at_restaurant`</span>

Agora o código será compilado! Para entender por que adicionar a palavra-chave `pub` nos permite usar esses caminhos em `add_to_waitlist` com relação às regras de privacidade, vamos analisar os caminhos absolutos e os caminhos relativos.

No caminho absoluto, começamos com `crate`, a raiz da árvore de módulos de nossa crate. O módulo `front_of_house` é definido na raiz da crate. Embora `front_of_house` não seja público, porque a função `eat_at_restaurant` é definida no mesmo módulo que `front_of_house` (ou seja, `eat_at_restaurant` e `front_of_house` são irmãos), podemos nos referir a `front_of_house` a partir de `eat_at_restaurant`. Em seguida, temos o módulo `hosting` marcado com `pub`. Podemos acessar o módulo pai de `hosting`, então podemos acessar `hosting`. Por fim, a função `add_to_waitlist` é marcada com `pub` e podemos acessar seu módulo pai, então essa chamada de função funciona!

No caminho relativo, a lógica é a mesma do caminho absoluto, exceto pelo primeiro passo: em vez de começar a partir da raiz da crate, o caminho começa a partir de `front_of_house`. O módulo `front_of_house` é definido no mesmo módulo que `eat_at_restaurant`, então o caminho relativo a partir do módulo em que `eat_at_restaurant` está definido funciona. Em seguida, como `hosting` e `add_to_waitlist` são marcados com `pub`, o restante do caminho funciona e essa chamada de função é válida!

Se você planeja compartilhar sua crate de biblioteca para que outros projetos possam usar seu código, sua API pública é o contrato com os usuários de sua crate que determina como eles podem interagir com seu código. Existem muitas considerações sobre como gerenciar as mudanças em sua API pública para tornar mais fácil para as pessoas dependerem de sua crate. Essas considerações estão fora do escopo deste livro; se você estiver interessado neste tópico, consulte [The Rust API Guidelines][api-guidelines].

>#### Melhores Práticas para Pacotes com um Executável e uma Biblioteca
>
> Mencionamos que um pacote pode conter tanto uma raiz de caixa binária em *src/main.rs* quanto uma raiz de caixa de biblioteca em *src/lib.rs*, e ambas as caixas terão o nome do pacote por padrão. Tipicamente, pacotes com esse padrão de conter tanto uma caixa de biblioteca quanto uma caixa binária terão apenas código suficiente na caixa binária para iniciar um executável que chama código da caixa de biblioteca. Isso permite que outros projetos se beneficiem ao máximo da funcionalidade que o pacote oferece, porque o código da caixa de biblioteca pode ser compartilhado.
>
> A árvore de módulos deve ser definida em *src/lib.rs*. Em seguida, qualquer item público pode ser usado na caixa binária começando os caminhos com o nome do pacote. A caixa binária se torna um usuário da caixa de biblioteca assim como uma caixa completamente externa usaria a caixa de biblioteca: ela só pode usar a API pública. Isso ajuda você a projetar uma boa API; você não é apenas o autor, você também é um cliente!
>
> No [Capítulo 12][ch12]<!-- ignore -->, demonstraremos essa prática organizacional com um programa de linha de comando que conterá tanto uma caixa binária quanto uma caixa de biblioteca.

### Iniciando Caminhos Relativos com `super`

Podemos construir caminhos relativos que começam no módulo pai, em vez de no módulo atual ou na raiz da caixa, usando `super` no início do caminho. Isso é semelhante a começar um caminho do sistema de arquivos com a sintaxe `..`. Usar `super` nos permite referenciar um item que sabemos estar no módulo pai, o que pode facilitar a reorganização da árvore de módulos quando o módulo está intimamente relacionado ao pai, mas o pai pode ser movido para outro lugar na árvore de módulos no futuro.

Considere o código no Exemplo 7-8 que modela a situação em que um chef corrige um pedido incorreto e o traz pessoalmente para o cliente. A função `corrigir_pedido_incorreto` definida no módulo `back_of_house` chama a função `entregar_pedido` definida no módulo pai especificando o caminho para `entregar_pedido` começando com `super`:

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-08/src/lib.rs}}
```

<span class="caption">Exemplo 7-8: Chamando uma função usando um caminho relativo que começa com `super`</span>

A função `corrigir_pedido_incorreto` está no módulo `back_of_house`, então podemos usar `super` para ir para o módulo pai de `back_of_house`, que neste caso é `crate`, a raiz. A partir daí, procuramos por `entregar_pedido` e o encontramos. Sucesso! Achamos que o módulo `back_of_house` e a função `entregar_pedido` provavelmente manterão a mesma relação entre si e serão movidos juntos se decidirmos reorganizar a árvore de módulos da caixa. Portanto, usamos `super` para ter menos lugares para atualizar o código no futuro, caso este código seja movido para um módulo diferente.

### Tornando Estruturas e Enumerações Públicas

Também podemos usar `pub` para designar estruturas e enumerações como públicas, mas há alguns detalhes adicionais no uso de `pub` com estruturas e enumerações. Se usarmos `pub` antes de uma definição de estrutura, tornamos a estrutura pública, mas os campos da estrutura ainda serão privados. Podemos tornar cada campo público ou não caso a caso. No Exemplo 7-9, definimos uma estrutura `back_of_house::Breakfast` pública com um campo `toast` público, mas um campo `seasonal_fruit` privado. Isso modela o caso em um restaurante onde o cliente pode escolher o tipo de pão que acompanha a refeição, mas o chef decide qual fruta acompanha a refeição com base no que está na estação e disponível em estoque. As frutas disponíveis mudam rapidamente, então os clientes não podem escolher a fruta ou mesmo ver qual fruta receberão.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-09/src/lib.rs}}
```

<span class="caption">Exemplo 7-9: Uma estrutura com alguns campos públicos e alguns campos privados</span>

Porque o campo `toast` na estrutura `back_of_house::Breakfast` é público, em `eat_at_restaurant` podemos escrever e ler o campo `toast` usando a notação de ponto. Note que não podemos usar o campo `seasonal_fruit` em `eat_at_restaurant` porque `seasonal_fruit` é privado. Tente descomentar a linha que modifica o valor do campo `seasonal_fruit` para ver qual erro você obtém!

Além disso, observe que, como `back_of_house::Breakfast` tem um campo privado, a estrutura precisa fornecer uma função associada pública que construa uma instância de `Breakfast` (a chamamos de `summer` aqui). Se `Breakfast` não tivesse essa função, não poderíamos criar uma instância de `Breakfast` em `eat_at_restaurant` porque não poderíamos definir o valor do campo privado `seasonal_fruit` em `eat_at_restaurant`.

Por outro lado, se tornarmos uma enumeração pública, todas as suas variantes se tornarão públicas. Só precisamos do `pub` antes da palavra-chave `enum`, como mostrado no Exemplo 7-10.

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-10/src/lib.rs}}
```

<span class="caption">Exemplo 7-10: Designar uma enumeração como pública torna todas as suas variantes públicas</span>

Como tornamos a enumeração `Appetizer` pública, podemos usar as variantes `Soup` e `Salad` em `eat_at_restaurant`.

Enumerações não são muito úteis a menos que suas variantes sejam públicas; seria irritante ter que anotar todas as variantes de enumeração com `pub` em todos os casos, então o padrão para as variantes de enumeração é serem públicas. Estruturas muitas vezes são úteis mesmo sem que seus campos sejam públicos, portanto os campos de estrutura seguem a regra geral de que tudo é privado por padrão, a menos que seja anotado com `pub`.

Há mais uma situação envolvendo `pub` que não abordamos, que é o último recurso do nosso sistema de módulos: a palavra-chave `use`. Vamos cobrir `use` primeiro, e depois mostraremos como combinar `pub` e `use`.

[pub]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#exposing-paths-with-the-pub-keyword
[api-guidelines]: https://rust-lang.github.io/api-guidelines/
[ch12]: ch12-00-an-io-project.html
