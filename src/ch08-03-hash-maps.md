A última de nossas coleções comuns é o *mapa de hash*. O tipo `HashMap<K, V>` armazena um mapeamento de chaves do tipo `K` para valores do tipo `V` usando uma *função de hash*, que determina como coloca essas chaves e valores na memória. Muitas linguagens de programação suportam esse tipo de estrutura de dados, mas frequentemente usam nomes diferentes, como hash, mapa, objeto, tabela de hash, dicionário ou array associativo, para citar alguns.

Mapas de hash são úteis quando você deseja procurar dados não usando um índice, como pode fazer com vetores, mas sim usando uma chave que pode ser de qualquer tipo. Por exemplo, em um jogo, você pode acompanhar a pontuação de cada equipe em um mapa de hash em que cada chave é o nome de uma equipe e os valores são as pontuações de cada equipe. Dado o nome de uma equipe, você pode recuperar sua pontuação.

Nesta seção, abordaremos a API básica de mapas de hash, mas muitas outras funcionalidades estão disponíveis nas funções definidas em `HashMap<K, V>` pela biblioteca padrão. Como sempre, consulte a documentação da biblioteca padrão para obter mais informações.

### Criando um Novo Hash Map

Uma maneira de criar um hash map vazio é usando `new` e adicionando elementos com `insert`. No Exemplo 8-20, estamos mantendo o controle das pontuações de duas equipes cujos nomes são *Azul* e *Amarelo*. A equipe Azul começa com 10 pontos, e a equipe Amarelo começa com 50.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-20/src/main.rs:here}}
```

<span class="caption">Exemplo 8-20: Criando um novo hash map e inserindo algumas chaves e valores</span>

Observe que primeiro precisamos usar `HashMap` da parte de coleções da biblioteca padrão. Entre as nossas três coleções comuns, esta é a menos usada, portanto, não está incluída automaticamente no escopo no prelúdio. Os hash maps também têm menos suporte da biblioteca padrão; por exemplo, não há macro embutida para construí-los.

Assim como vetores, hash maps armazenam seus dados no heap. Este `HashMap` possui chaves do tipo `String` e valores do tipo `i32`. Como vetores, os hash maps são homogêneos: todas as chaves devem ter o mesmo tipo entre si e todos os valores devem ter o mesmo tipo.

### Acessando Valores em um Hash Map

Podemos obter um valor do hash map fornecendo sua chave ao método `get`, como mostrado no Exemplo 8-21.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-21/src/main.rs:here}}
```

<span class="caption">Exemplo 8-21: Acessando a pontuação da equipe Azul armazenada no hash map</span>

Aqui, `score` terá o valor associado à equipe Azul, e o resultado será `10`. O método `get` retorna um `Option<&V>`; se não houver um valor para essa chave no hash map, `get` retornará `None`. Este programa lida com a `Option` chamando `copied` para obter um `Option<i32>` em vez de um `Option<&i32>`, depois `unwrap_or` para definir `score` como zero se `scores` não tiver uma entrada para a chave.

Podemos iterar sobre cada par chave/valor em um hash map de maneira semelhante ao que fazemos com vetores, usando um loop `for`:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/no-listing-03-iterate-over-hashmap/src/main.rs:here}}
```

Este código imprimirá cada par em uma ordem arbitrária:

```text
Yellow: 50
Blue: 10
```

### Hash Maps e Propriedade (Ownership)

Para tipos que implementam o traço `Copy`, como `i32`, os valores são copiados para o hash map. Para valores de propriedade (owned) como `String`, os valores serão movidos e o hash map será o proprietário desses valores, conforme demonstrado no Exemplo 8-22.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-22/src/main.rs:here}}
```

<span class="caption">Exemplo 8-22: Mostrando que chaves e valores são de propriedade do hash map assim que são inseridos</span>

Não podemos usar as variáveis `field_name` e `field_value` após elas terem sido movidas para o hash map com a chamada para `insert`.

