## Definindo Módulos para Controlar Escopo e Privacidade

Nesta seção, falaremos sobre módulos e outras partes do sistema de módulos, nomeadamente *paths* que permitem nomear itens; a palavra-chave `use` que traz um caminho para o escopo; e a palavra-chave `pub` para tornar itens públicos. Também discutiremos a palavra-chave `as`, pacotes externos e o operador de globo.

Primeiro, começaremos com uma lista de regras para referência fácil quando você estiver organizando seu código no futuro. Em seguida, explicaremos cada uma das regras em detalhes.

### Resumo de Módulos

Aqui, fornecemos um resumo rápido de como módulos, caminhos, a palavra-chave `use` e a palavra-chave `pub` funcionam no compilador e como a maioria dos desenvolvedores organiza seu código. Vamos passar por exemplos de cada uma dessas regras ao longo deste capítulo, mas este é um ótimo lugar para consultar como lembrete de como os módulos funcionam.

- **Comece a partir da raiz da crate**: Ao compilar uma crate, o compilador primeiro procura no arquivo raiz da crate (geralmente *src/lib.rs* para uma crate de biblioteca ou *src/main.rs* para uma crate binária) o código a ser compilado.
- **Declarando módulos**: No arquivo raiz da crate, você pode declarar novos módulos; por exemplo, você pode declarar um módulo "jardim" com `mod jardim;`. O compilador procurará o código do módulo nestes lugares:
  - No próprio arquivo, dentro de chaves que substituem o ponto e vírgula após `mod jardim`
  - No arquivo *src/jardim.rs*
  - No arquivo *src/jardim/mod.rs*
- **Declarando submódulos**: Em qualquer arquivo que não seja o arquivo raiz da crate, você pode declarar submódulos. Por exemplo, você pode declarar `mod vegetais;` em *src/jardim.rs*. O compilador procurará o código do submódulo dentro do diretório com o nome do módulo pai nestes lugares:
  - No próprio arquivo, imediatamente após `mod vegetais`, dentro de chaves em vez do ponto e vírgula
  - No arquivo *src/jardim/vegetais.rs*
  - No arquivo *src/jardim/vegetais/mod.rs*
- **Caminhos para código em módulos**: Uma vez que um módulo faz parte da sua crate, você pode se referir ao código nesse módulo de qualquer outro lugar na mesma crate, desde que as regras de privacidade permitam, usando o caminho para o código. Por exemplo, um tipo `Aspargo` no módulo de vegetais do jardim seria encontrado em `crate::jardim::vegetais::Aspargo`.
- **Privado vs. público**: O código dentro de um módulo é privado por padrão para seus módulos pai. Para tornar um módulo público, declare-o com `pub mod` em vez de `mod`. Para tornar itens dentro de um módulo público, use `pub` antes de suas declarações.
- **A palavra-chave `use`**: Dentro de um escopo, a palavra-chave `use` cria atalhos para itens a fim de reduzir a repetição de caminhos longos. Em qualquer escopo que possa se referir a `crate::jardim::vegetais::Aspargo`, você pode criar um atalho com `use crate::jardim::vegetais::Aspargo;` e a partir desse momento você só precisa escrever `Aspargo` para usar esse tipo no escopo.

Aqui, criamos uma crate binária chamada `quintal` que ilustra essas regras. O diretório da crate, também chamado `quintal`, contém esses arquivos e diretórios:

```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

O arquivo raiz da crate neste caso é *src/main.rs*, e ele contém:

<span class="filename">Filename: src/main.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/main.rs}}
```

A linha `pub mod garden;` diz ao compilador para incluir o código que ele encontra em *src/garden.rs*, que é:

<span class="filename">Filename: src/garden.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden.rs}}
```

Aqui, `pub mod vegetables;` significa que o código em *src/garden/vegetables.rs* também está incluído. Esse código é:

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden/vegetables.rs}}
```

Agora vamos entrar em detalhes sobre essas regras e demonstrá-las em ação!

