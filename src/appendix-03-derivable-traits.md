## Apêndice C: Traits Deriváveis

Em várias partes do livro, discutimos o atributo `derive`, que você pode aplicar a uma definição de struct ou enumeração. O atributo `derive` gera código que implementará um trait com sua própria implementação padrão no tipo que você anotou com a sintaxe `derive`.

Neste apêndice, fornecemos uma referência de todos os traits na biblioteca padrão que você pode usar com `derive`. Cada seção aborda:

* Quais operadores e métodos que a derivação deste trait habilitará.
* O que a implementação do trait fornecida por `derive` faz.
* O que a implementação do trait significa sobre o tipo.
* As condições em que você está ou não autorizado a implementar o trait.
* Exemplos de operações que requerem o trait.

Se você deseja um comportamento diferente do fornecido pelo atributo `derive`, consulte a [documentação da biblioteca padrão](../std/index.html)<!-- ignore --> de cada trait para obter detalhes sobre como implementá-los manualmente.

Os traits listados aqui são os únicos definidos pela biblioteca padrão que podem ser implementados em seus tipos usando `derive`. Outros traits definidos na biblioteca padrão não têm um comportamento padrão sensato, portanto, cabe a você implementá-los da maneira que faça sentido para o que você está tentando realizar.

Um exemplo de um trait que não pode ser derivado é o `Display`, que lida com a formatação para os usuários finais. Você sempre deve considerar a maneira apropriada de exibir um tipo para um usuário final. Quais partes do tipo um usuário final deve poder ver? Quais partes eles considerariam relevantes? Qual formato dos dados seria mais relevante para eles? O compilador Rust não tem essa visão, portanto, não pode fornecer um comportamento padrão apropriado para você.

A lista de traits deriváveis fornecida neste apêndice não é abrangente: as bibliotecas podem implementar `derive` para seus próprios traits, tornando a lista de traits que você pode usar com `derive` verdadeiramente aberta. A implementação de `derive` envolve o uso de uma macro procedural, que é abordada na seção [“Macros”][macros]<!-- ignore --> do Capítulo 19.

### `Debug` para Saída de Programadores

O trait `Debug` permite formatação de depuração em strings de formato, que você indica adicionando `:?` dentro de espaços reservados `{}`.

O trait `Debug` permite que você imprima instâncias de um tipo para fins de depuração, para que você e outros programadores que usam seu tipo possam inspecionar uma instância em um ponto específico na execução de um programa.

O trait `Debug` é necessário, por exemplo, ao usar a macro `assert_eq!`. Essa macro imprime os valores das instâncias fornecidas como argumentos se a asserção de igualdade falhar, para que os programadores possam ver por que as duas instâncias não eram iguais.

### `PartialEq` e `Eq` para Comparação de Igualdade

O trait `PartialEq` permite que você compare instâncias de um tipo para verificar a igualdade e habilita o uso dos operadores `==` e `!=`.

A derivação do `PartialEq` implementa o método `eq`. Quando o `PartialEq` é derivado para structs, duas instâncias são iguais apenas se *todos* os campos forem iguais, e as instâncias não são iguais se algum campo não for igual. Quando derivado para enums, cada variante é igual a si mesma e não igual às outras variantes.

O trait `PartialEq` é necessário, por exemplo, ao usar a macro `assert_eq!`, que precisa ser capaz de comparar duas instâncias de um tipo quanto à igualdade.

O trait `Eq` não possui métodos. Sua finalidade é indicar que, para cada valor do tipo anotado, o valor é igual a si mesmo. O trait `Eq` só pode ser aplicado a tipos que também implementam o `PartialEq`, embora nem todos os tipos que implementam o `PartialEq` possam implementar o `Eq`. Um exemplo disso são os tipos de números de ponto flutuante: a implementação de números de ponto flutuante declara que duas instâncias do valor não-numérico (`NaN`) não são iguais entre si.

Um exemplo de quando o `Eq` é necessário é para chaves em um `HashMap<K, V>`, para que o `HashMap<K, V>` possa determinar se duas chaves são iguais.

### `PartialOrd` e `Ord` para Comparação de Ordens

O trait `PartialOrd` permite que você compare instâncias de um tipo para fins de ordenação. Um tipo que implementa `PartialOrd` pode ser usado com os operadores `<`, `>`, `<=` e `>=`. Você só pode aplicar o trait `PartialOrd` a tipos que também implementam `PartialEq`.

Ao derivar o `PartialOrd`, você implementa o método `partial_cmp`, que retorna um `Option<Ordering>` que será `None` quando os valores fornecidos não produzirem uma ordenação. Um exemplo de um valor que não produz uma ordenação, embora a maioria dos valores desse tipo possa ser comparada, é o valor de ponto flutuante não-numérico (`NaN`). Chamar `partial_cmp` com qualquer número de ponto flutuante e o valor de ponto flutuante `NaN` retornará `None`.

Quando derivado para structs, o `PartialOrd` compara duas instâncias, comparando o valor em cada campo na ordem em que os campos aparecem na definição da struct. Quando derivado para enums, as variantes do enum declaradas anteriormente na definição do enum são consideradas menores do que as variantes listadas posteriormente.