Se inserirmos referências a valores no hash map, os valores não serão movidos para o hash map. Os valores apontados pelas referências devem ser válidos pelo menos enquanto o hash map for válido. Abordaremos essas questões com mais detalhes na seção [“Validating References with Lifetimes”][validating-references-with-lifetimes] no Capítulo 10.

### Atualizando um Hash Map

Embora o número de pares chave-valor seja expansível, cada chave única só pode ter um valor associado a ela por vez (mas não o contrário: por exemplo, tanto a equipe Azul quanto a equipe Amarela podem ter o valor 10 armazenado no hash map `scores`).

Quando você deseja alterar os dados em um hash map, é preciso decidir como lidar com o caso em que uma chave já tem um valor atribuído. Você pode substituir o valor antigo pelo novo, ignorando completamente o valor antigo. Você pode manter o valor antigo e ignorar o novo valor, adicionando o novo valor apenas se a chave *ainda não* tiver um valor. Ou você pode combinar o valor antigo com o novo valor. Vamos ver como fazer cada uma dessas opções!

#### Sobrescrevendo um Valor

Se inserirmos uma chave e um valor em um hash map e, em seguida, inserirmos a mesma chave com um valor diferente, o valor associado a essa chave será substituído. Mesmo que o código no Exemplo 8-23 chame `insert` duas vezes, o hash map conterá apenas um par chave/valor porque estamos inserindo o valor para a chave da equipe Azul duas vezes.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-23/src/main.rs:here}}
```

<span class="caption">Exemplo 8-23: Substituindo um valor armazenado com uma chave específica</span>

Este código imprimirá `{"Blue": 25}`. O valor original de `10` foi sobrescrito.

<!-- Old headings. Do not remove or links may break. -->
<a id="only-inserting-a-value-if-the-key-has-no-value"></a>

#### Adicionando uma Chave e Valor Somente Se uma Chave Não Estiver Presente

É comum verificar se uma chave específica já existe no hash map com um valor e, em seguida, tomar as seguintes ações: se a chave já existir no hash map, o valor existente deve permanecer como está. Se a chave não existir, ela deve ser inserida juntamente com um valor.

Os hash maps possuem uma API especial para isso chamada `entry` que recebe a chave que você deseja verificar como parâmetro. O valor de retorno do método `entry` é um enum chamado `Entry` que representa um valor que pode ou não existir. Digamos que queremos verificar se a chave para a equipe Amarela tem um valor associado a ela. Se não tiver, queremos inserir o valor 50, e o mesmo para a equipe Azul. Usando a API `entry`, o código se parece com o Exemplo 8-24.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-24/src/main.rs:here}}
```

<span class="caption">Exemplo 8-24: Usando o método `entry` para inserir apenas se a chave ainda não tiver um valor</span>

O método `or_insert` em `Entry` é definido para retornar uma referência mutável ao valor correspondente à chave `Entry` se essa chave existir e, caso contrário, insere o parâmetro como o novo valor para esta chave e retorna uma referência mutável ao novo valor. Essa técnica é muito mais limpa do que escrever a lógica manualmente e, além disso, funciona de forma mais harmoniosa com o verificador de empréstimos (borrow checker).

Executar o código no Exemplo 8-24 imprimirá `{"Yellow": 50, "Blue": 10}`. A primeira chamada para `entry` inserirá a chave para a equipe Amarela com o valor 50, pois a equipe Amarela ainda não tem um valor. A segunda chamada para `entry` não alterará o hash map, pois a equipe Azul já possui o valor 10.

#### Atualizando um Valor com Base no Valor Antigo

