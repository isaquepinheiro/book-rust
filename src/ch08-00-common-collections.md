# Coleções Comuns

A biblioteca padrão do Rust inclui uma série de estruturas de dados muito úteis chamadas *coleções*. A maioria dos outros tipos de dados representa um valor específico, mas as coleções podem conter vários valores. Ao contrário dos tipos de array e tupla incorporados, os dados apontados por essas coleções são armazenados no heap, o que significa que a quantidade de dados não precisa ser conhecida em tempo de compilação e pode crescer ou encolher à medida que o programa é executado. Cada tipo de coleção tem capacidades e custos diferentes, e escolher a adequada para a situação atual é uma habilidade que você desenvolverá ao longo do tempo. Neste capítulo, discutiremos três coleções que são usadas com muita frequência em programas Rust:

* Um *vetor* permite que você armazene um número variável de valores um ao lado do outro.
* Uma *string* é uma coleção de caracteres. Já mencionamos o tipo `String` anteriormente, mas neste capítulo falaremos sobre ele em profundidade.
* Um *hash map* permite que você associe um valor a uma chave específica. É uma implementação específica da estrutura de dados mais geral chamada *mapa*.

Para aprender sobre outros tipos de coleções fornecidos pela biblioteca padrão, consulte [a documentação][collections].

Discutiremos como criar e atualizar vetores, strings e hash maps, além de explicar o que torna cada um deles especial.

[collections]: ../std/collections/index.html