O trait `PartialOrd` é necessário, por exemplo, para o método `gen_range` da biblioteca `rand`, que gera um valor aleatório no intervalo especificado por uma expressão de intervalo.

O trait `Ord` permite que você saiba que, para qualquer dois valores do tipo anotado, uma ordenação válida existirá. O trait `Ord` implementa o método `cmp`, que retorna um `Ordering` em vez de um `Option<Ordering>`, porque uma ordenação válida sempre será possível. Você só pode aplicar o trait `Ord` a tipos que também implementam `PartialOrd` e `Eq` (e `Eq` requer `PartialEq`). Quando derivado para structs e enums, `cmp` se comporta da mesma forma que a implementação derivada para `partial_cmp` com `PartialOrd`.

Um exemplo de quando o `Ord` é necessário é ao armazenar valores em um `BTreeSet<T>`, uma estrutura de dados que armazena dados com base na ordem de classificação dos valores.

### `Clone` e `Copy` para Duplicação de Valores

A trait `Clone` permite que você crie explicitamente uma cópia profunda de um valor, e
o processo de duplicação pode envolver a execução de código arbitrário e a cópia de dados na heap. Veja a seção ["Formas como Variáveis e Dados Interagem:
Clone"][ways-variables-and-data-interact-clone]<!-- ignore --> no
Capítulo 4 para obter mais informações sobre `Clone`.

A derivação de `Clone` implementa o método `clone`, que, quando implementado para o
tipo inteiro, chama `clone` em cada uma das partes do tipo. Isso significa que todos os
campos ou valores no tipo também devem implementar `Clone` para derivar `Clone`.

Um exemplo em que `Clone` é necessário é ao chamar o método `to_vec` em uma
fatia (slice). A fatia não possui as instâncias dos tipos que ela contém, mas o vetor
retornado por `to_vec` precisará possuir suas instâncias, então `to_vec` chama
`clone` em cada item. Portanto, o tipo armazenado na fatia deve implementar `Clone`.

A trait `Copy` permite duplicar um valor copiando apenas os bits armazenados na
pilha (stack); nenhum código arbitrário é necessário. Veja a seção ["Dados Apenas na Pilha:
Copy"][stack-only-data-copy]<!-- ignore --> no Capítulo 4 para obter mais
informações sobre `Copy`.

A trait `Copy` não define métodos para evitar que programadores sobrecarreguem esses métodos e violem a suposição de que nenhum código arbitrário está sendo executado. Dessa forma, todos os programadores podem assumir que a cópia de um valor será muito rápida.

Você pode derivar `Copy` para qualquer tipo cujas partes implementem todas `Copy`. Um tipo que
implementa `Copy` também deve implementar `Clone`, porque um tipo que implementa
`Copy` possui uma implementação trivial de `Clone` que realiza a mesma tarefa que
`Copy`.

A trait `Copy` raramente é necessária; tipos que implementam `Copy` têm
otimizações disponíveis, o que significa que você não precisa chamar `clone`, o que torna
o código mais conciso.

Tudo o que é possível com `Copy` também pode ser realizado com `Clone`, mas o
código pode ser mais lento ou ter que usar `clone` em alguns lugares.

### `Hash` para Mapeamento de um Valor para um Valor de Tamanho Fixo

A trait `Hash` permite que você pegue uma instância de um tipo de tamanho arbitrário e
mapeie essa instância para um valor de tamanho fixo usando uma função de hash. A derivação
de `Hash` implementa o método `hash`. A implementação derivada do método `hash`
combina o resultado da chamada de `hash` em cada uma das partes do tipo,
o que significa que todos os campos ou valores também devem implementar `Hash` para derivar `Hash`.

Um exemplo em que `Hash` é necessário é ao armazenar chaves em um `HashMap<K, V>`
para armazenar dados de forma eficiente.

### `Default` para Valores Padrão

A trait `Default` permite criar um valor padrão para um tipo. A derivação de `Default` implementa a função `default`. A implementação derivada da função `default` chama a função `default` em cada parte do tipo, o que significa que todos os campos ou valores no tipo também devem implementar `Default` para derivar `Default`.

A função `Default::default` é comumente usada em combinação com a sintaxe de atualização de estrutura discutida na seção ["Criando Instâncias a Partir de Outras Instâncias Com Sintaxe de Atualização de Estrutura"][creating-instances-from-other-instances-with-struct-update-syntax]<!-- ignore --> no Capítulo 5. Você pode personalizar alguns campos de uma estrutura e, em seguida, definir e usar um valor padrão para o restante dos campos usando `..Default::default()`.

A trait `Default` é necessária quando você utiliza o método `unwrap_or_default` em instâncias de `Option<T>`, por exemplo. Se o `Option<T>` estiver `None`, o método `unwrap_or_default` retornará o resultado de `Default::default` para o tipo `T` armazenado no `Option<T>`.

[creating-instances-from-other-instances-with-struct-update-syntax]:
ch05-01-defining-structs.html#creating-instances-from-other-instances-with-struct-update-syntax
[stack-only-data-copy]:
ch04-01-what-is-ownership.html#stack-only-data-copy
[ways-variables-and-data-interact-clone]:
ch04-01-what-is-ownership.html#ways-variables-and-data-interact-clone
[macros]: ch19-06-macros.html#macros
