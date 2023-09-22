# Gerenciando Projetos em Crescimento com Pacotes, Caixas e Módulos

À medida que você escreve programas grandes, a organização do seu código se torna cada vez mais importante. Ao agrupar funcionalidades relacionadas e separar o código com características distintas, você esclarecerá onde encontrar o código que implementa uma funcionalidade específica e onde ir para alterar como uma funcionalidade funciona.

Os programas que escrevemos até agora estavam em um único módulo em um único arquivo. Conforme um projeto cresce, você deve organizar o código dividindo-o em vários módulos e, em seguida, em vários arquivos. Um pacote pode conter múltiplas caixas binárias e, opcionalmente, uma caixa de biblioteca. Conforme um pacote cresce, você pode extrair partes para caixas separadas que se tornam dependências externas. Este capítulo aborda todas essas técnicas. Para projetos muito grandes que compreendem um conjunto de pacotes inter-relacionados que evoluem juntos, o Cargo fornece *workspaces*, que abordaremos na seção [“Workspaces do Cargo”][workspaces]<!-- ignore --> no Capítulo 14.

Também discutiremos a encapsulação de detalhes de implementação, o que permite reutilizar o código em um nível mais alto: uma vez que você implementou uma operação, outros códigos podem chamar seu código por meio de sua interface pública sem precisar saber como a implementação funciona. A maneira como você escreve o código define quais partes são públicas para que outros códigos as utilizem e quais partes são detalhes de implementação privados que você reserva o direito de alterar. Essa é outra maneira de limitar a quantidade de detalhes que você precisa manter na sua cabeça.

Um conceito relacionado é o escopo: o contexto aninhado no qual o código é escrito tem um conjunto de nomes definidos como "em escopo". Ao ler, escrever e compilar código, programadores e compiladores precisam saber se um nome específico em um local específico se refere a uma variável, função, estrutura, enumeração, módulo, constante ou outro item, e o que esse item significa. Você pode criar escopos e alterar quais nomes estão dentro ou fora de escopo. Não é possível ter dois itens com o mesmo nome no mesmo escopo; ferramentas estão disponíveis para resolver conflitos de nomes.

O Rust possui várias funcionalidades que permitem gerenciar a organização do seu código, incluindo quais detalhes são expostos, quais detalhes são privados e quais nomes estão em cada escopo nos seus programas. Essas funcionalidades, às vezes referidas coletivamente como o *sistema de módulos*, incluem:

* **Pacotes:** Um recurso do Cargo que permite construir, testar e compartilhar caixas
* **Caixas:** Uma árvore de módulos que produz uma biblioteca ou um executável
* **Módulos** e **use:** Permitem controlar a organização, o escopo e a privacidade de caminhos
* **Caminhos:** Uma forma de nomear um item, como uma estrutura, função ou módulo

Neste capítulo, abordaremos todas essas funcionalidades, discutiremos como elas interagem e explicaremos como usá-las para gerenciar o escopo. No final, você deverá ter um entendimento sólido do sistema de módulos e ser capaz de trabalhar com escopos como um profissional!

[workspaces]: ch14-03-cargo-workspaces.html
