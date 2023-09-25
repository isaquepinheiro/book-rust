## Armazenando Listas de Valores com Vetores

O primeiro tipo de coleção que vamos analisar é `Vec<T>`, também conhecido como um *vetor*.
Vetores permitem que você armazene mais de um valor em uma única estrutura de dados que
coloca todos os valores lado a lado na memória. Vetores só podem armazenar valores
do mesmo tipo. Eles são úteis quando você tem uma lista de itens, como os
linhas de texto em um arquivo ou os preços de itens em um carrinho de compras.

### Criando um Novo Vetor

Para criar um novo vetor vazio, chamamos a função `Vec::new`, conforme mostrado em
Listagem 8-1.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-01/src/main.rs:here}}
```

<span class="caption">Listagem 8-1: Criando um novo vetor vazio para armazenar valores
do tipo `i32`</span>

Observe que adicionamos uma anotação de tipo aqui. Como não estamos inserindo nenhum
valor neste vetor, o Rust não sabe que tipo de elementos pretendemos
armazenar. Isso é um ponto importante. Vetores são implementados usando genéricos;
vamos abordar como usar genéricos com seus próprios tipos no Capítulo 10. Por enquanto,
saiba que o tipo `Vec<T>` fornecido pela biblioteca padrão pode conter qualquer tipo.
Quando criamos um vetor para armazenar um tipo específico, podemos especificar o tipo entre
colchetes angulares. Na Listagem 8-1, dissemos ao Rust que o `Vec<T>` em `v`
conterá elementos do tipo `i32`.

Na maioria das vezes, você criará um `Vec<T>` com valores iniciais e o Rust inferirá
o tipo de valor que você deseja armazenar, então você raramente precisa fazer esta anotação de tipo. O Rust convenientemente fornece a macro `vec!`, que criará um
novo vetor que contém os valores que você lhe der. A Listagem 8-2 cria um novo
`Vec<i32>` que contém os valores `1`, `2` e `3`. O tipo inteiro é `i32`
porque esse é o tipo inteiro padrão, como discutimos na seção ["Tipos de Dados"][data-types] do Capítulo 3.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-02/src/main.rs:here}}
```

<span class="caption">Listagem 8-2: Criando um novo vetor contendo valores</span>

Como fornecemos valores iniciais do tipo `i32`, o Rust pode inferir que o tipo de `v`
é `Vec<i32>`, e a anotação de tipo não é necessária. Em seguida, veremos como
modificar um vetor.

### Atualizando um Vetor

Para criar um vetor e depois adicionar elementos a ele, podemos usar o método `push`,
conforme mostrado na Listagem 8-3.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-03/src/main.rs:here}}
```

<span class="caption">Listagem 8-3: Usando o método `push` para adicionar valores a um
vetor</span>

Como acontece com qualquer variável, se quisermos ser capazes de alterar seu valor, precisamos
torná-la mutável usando a palavra-chave `mut`, conforme discutido no Capítulo 3. Os números
que colocamos dentro são todos do tipo `i32`, e o Rust infere isso a partir dos dados, então
não precisamos da anotação `Vec<i32>`.

### Lendo Elementos de Vetores

Existem duas maneiras de referenciar um valor armazenado em um vetor: por meio de indexação ou
usando o método `get`. Nos exemplos a seguir, anotamos os tipos dos valores que são retornados por essas funções para maior clareza.

A Listagem 8-4 mostra ambos os métodos de acesso a um valor em um vetor, com sintaxe de indexação e o método `get`.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-04/src/main.rs:here}}
```

<span class="caption">Listagem 8-4: Usando a sintaxe de indexação ou o método `get` para
acessar um item em um vetor</span>

Observe alguns detalhes aqui. Usamos o valor do índice `2` para obter o terceiro elemento
porque os vetores são indexados por número, começando em zero. Usar `&` e `[]`
nos dá uma referência ao elemento no valor do índice. Quando usamos o método `get` com o índice passado como argumento, obtemos um `Option<&T>` que podemos
usar com `match`.

