Então, como você decide quando deve chamar `panic!` e quando deve retornar `Result`? Quando o código entra em pânico, não há como se recuperar. Você poderia chamar `panic!` para qualquer situação de erro, seja ou não possível recuperar, mas então você está tomando a decisão de que uma situação é irreparável em nome do código chamador. Quando você opta por retornar um valor `Result`, você dá opções ao código chamador. O código chamador pode escolher tentar se recuperar de uma maneira apropriada para sua situação, ou pode decidir que um valor `Err` neste caso é irrecuperável, então pode chamar `panic!` e transformar seu erro recuperável em um erro irreparável. Portanto, retornar `Result` é uma boa escolha padrão quando você está definindo uma função que pode falhar.

Em situações como exemplos, código de protótipo e testes, é mais apropriado escrever código que entra em pânico em vez de retornar um `Result`. Vamos explorar por que isso acontece e depois discutir situações em que o compilador não pode determinar que a falha é impossível, mas você, como humano, pode. O capítulo concluirá com algumas diretrizes gerais sobre como decidir se deve entrar em pânico em código de biblioteca.

Quando você está escrevendo um exemplo para ilustrar algum conceito, incluir código robusto de tratamento de erros também pode tornar o exemplo menos claro. Em exemplos, é compreendido que uma chamada a um método como `unwrap` que poderia entrar em pânico é destinada como um espaço reservado para a forma como você gostaria que sua aplicação tratasse erros, o que pode variar com base no que o resto do seu código está fazendo.

Da mesma forma, os métodos `unwrap` e `expect` são muito úteis durante a prototipagem, antes de você estar pronto para decidir como lidar com erros. Eles deixam marcadores claros em seu código para quando você estiver pronto para tornar seu programa mais robusto.

Se uma chamada de método falhar em um teste, você desejará que o teste inteiro falhe, mesmo que esse método não seja a funcionalidade em teste. Como `panic!` é como um teste é marcado como falha, chamar `unwrap` ou `expect` é exatamente o que deve acontecer.

Também seria apropriado chamar `unwrap` ou `expect` quando você tem alguma outra lógica que garante que o `Result` terá um valor `Ok`, mas a lógica não é algo que o compilador compreende. Ainda assim, você terá um valor `Result` que precisa lidar: qualquer operação que você está chamando ainda tem a possibilidade de falhar em geral, mesmo que seja logicamente impossível em sua situação específica. Se você puder garantir, examinando manualmente o código, que nunca terá uma variante `Err`, é perfeitamente aceitável chamar `unwrap`, e ainda melhor documentar o motivo pelo qual você acredita que nunca terá uma variante `Err` no texto do `expect`. Aqui está um exemplo:

```rust
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-08-unwrap-that-cant-fail/src/main.rs:here}}
```

Estamos criando uma instância de `IpAddr` analisando uma string codificada. Podemos ver que `127.0.0.1` é um endereço IP válido, então é aceitável usar `expect` aqui. No entanto, ter uma string válida codificada não altera o tipo de retorno do método `parse`: ainda obtemos um valor `Result`, e o compilador ainda nos forçará a lidar com o `Result` como se a variante `Err` fosse uma possibilidade, porque o compilador não é inteligente o suficiente para ver que essa string é sempre um endereço IP válido. Se a string do endereço IP viesse de um usuário em vez de estar codificada no programa e, portanto, *tivesse* uma possibilidade de falha, definitivamente gostaríamos de lidar com o `Result` de uma maneira mais robusta. Mencionar a suposição de que este endereço IP está codificado nos levará a mudar `expect` para um código de tratamento de erros melhor se, no futuro, precisarmos obter o endereço IP de alguma outra fonte.

### Diretrizes para o Tratamento de Erros

É aconselhável que seu código entre em pânico (panic) quando for possível que ele entre em um estado ruim. Neste contexto, um *estado ruim* ocorre quando alguma suposição, garantia, contrato ou invariante tenha sido quebrada, como quando valores inválidos, valores contraditórios ou valores ausentes são passados para o seu código, junto com um ou mais dos seguintes:

* O estado ruim é algo inesperado, ao contrário de algo que provavelmente ocorrerá ocasionalmente, como um usuário inserindo dados no formato errado.
* Seu código, após este ponto, precisa confiar em não estar neste estado ruim, em vez de verificar o problema a cada passo.
* Não há uma maneira adequada de codificar esta informação nos tipos que você utiliza. Vamos trabalhar através de um exemplo do que queremos dizer na seção [“Codificando Estados e Comportamento como Tipos”][encoding]<!-- ignore --> do Capítulo 17.

Se alguém chamar o seu código e passar valores que não fazem sentido, é melhor retornar um erro, se possível, para que o usuário da biblioteca possa decidir o que fazer nesse caso. No entanto, em casos em que continuar poderia ser inseguro ou prejudicial, a melhor escolha pode ser chamar `panic!` e alertar a pessoa que está usando a sua biblioteca sobre o erro no código delas, para que elas possam corrigi-lo durante o desenvolvimento. Da mesma forma, `panic!` é frequentemente apropriado se você estiver chamando código externo que está fora do seu controle e ele retornar um estado inválido que você não pode corrigir.

No entanto, quando a falha é esperada, é mais apropriado retornar um `Result` do que fazer uma chamada a `panic!`. Exemplos incluem um analisador recebendo dados malformados ou uma solicitação HTTP retornando um status que indica que você atingiu um limite de taxa. Nesses casos, retornar um `Result` indica que a falha é uma possibilidade esperada que o código chamador deve decidir como lidar.

Quando o seu código realiza uma operação que poderia colocar um usuário em risco se for chamado com valores inválidos, o seu código deve verificar se os valores são válidos primeiro e entrar em pânico se os valores não forem válidos. Isso ocorre principalmente por motivos de segurança: tentar operar em dados inválidos pode expor o seu código a vulnerabilidades. Essa é a principal razão pela qual a biblioteca padrão chamará `panic!` se você tentar acessar memória fora dos limites: tentar acessar memória que não pertence à estrutura de dados atual é um problema de segurança comum. Funções frequentemente têm *contratos*: o comportamento delas só é garantido se as entradas atenderem a requisitos específicos. Entrar em pânico quando o contrato é violado faz sentido porque uma violação de contrato sempre indica um erro no lado do chamador, e não é um tipo de erro que você deseja que o código chamador tenha que lidar explicitamente. Na verdade, não há uma maneira razoável para o código chamador se recuperar; os *programadores* que estão chamando precisam corrigir o código. Os contratos para uma função, especialmente quando uma violação causará um pânico, devem ser explicados na documentação da API da função.

No entanto, ter muitas verificações de erros em todas as suas funções seria verboso e irritante. Felizmente, você pode usar o sistema de tipos do Rust (e, portanto, a verificação de tipos feita pelo compilador) para realizar muitas das verificações para você. Se sua função tem um tipo específico como parâmetro, você pode prosseguir com a lógica do seu código sabendo que o compilador já garantiu que você tem um valor válido. Por exemplo, se você tem um tipo em vez de uma `Option`, seu programa espera ter *algo* em vez de *nada*. Seu código então não precisa lidar com dois casos para as variantes `Some` e `None`: ele terá apenas um caso para definitivamente ter um valor. O código que tentar passar nada para sua função nem mesmo será compilado, portanto, sua função não precisa verificar esse caso em tempo de execução. Outro exemplo é o uso de um tipo de inteiro sem sinal, como `u32`, que garante que o parâmetro nunca seja negativo.

Vamos levar a ideia de usar o sistema de tipos do Rust para garantir que temos um valor válido um passo adiante e ver como criar um tipo personalizado para validação. Lembra-se do jogo de adivinhação no Capítulo 2, em que nosso código pedia ao usuário para adivinhar um número entre 1 e 100? Nunca validamos que o palpite do usuário estava dentro desses números antes de verificar contra o nosso número secreto; só validamos que o palpite era positivo. Nesse caso, as consequências não foram muito graves: nossa saída de "Muito alto" ou "Muito baixo" ainda estaria correta. Mas seria um aprimoramento útil guiar o usuário em direção a palpites válidos e ter um comportamento diferente quando um usuário adivinha um número fora do intervalo, em comparação com quando um usuário digita, por exemplo, letras.

Uma maneira de fazer isso seria analisar o palpite como um `i32` em vez de apenas um `u32` para permitir números potencialmente negativos e, em seguida, adicionar uma verificação para garantir que o número esteja dentro do intervalo, da seguinte forma:

