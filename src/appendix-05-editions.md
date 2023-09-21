## Apêndice E - Edições

No Capítulo 1, você viu que `cargo new` adiciona um pouco de metadados ao seu arquivo *Cargo.toml* sobre uma edição. Este apêndice fala sobre o que isso significa!

A linguagem Rust e o compilador têm um ciclo de lançamento de seis semanas, o que significa que os usuários recebem constantemente novos recursos. Outras linguagens de programação lançam alterações maiores com menos frequência; Rust lança atualizações menores com mais frequência. Com o tempo, todas essas pequenas mudanças se acumulam. Mas de um lançamento para outro, pode ser difícil olhar para trás e dizer: "Uau, entre o Rust 1.10 e o Rust 1.31, o Rust mudou bastante!"

A cada dois ou três anos, a equipe Rust lança uma nova *edição* do Rust. Cada edição reúne os recursos que foram incorporados em um pacote claro, com documentação e ferramentas completamente atualizadas. Novas edições são lançadas como parte do processo de lançamento de seis semanas habitual.

As edições servem a diferentes propósitos para diferentes pessoas:

- Para usuários ativos do Rust, uma nova edição reúne mudanças incrementais em um pacote fácil de entender.
- Para não-usuários, uma nova edição sinaliza que alguns avanços importantes foram feitos, o que pode tornar o Rust digno de uma nova análise.
- Para aqueles que desenvolvem em Rust, uma nova edição fornece um ponto de referência para o projeto como um todo.

No momento em que este texto foi escrito, três edições do Rust estão disponíveis: Rust 2015, Rust 2018 e Rust 2021. Este livro foi escrito usando os idiomas da edição Rust 2021.

A chave `edition` no arquivo *Cargo.toml* indica qual edição o compilador deve usar para o seu código. Se a chave não existir, o Rust usará `2015` como o valor da edição por razões de compatibilidade com versões anteriores.

Cada projeto pode optar por usar uma edição diferente da edição padrão de `2015`. Edições podem conter mudanças incompatíveis, como a inclusão de uma nova palavra-chave que entra em conflito com identificadores no código. No entanto, a menos que você opte por essas mudanças, seu código continuará a compilar mesmo quando você atualizar a versão do compilador Rust que está usando.

Todas as versões do compilador Rust suportam qualquer edição que existia antes do lançamento desse compilador e podem vincular bibliotecas de qualquer edição suportada. As mudanças de edição afetam apenas a forma como o compilador analisa inicialmente o código. Portanto, se você estiver usando Rust 2015 e uma de suas dependências estiver usando Rust 2018, seu projeto ainda será compilado e poderá usar essa dependência. A situação oposta, em que seu projeto usa Rust 2018 e uma dependência usa Rust 2015, também funciona.

Para ser claro: a maioria dos recursos estará disponível em todas as edições. Desenvolvedores que usam qualquer edição do Rust continuarão a ver melhorias à medida que novos lançamentos estáveis são feitos. No entanto, em alguns casos, principalmente quando novas palavras-chave são adicionadas, alguns novos recursos podem estar disponíveis apenas em edições posteriores. Você precisará mudar de edição se quiser aproveitar esses recursos.

Para obter mais detalhes, o [*Guia de Edições*](https://doc.rust-lang.org/stable/edition-guide/) é um livro completo sobre edições que enumera as diferenças entre as edições e explica como atualizar automaticamente seu código para uma nova edição via `cargo fix`.
