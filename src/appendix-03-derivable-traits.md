## Apêndice C: Traits Deriváveis

Em vários lugares do livro, discutimos o atributo `derive`, que você pode aplicar a uma definição de struct ou enum. O atributo `derive` gera código que implementará um trait com sua própria implementação padrão no tipo que você anotou com a sintaxe `derive`.

Neste apêndice, fornecemos uma referência de todos os traits na biblioteca padrão que você pode usar com `derive`. Cada seção aborda:

* Quais operadores e métodos derivar esse trait habilitará
* O que a implementação do trait fornecida pelo `derive` faz
* O que implementar o trait significa sobre o tipo
* As condições em que você está autorizado ou não a implementar o trait
* Exemplos de operações que requerem o trait

Se você deseja um comportamento diferente do fornecido pelo atributo `derive`, consulte a [documentação da biblioteca padrão](../std/index.html) para cada trait para obter detalhes sobre como implementá-los manualmente.

Os traits listados aqui são os únicos definidos pela biblioteca padrão que podem ser implementados em seus tipos usando `derive`. Outros traits definidos na biblioteca padrão não têm comportamento padrão sensato, então cabe a você implementá-los da maneira que fizer sentido para o que você está tentando realizar.

Um exemplo de um trait que não pode ser derivado é o `Display`, que lida com a formatação para usuários finais. Você sempre deve considerar a maneira apropriada de exibir um tipo para um usuário final. Quais partes do tipo um usuário final deve ser autorizado a ver? Quais partes eles acham relevantes? Qual formato dos dados seria mais relevante para eles? O compilador Rust não tem essa visão, portanto, não pode fornecer um comportamento padrão apropriado para você.

A lista de traits deriváveis fornecida neste apêndice não é abrangente: as bibliotecas podem implementar `derive` para seus próprios traits, tornando a lista de traits com os quais você pode usar `derive` verdadeiramente ilimitada. Implementar `derive` envolve o uso de uma macro procedural, que é abordada na seção [“Macros”][macros] do Capítulo 19.

### `Debug` for Programmer Output

The `Debug` trait enables debug formatting in format strings, which you
indicate by adding `:?` within `{}` placeholders.

The `Debug` trait allows you to print instances of a type for debugging
purposes, so you and other programmers using your type can inspect an instance
at a particular point in a program’s execution.

The `Debug` trait is required, for example, in use of the `assert_eq!` macro.
This macro prints the values of instances given as arguments if the equality
assertion fails so programmers can see why the two instances weren’t equal.

### `PartialEq` and `Eq` for Equality Comparisons

The `PartialEq` trait allows you to compare instances of a type to check for
equality and enables use of the `==` and `!=` operators.

Deriving `PartialEq` implements the `eq` method. When `PartialEq` is derived on
structs, two instances are equal only if *all* fields are equal, and the
instances are not equal if any fields are not equal. When derived on enums,
each variant is equal to itself and not equal to the other variants.

The `PartialEq` trait is required, for example, with the use of the
`assert_eq!` macro, which needs to be able to compare two instances of a type
for equality.

The `Eq` trait has no methods. Its purpose is to signal that for every value of
the annotated type, the value is equal to itself. The `Eq` trait can only be
applied to types that also implement `PartialEq`, although not all types that
implement `PartialEq` can implement `Eq`. One example of this is floating point
number types: the implementation of floating point numbers states that two
instances of the not-a-number (`NaN`) value are not equal to each other.

An example of when `Eq` is required is for keys in a `HashMap<K, V>` so the
`HashMap<K, V>` can tell whether two keys are the same.

### `PartialOrd` and `Ord` for Ordering Comparisons

The `PartialOrd` trait allows you to compare instances of a type for sorting
purposes. A type that implements `PartialOrd` can be used with the `<`, `>`,
`<=`, and `>=` operators. You can only apply the `PartialOrd` trait to types
that also implement `PartialEq`.

Deriving `PartialOrd` implements the `partial_cmp` method, which returns an
`Option<Ordering>` that will be `None` when the values given don’t produce an
ordering. An example of a value that doesn’t produce an ordering, even though
most values of that type can be compared, is the not-a-number (`NaN`) floating
point value. Calling `partial_cmp` with any floating point number and the `NaN`
floating point value will return `None`.

When derived on structs, `PartialOrd` compares two instances by comparing the
value in each field in the order in which the fields appear in the struct
definition. When derived on enums, variants of the enum declared earlier in the
enum definition are considered less than the variants listed later.