A razão pela qual o Rust fornece essas duas maneiras de referenciar um elemento é para que você possa
escolher como o programa se comporta quando você tenta usar um valor de índice fora do
intervalo dos elementos existentes. Como exemplo, vejamos o que acontece quando temos
um vetor com cinco elementos e, em seguida, tentamos acessar um elemento no índice 100
com cada técnica, conforme mostrado na Listagem 8-5.

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-05/src/main.rs:here}}
```

<span class="caption">Listagem 8-5: Tentativa de acessar o elemento no índice
100 em um vetor contendo cinco elementos</span>

Quando executamos este código, o primeiro método `[]` fará com que o programa entre em pânico
porque faz referência a um elemento inexistente. Este método é melhor usado quando você
deseja que seu programa falhe se houver uma tentativa de acessar um elemento após o final do vetor.

Quando o método `get` recebe um índice que está fora do vetor, ele retorna
`None` sem entrar em pânico. Você usaria este método se acessar um elemento
além do alcance do vetor puder acontecer ocasionalmente em circunstâncias normais.
Seu código então terá lógica para lidar com ter `Some(&element)` ou `None`,
conforme discutido no Capítulo 6. Por exemplo, o índice poderia vir de uma pessoa inserindo um número. Se eles acidentalmente inserirem um número muito grande e o programa receber um valor `None`, você poderia informar ao usuário quantos itens estão no vetor atual e dar a eles outra chance de inserir um valor válido. Isso seria mais amigável ao usuário do que fazer o programa travar devido a um erro de digitação!

Quando o programa tem uma referência válida, o verificador de empréstimo (coberto no Capítulo 4) aplica as regras de propriedade e empréstimo para garantir que essa referência e qualquer outra referência ao conteúdo do vetor permaneçam válidas. Lembre-se da regra que diz que você não pode ter referências mutáveis e imutáveis no mesmo escopo. Essa regra se aplica na Listagem 8-6, onde temos uma referência imutável ao primeiro elemento em um vetor e tentamos adicionar um elemento ao final. Este programa não funcionará se também tentarmos nos referir a esse elemento posteriormente na função:


```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-06/src/main.rs:here}}
```

<span class="caption">Listagem 8-6: Tentativa de adicionar um elemento a um vetor
enquanto se mantém uma referência a um item</span>

Compilar este código resultará neste erro:


```console
{{#include ../listings/ch08-common-collections/listing-08-06/output.txt}}
```

O código na Listagem 8-6 pode parecer que deveria funcionar: por que uma referência
ao primeiro elemento se importaria com mudanças no final do vetor? Este erro ocorre
devido à forma como os vetores funcionam: porque os vetores colocam os valores lado a lado
na memória, adicionar um novo elemento ao final do vetor pode exigir
alocar nova memória e copiar os elementos antigos para o novo espaço, se não houver espaço suficiente para colocar todos os elementos lado a lado onde o vetor
está atualmente armazenado. Nesse caso, a referência ao primeiro elemento estaria
apontando para a memória desalocada. As regras de empréstimo impedem que os programas
acabem nessa situação.

> Nota: Para obter mais informações sobre os detalhes de implementação do tipo `Vec<T>`, consulte ["The Rustonomicon"][nomicon].

### Iterando sobre os Valores em um Vetor

Para acessar cada elemento em um vetor sequencialmente, iteramos por todos os
elementos em vez de usar índices para acessá-los individualmente. A Listagem 8-7 mostra como
usar um loop `for` para obter referências imutáveis a cada elemento em um vetor de
valores `i32` e imprimi-los.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-07/src/main.rs:here}}
```

<span class="caption">Listagem 8-7: Imprimindo cada elemento em um vetor ao
iterar sobre os elementos usando um loop `for`</span>

Também podemos iterar sobre referências mutáveis a cada elemento em um vetor mutável
para fazer alterações em todos os elementos. O loop `for` na Listagem 8-8
adicionará `50` a cada elemento.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-08/src/main.rs:here}}
```

<span class="caption">Listagem 8-8: Iterando sobre referências mutáveis aos
elementos em um vetor</span>

Para alterar o valor ao qual a referência mutável se refere, precisamos usar o operador de desreferência `*` para acessar o valor em `i` antes de usar o operador `+=`. Falaremos mais sobre o operador de desreferência na seção ["Seguindo o Ponteiro até o Valor com o Operador de Desreferência"][deref] do Capítulo 15.

Iterar sobre um vetor, seja de forma imutável ou mutável, é seguro devido às regras do verificador de empréstimo. Se tentássemos inserir ou remover itens nos corpos dos loops `for` nas Listagens 8-7 e 8-8, receberíamos um erro do compilador semelhante ao que obtivemos com o código na Listagem 8-6. A referência ao vetor que o loop `for` mantém impede a modificação simultânea de todo o vetor.

### Usando um Enum para Armazenar Múltiplos Tipos

Os vetores só podem armazenar valores do mesmo tipo. Isso pode ser inconveniente; há definitivamente casos de uso em que precisamos armazenar uma lista de itens de diferentes tipos. Felizmente, as variantes de um enum são definidas sob o mesmo tipo de enum, portanto, quando precisamos que um tipo represente elementos de tipos diferentes, podemos definir e usar um enum!

Por exemplo, digamos que queremos obter valores de uma linha em uma planilha em que algumas das colunas na linha contêm inteiros, alguns números de ponto flutuante e algumas strings. Podemos definir um enum cujas variantes irão conter os diferentes tipos de valor, e todas as variantes do enum serão consideradas do mesmo tipo: o tipo do enum. Em seguida, podemos criar um vetor para armazenar esse enum e, assim, em última análise, armazenar diferentes tipos. Demonstramos isso no Exemplo 8-9.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-09/src/main.rs:here}}
```

