## Separando Módulos em Arquivos Diferentes

Até agora, todos os exemplos neste capítulo definiram vários módulos em um único arquivo. Quando os módulos se tornam grandes, pode ser desejável mover suas definições para um arquivo separado para tornar o código mais fácil de navegar.

Por exemplo, começaremos a partir do código do Exemplo 7-17 que tinha vários módulos do restaurante. Vamos extrair módulos para arquivos em vez de ter todos os módulos definidos no arquivo raiz da crate. Neste caso, o arquivo raiz da crate é *src/lib.rs*, mas esse procedimento também funciona com crates binárias cujo arquivo raiz da crate é *src/main.rs*.

Primeiro, vamos extrair o módulo `front_of_house` para seu próprio arquivo. Remova o código dentro das chaves para o módulo `front_of_house`, deixando apenas a declaração `mod front_of_house;`, para que *src/lib.rs* contenha o código mostrado no Exemplo 7-21. Observe que isso não compilará até que criemos o arquivo *src/front_of_house.rs* no Exemplo 7-22.

<span class="filename">Filename: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-21-and-22/src/lib.rs}}
```

<span class="caption">Exemplo 7-21: Declarando o módulo `front_of_house` cujo corpo estará em *src/front_of_house.rs*</span>

Em seguida, coloque o código que estava entre as chaves em um novo arquivo chamado *src/front_of_house.rs*, conforme mostrado no Exemplo 7-22. O compilador sabe procurar neste arquivo porque encontrou a declaração de módulo no arquivo raiz da crate com o nome `front_of_house`.

<span class="filename">Filename: src/front_of_house.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-21-and-22/src/front_of_house.rs}}
```

<span class="caption">Exemplo 7-22: Definições dentro do módulo `front_of_house` em *src/front_of_house.rs*</span>

Observe que você só precisa carregar um arquivo usando uma declaração `mod` *uma vez* em sua árvore de módulos. Uma vez que o compilador saiba que o arquivo faz parte do projeto (e saiba onde na árvore de módulos o código reside por causa de onde você colocou a declaração `mod`), outros arquivos em seu projeto devem se referir ao código do arquivo carregado usando um caminho para onde foi declarado, conforme coberto na seção [“Caminhos para Referenciar um Item na Árvore de Módulos”][paths]<!-- ignore -->. Em outras palavras, `mod` *não* é uma operação de "include" que você pode ter visto em outras linguagens de programação.

Em seguida, vamos extrair o módulo `hosting` para seu próprio arquivo. O processo é um pouco diferente porque `hosting` é um módulo filho de `front_of_house`, não do módulo raiz. Colocaremos o arquivo para `hosting` em um novo diretório que será nomeado de acordo com seus ancestrais na árvore de módulos, neste caso *src/front_of_house/*.

Para começar a mover `hosting`, alteramos *src/front_of_house.rs* para conter apenas a declaração do módulo `hosting`:

<span class="filename">Filename: src/front_of_house.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/no-listing-02-extracting-hosting/src/front_of_house.rs}}
```

Em seguida, criamos um diretório *src/front_of_house* e um arquivo *hosting.rs* para conter as definições feitas no módulo `hosting`:

<span class="filename">Filename: src/front_of_house/hosting.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/no-listing-02-extracting-hosting/src/front_of_house/hosting.rs}}
```

Se colocássemos *hosting.rs* no diretório *src*, o compilador esperaria que o código de *hosting.rs* estivesse em um módulo `hosting` declarado no arquivo raiz da crate, e não declarado como um filho do módulo `front_of_house`. As regras do compilador para quais arquivos verificar para o código dos módulos significam que os diretórios e arquivos correspondem mais de perto à árvore de módulos.

> ### Caminhos de Arquivo Alternativos
>
> Até agora, cobrimos os caminhos de arquivo mais idiomáticos que o compilador Rust utiliza, mas Rust também suporta um estilo mais antigo de caminho de arquivo. Para um módulo chamado `front_of_house` declarado na raiz da crate, o compilador procurará o código do módulo em:
>
> * *src/front_of_house.rs* (o que cobrimos)
> * *src/front_of_house/mod.rs* (caminho mais antigo, ainda suportado)
>
> Para um módulo chamado `hosting` que é um submódulo de `front_of_house`, o compilador procurará o código do módulo em:
>
> * *src/front_of_house/hosting.rs* (o que cobrimos)
> * *src/front_of_house/hosting/mod.rs* (caminho mais antigo, ainda suportado)
>
> Se você usar ambos os estilos para o mesmo módulo, você receberá um erro do compilador. Usar uma mistura de ambos os estilos para diferentes módulos no mesmo projeto é permitido, mas pode ser confuso para as pessoas que navegam no seu projeto.
>
> A principal desvantagem do estilo que usa arquivos nomeados *mod.rs* é que o seu projeto pode acabar com muitos arquivos chamados *mod.rs*, o que pode ficar confuso quando você os tem abertos no seu editor ao mesmo tempo.

Nós movemos o código de cada módulo para um arquivo separado, e a árvore de módulos permanece a mesma. As chamadas de função em `eat_at_restaurant` funcionarão sem qualquer modificação, mesmo que as definições estejam em arquivos diferentes. Essa técnica permite que você mova módulos para novos arquivos à medida que eles crescem em tamanho.

Observe que a declaração `pub use crate::front_of_house::hosting` em *src/lib.rs* também não mudou, nem o `use` tem algum impacto sobre quais arquivos são compilados como parte da crate. A palavra-chave `mod` declara módulos, e o Rust procura em um arquivo com o mesmo nome do módulo pelo código que pertence a esse módulo.

## Resumo

Rust permite que você divida um pacote em várias crates e uma crate em módulos para que você possa se referir a itens definidos em um módulo de outro módulo. Você pode fazer isso especificando caminhos absolutos ou relativos. Esses caminhos podem ser trazidos para o escopo com uma declaração `use`, para que você possa usar um caminho mais curto para várias utilizações do item nesse escopo. O código do módulo é privado por padrão, mas você pode tornar as definições públicas adicionando a palavra-chave `pub`.

No próximo capítulo, veremos algumas estruturas de dados de coleção na biblioteca padrão que você pode usar em seu código bem organizado.

[paths]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html