### Agrupando Código Relacionado em Módulos

*Os módulos* nos permitem organizar o código dentro de uma crate para legibilidade e reutilização fácil. Os módulos também nos permitem controlar a *privacidade* dos itens, porque o código dentro de um módulo é privado por padrão. Itens privados são detalhes de implementação internos que não estão disponíveis para uso externo. Podemos optar por tornar módulos e os itens dentro deles públicos, o que os expõe para permitir que o código externo os use e dependa deles.

Como exemplo, vamos escrever uma crate de biblioteca que fornece a funcionalidade de um restaurante. Vamos definir as assinaturas de funções, mas deixar seus corpos vazios para nos concentrarmos na organização do código, em vez da implementação de um restaurante.

Na indústria de restaurantes, algumas partes de um restaurante são chamadas de *frente de casa* e outras de *cozinha*. Frente de casa é onde os clientes estão; isso engloba onde os anfitriões acomodam os clientes, os servidores anotam pedidos e fazem pagamentos, e os bartenders preparam bebidas. Cozinha é onde os chefs e cozinheiros trabalham na cozinha, os lavadores de pratos fazem a limpeza e os gerentes fazem o trabalho administrativo.

Para estruturar nossa crate dessa forma, podemos organizar suas funções em módulos aninhados. Crie uma nova biblioteca chamada `restaurant` executando `cargo new restaurant --lib`; em seguida, insira o código do Exemplo 7-1 em *src/lib.rs* para definir alguns módulos e assinaturas de funções. Aqui está a seção de frente de casa:

<span class="filename">Filename: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-01/src/lib.rs}}
```

<span class="caption">Listagem 7-1: Um módulo `front_of_house` que contém outros
módulos que, por sua vez, contêm funções</span>

Definimos um módulo com a palavra-chave `mod`, seguida pelo nome do módulo
(neste caso, `front_of_house`). O corpo do módulo é então colocado entre chaves. Dentro dos módulos, podemos incluir outros módulos, como neste caso com os módulos `hosting` e `serving`. Módulos também podem conter definições de outros itens, como structs, enums, constants, traits e, como no Exemplo 7-1, funções.

Ao usar módulos, podemos agrupar definições relacionadas e nomear por que elas são relacionadas. Programadores que usam este código podem navegar pelo código com base nos grupos em vez de ter que ler todas as definições, facilitando a localização das definições relevantes para eles. Programadores que adicionam novas funcionalidades a este código saberiam onde colocar o código para manter o programa organizado.

Anteriormente, mencionamos que *src/main.rs* e *src/lib.rs* são chamados de raízes da crate. O motivo de seu nome é que o conteúdo de um destes dois arquivos forma um módulo chamado `crate` na raiz da estrutura de módulos da crate, conhecida como *árvore de módulos*.

A Listagem 7-2 mostra a árvore de módulos para a estrutura apresentada na Listagem 7-1.

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

<span class="caption">Listagem 7-2: A árvore de módulos para o código na Listagem 7-1</span>

Esta árvore mostra como alguns dos módulos estão aninhados uns dentro dos outros; por exemplo, `hosting` está aninhado dentro de `front_of_house`. A árvore também mostra que alguns módulos são *irmãos* entre si, o que significa que estão definidos no mesmo módulo; `hosting` e `serving` são irmãos definidos dentro de `front_of_house`. Se o módulo A estiver contido dentro do módulo B, dizemos que o módulo A é o *filho* do módulo B e que o módulo B é o *pai* do módulo A. Observe que toda a árvore de módulos é enraizada no módulo implícito chamado `crate`.

A árvore de módulos pode lembrar a árvore de diretórios do sistema de arquivos em seu computador; esta é uma comparação muito apropriada! Assim como diretórios em um sistema de arquivos, você usa módulos para organizar seu código. E assim como arquivos em um diretório, precisamos de uma maneira de encontrar nossos módulos.