<span class="caption">Exemplo 8-9: Definindo um `enum` para armazenar valores de
diferentes tipos em um único vetor</span>

Rust precisa saber quais tipos estarão no vetor em tempo de compilação para que saiba exatamente quanto de memória na pilha (heap) será necessário para armazenar cada elemento. Também devemos ser explícitos sobre quais tipos são permitidos neste vetor. Se o Rust permitisse que um vetor armazenasse qualquer tipo, haveria uma chance de que um ou mais dos tipos causassem erros nas operações realizadas nos elementos do vetor. Usar um enum junto com uma expressão `match` significa que o Rust garantirá em tempo de compilação que todos os casos possíveis sejam tratados, como discutido no Capítulo 6.

Se você não conhece o conjunto exaustivo de tipos que um programa receberá em tempo de execução para armazenar em um vetor, a técnica do enum não funcionará. Em vez disso, você pode usar um objeto de trait, o qual abordaremos no Capítulo 17.

Agora que discutimos algumas das formas mais comuns de usar vetores, certifique-se de revisar [a documentação da API][vec-api]<!-- ignore --> para todos os muitos métodos úteis definidos em `Vec<T>` pela biblioteca padrão. Por exemplo, além do método `push`, um método `pop` remove e retorna o último elemento.

### Descartar um Vetor Descarta Seus Elementos

Assim como qualquer outra `struct`, um vetor é liberado quando sai do escopo, como anotado no Exemplo 8-10.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-10/src/main.rs:here}}
```

<span class="caption">Exemplo 8-10: Mostrando onde o vetor e seus elementos são descartados</span>

Quando o vetor é descartado, todo o seu conteúdo também é descartado, o que significa que os inteiros que ele contém serão limpos. O verificador de empréstimos (borrow checker) garante que quaisquer referências aos conteúdos de um vetor sejam usadas apenas enquanto o vetor em si estiver válido.

Vamos seguir em frente para o próximo tipo de coleção: `String`!

[data-types]: ch03-02-data-types.html#data-types
[nomicon]: ../nomicon/vec/vec.html
[vec-api]: ../std/vec/struct.Vec.html
[deref]: ch15-02-deref.html#following-the-pointer-to-the-value-with-the-dereference-operator