```rust,ignore
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-09-guess-out-of-range/src/main.rs:here}}
```

A expressão `if` verifica se o nosso valor está fora do intervalo, informa ao usuário sobre o problema e chama `continue` para iniciar a próxima iteração do loop e pedir outro palpite. Após a expressão `if`, podemos prosseguir com as comparações entre `guess` e o número secreto sabendo que `guess` está entre 1 e 100.

No entanto, esta não é uma solução ideal: se fosse absolutamente crucial que o programa operasse apenas com valores entre 1 e 100 e tivesse muitas funções com esse requisito, ter uma verificação como essa em cada função seria tedioso (e poderia afetar o desempenho).

Em vez disso, podemos criar um novo tipo e colocar as validações em uma função para criar uma instância desse tipo em vez de repetir as validações em todos os lugares. Dessa forma, é seguro para as funções usarem o novo tipo em suas assinaturas e usarem com confiança os valores que recebem. A Listagem 9-13 mostra uma maneira de definir um tipo `Guess` que só criará uma instância de `Guess` se a função `new` receber um valor entre 1 e 100.

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file requires the `rand` crate. We do want to include it for reader
experimentation purposes, but don't want to include it for rustdoc testing
purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-13/src/main.rs:here}}
```

<span class="caption">Listagem 9-13: Um tipo `Guess` que só continuará com valores entre 1 e 100.</span>

Primeiro, definimos uma struct chamada `Guess` que possui um campo chamado `value` que armazena um `i32`. É aqui que o número será armazenado.

Em seguida, implementamos uma função associada chamada `new` em `Guess` que cria instâncias de valores `Guess`. A função `new` é definida para ter um parâmetro chamado `value` do tipo `i32` e para retornar um `Guess`. O código no corpo da função `new` testa o `value` para garantir que ele esteja entre 1 e 100. Se o `value` não passar neste teste, fazemos uma chamada para `panic!`, o que alertará o programador que está escrevendo o código de chamada de que eles têm um bug para corrigir, porque criar um `Guess` com um `value` fora desse intervalo violaria o contrato em que `Guess::new` está se baseando. As condições em que `Guess::new` pode entrar em pânico devem ser discutidas na documentação pública da API; abordaremos as convenções de documentação que indicam a possibilidade de um `panic!` na documentação da API que você cria no Capítulo 14. Se o `value` passar no teste, criamos um novo `Guess` com o campo `value` definido para o parâmetro `value` e retornamos o `Guess`.

Em seguida, implementamos um método chamado `value` que faz uma referência a `self`, não tem outros parâmetros e retorna um `i32`. Esse tipo de método é às vezes chamado de *getter*, porque seu objetivo é obter alguns dados de seus campos e retorná-los. Esse método público é necessário porque o campo `value` da struct `Guess` é privado. É importante que o campo `value` seja privado para que o código que usa a struct `Guess` não possa definir `value` diretamente: o código fora do módulo *deve* usar a função `Guess::new` para criar uma instância de `Guess`, garantindo assim que não há maneira de um `Guess` ter um `value` que não tenha sido verificado pelas condições na função `Guess::new`.

Uma função que possui um parâmetro ou retorna apenas números entre 1 e 100 pode então declarar em sua assinatura que aceita ou retorna um `Guess` em vez de um `i32` e não precisaria fazer verificações adicionais em seu corpo.

## Resumo

As funcionalidades de tratamento de erros do Rust são projetadas para ajudá-lo a escrever código mais robusto. O macro `panic!` sinaliza que o programa está em um estado que ele não pode lidar e permite que você pare o processo em vez de tentar prosseguir com valores inválidos ou incorretos. A enumeração `Result` usa o sistema de tipos do Rust para indicar que as operações podem falhar de uma maneira que seu código poderia se recuperar. Você pode usar `Result` para informar ao código que chama seu código que ele precisa lidar com o sucesso ou a falha potencial também. Usar `panic!` e `Result` nas situações apropriadas tornará seu código mais confiável diante de problemas inevitáveis.

Agora que você viu maneiras úteis de como a biblioteca padrão usa generics com as enumerações `Option` e `Result`, falaremos sobre como os generics funcionam e como você pode usá-los em seu código.

[encoding]: ch17-03-oo-design-patterns.html#encoding-states-and-behavior-as-types