The `PartialOrd` trait is required, for example, for the `gen_range` method
from the `rand` crate that generates a random value in the range specified by a
range expression.

The `Ord` trait allows you to know that for any two values of the annotated
type, a valid ordering will exist. The `Ord` trait implements the `cmp` method,
which returns an `Ordering` rather than an `Option<Ordering>` because a valid
ordering will always be possible. You can only apply the `Ord` trait to types
that also implement `PartialOrd` and `Eq` (and `Eq` requires `PartialEq`). When
derived on structs and enums, `cmp` behaves the same way as the derived
implementation for `partial_cmp` does with `PartialOrd`.

An example of when `Ord` is required is when storing values in a `BTreeSet<T>`,
a data structure that stores data based on the sort order of the values.

### `Clone` and `Copy` for Duplicating Values

The `Clone` trait allows you to explicitly create a deep copy of a value, and
the duplication process might involve running arbitrary code and copying heap
data. See the [“Ways Variables and Data Interact:
Clone”][ways-variables-and-data-interact-clone]<!-- ignore --> section in
Chapter 4 for more information on `Clone`.

Deriving `Clone` implements the `clone` method, which when implemented for the
whole type, calls `clone` on each of the parts of the type. This means all the
fields or values in the type must also implement `Clone` to derive `Clone`.

An example of when `Clone` is required is when calling the `to_vec` method on a
slice. The slice doesn’t own the type instances it contains, but the vector
returned from `to_vec` will need to own its instances, so `to_vec` calls
`clone` on each item. Thus, the type stored in the slice must implement `Clone`.

The `Copy` trait allows you to duplicate a value by only copying bits stored on
the stack; no arbitrary code is necessary. See the [“Stack-Only Data:
Copy”][stack-only-data-copy]<!-- ignore --> section in Chapter 4 for more
information on `Copy`.

The `Copy` trait doesn’t define any methods to prevent programmers from
overloading those methods and violating the assumption that no arbitrary code
is being run. That way, all programmers can assume that copying a value will be
very fast.

You can derive `Copy` on any type whose parts all implement `Copy`. A type that
implements `Copy` must also implement `Clone`, because a type that implements
`Copy` has a trivial implementation of `Clone` that performs the same task as
`Copy`.

The `Copy` trait is rarely required; types that implement `Copy` have
optimizations available, meaning you don’t have to call `clone`, which makes
the code more concise.

Everything possible with `Copy` you can also accomplish with `Clone`, but the
code might be slower or have to use `clone` in places.

### `Hash` for Mapping a Value to a Value of Fixed Size

The `Hash` trait allows you to take an instance of a type of arbitrary size and
map that instance to a value of fixed size using a hash function. Deriving
`Hash` implements the `hash` method. The derived implementation of the `hash`
method combines the result of calling `hash` on each of the parts of the type,
meaning all fields or values must also implement `Hash` to derive `Hash`.

An example of when `Hash` is required is in storing keys in a `HashMap<K, V>`
to store data efficiently.

### `Default` for Default Values

The `Default` trait allows you to create a default value for a type. Deriving
`Default` implements the `default` function. The derived implementation of the
`default` function calls the `default` function on each part of the type,
meaning all fields or values in the type must also implement `Default` to
derive `Default`.

The `Default::default` function is commonly used in combination with the struct
update syntax discussed in the [“Creating Instances From Other Instances With
Struct Update
Syntax”][creating-instances-from-other-instances-with-struct-update-syntax]<!-- ignore -->
section in Chapter 5. You can customize a few fields of a struct and then
set and use a default value for the rest of the fields by using
`..Default::default()`.

The `Default` trait is required when you use the method `unwrap_or_default` on
`Option<T>` instances, for example. If the `Option<T>` is `None`, the method
`unwrap_or_default` will return the result of `Default::default` for the type
`T` stored in the `Option<T>`.

[creating-instances-from-other-instances-with-struct-update-syntax]:
ch05-01-defining-structs.html#creating-instances-from-other-instances-with-struct-update-syntax
[stack-only-data-copy]:
ch04-01-what-is-ownership.html#stack-only-data-copy
[ways-variables-and-data-interact-clone]:
ch04-01-what-is-ownership.html#ways-variables-and-data-interact-clone
[macros]: ch19-06-macros.html#macros