Outro caso de uso comum para hash maps é procurar o valor de uma chave e, em seguida, atualizá-lo com base no valor antigo. Por exemplo, o Exemplo 8-25 mostra código que conta quantas vezes cada palavra aparece em algum texto. Usamos um hash map com as palavras como chaves e incrementamos o valor para acompanhar quantas vezes vimos essa palavra. Se for a primeira vez que vemos uma palavra, primeiro inseriremos o valor 0.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-25/src/main.rs:here}}
```

<span class="caption">Exemplo 8-25: Contando ocorrências de palavras usando um hash map que armazena palavras e contagens</span>

Este código imprimirá `{"world": 2, "hello": 1, "wonderful": 1}`. Você pode ver os mesmos pares chave/valor impressos em uma ordem diferente: lembre-se da seção ["Acessando Valores em um Hash Map"][access]<!-- ignore --> que a iteração sobre um hash map acontece em uma ordem arbitrária.

O método `split_whitespace` retorna um iterador sobre sub-slices, separados por espaços em branco, do valor em `text`. O método `or_insert` retorna uma referência mutável (`&mut V`) ao valor para a chave especificada. Aqui, armazenamos essa referência mutável na variável `count`, para atribuir um novo valor, precisamos primeiro desreferenciar `count` usando o asterisco (`*`). A referência mutável sai do escopo no final do loop `for`, então todas essas alterações são seguras e permitidas pelas regras de empréstimo (borrowing) do Rust.

### Funções de Hashing

Por padrão, o `HashMap` utiliza uma função de hashing chamada *SipHash* que pode oferecer resistência a ataques de Negação de Serviço (DoS) envolvendo tabelas de hash[^siphash]<!-- ignore -->. Esta não é a função de hashing mais rápida disponível, mas a compensação por uma melhor segurança que vem com a redução no desempenho vale a pena. Se você analisar o código e achar que a função de hash padrão está muito lenta para seus propósitos, você pode mudar para outra função especificando um hasher diferente. Um *hasher* é um tipo que implementa o traço `BuildHasher`. Falaremos sobre traços e como implementá-los no Capítulo 10. Você não precisa necessariamente implementar seu próprio hasher do zero; [crates.io](https://crates.io/)<!-- ignore --> possui bibliotecas compartilhadas por outros usuários Rust que fornecem hashers implementando muitos algoritmos de hashing comuns.

[^siphash]: [https://en.wikipedia.org/wiki/SipHash](https://en.wikipedia.org/wiki/SipHash)

## Resumo

Vectores, strings e hash maps fornecerão uma grande quantidade de funcionalidades necessárias em programas quando você precisa armazenar, acessar e modificar dados. Aqui estão alguns exercícios para os quais você deve estar preparado para resolver agora:

* Dada uma lista de inteiros, use um vetor e retorne a mediana (quando ordenada, o valor na posição do meio) e a moda (o valor que ocorre com mais frequência; um hash map será útil aqui) da lista.
* Converta strings para o "pig latin". A primeira consoante de cada palavra é movida para o final da palavra e "ay" é adicionado, então "first" se torna "irst-fay". Palavras que começam com uma vogal têm "hay" adicionado ao final ("apple" se torna "apple-hay"). Tenha em mente os detalhes sobre a codificação UTF-8!
* Usando um hash map e vetores, crie uma interface de texto que permita ao usuário adicionar nomes de funcionários a um departamento em uma empresa. Por exemplo, "Adicionar Sally à Engenharia" ou "Adicionar Amir às Vendas". Em seguida, permita que o usuário obtenha uma lista de todas as pessoas em um departamento ou todas as pessoas na empresa por departamento, ordenadas alfabeticamente.

A documentação da API da biblioteca padrão descreve métodos que vetores, strings e hash maps têm que serão úteis para esses exercícios!

Estamos entrando em programas mais complexos nos quais as operações podem falhar, então é um momento perfeito para discutir o tratamento de erros. Vamos fazer isso a seguir!

[validating-references-with-lifetimes]:
ch10-03-lifetime-syntax.html#validating-references-with-lifetimes
[access]: #accessing-values-in-a-hash-map
