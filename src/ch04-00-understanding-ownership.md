# Compreendendo a Propriedade

A propriedade (ownership) é a característica mais única do Rust e tem profundas implicações para o restante da linguagem. Ela permite ao Rust fazer garantias de segurança de memória sem necessidade de um coletor de lixo (garbage collector), portanto, é importante entender como a propriedade funciona. Neste capítulo, falaremos sobre a propriedade, bem como várias características relacionadas: empréstimos (borrowing), slices e como o Rust organiza dados na memória.
