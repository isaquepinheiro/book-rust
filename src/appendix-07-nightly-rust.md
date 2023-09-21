## Apêndice G - Como o Rust é Criado e o "Rust Noturno"

Este apêndice trata de como o Rust é desenvolvido e como isso afeta você como desenvolvedor Rust.

### Estabilidade Sem Estagnação

Como linguagem, o Rust se preocupa *muito* com a estabilidade do seu código. Queremos que o Rust seja uma base sólida na qual você possa construir, e se as coisas estiverem mudando constantemente, isso seria impossível. Ao mesmo tempo, se não pudermos experimentar com novos recursos, podemos não descobrir falhas importantes até depois de seu lançamento, quando não podemos mais fazer alterações.

Nossa solução para esse problema é o que chamamos de "estabilidade sem estagnação", e nosso princípio orientador é o seguinte: você nunca deve temer a atualização para uma nova versão estável do Rust. Cada atualização deve ser tranquila, mas também deve trazer novos recursos, menos bugs e tempos de compilação mais rápidos.

### Piui piui! Canais de Lançamento e Viajando nos Trens

O desenvolvimento do Rust opera em um *cronograma de trem*. Isso significa que todo o desenvolvimento é feito no ramo `master` do repositório do Rust. Os lançamentos seguem um modelo de lançamento de software semelhante a um trem, que foi usado pela Cisco IOS e outros projetos de software. Existem três *canais de lançamento* para o Rust:

* Noturno (Nightly)
* Beta
* Estável (Stable)

A maioria dos desenvolvedores Rust utiliza principalmente o canal estável, mas aqueles que desejam experimentar novos recursos experimentais podem usar o noturno ou o beta.

Aqui está um exemplo de como funciona o processo de desenvolvimento e lançamento: vamos supor que a equipe Rust está trabalhando no lançamento do Rust 1.5. Esse lançamento ocorreu em dezembro de 2015, mas isso nos fornecerá números de versão realistas. Um novo recurso é adicionado ao Rust: um novo commit é incorporado ao ramo `master`. A cada noite, uma nova versão noturna do Rust é produzida. Todos os dias são dias de lançamento, e esses lançamentos são criados automaticamente pela nossa infraestrutura de lançamento. Portanto, à medida que o tempo passa, nossos lançamentos parecem assim, uma vez por noite:

```text
nightly: * - - * - - *
```

A cada seis semanas, é hora de preparar um novo lançamento! O ramo `beta` do repositório Rust se separa do ramo `master` usado pela versão noturna. Agora, existem dois lançamentos:

```text
nightly: * - - * - - *
                     |
beta:                *
```

A maioria dos usuários do Rust não utiliza as versões beta ativamente, mas as testa em seus sistemas de integração contínua (CI) para ajudar o Rust a descobrir possíveis regressões. Enquanto isso, ainda há uma versão noturna a cada noite:

```text
nightly: * - - * - - * - - * - - *
                     |
beta:                *
```

Digamos que uma regressão seja encontrada. É bom que tivemos algum tempo para testar a versão beta antes que a regressão se infiltrasse em uma versão estável! A correção é aplicada ao `master`, para que a versão noturna seja corrigida, e então a correção é retroportada para o ramo `beta`, e uma nova versão beta é produzida:

```text
nightly: * - - * - - * - - * - - * - - *
                     |
beta:                * - - - - - - - - *
```

Seis semanas após a criação do primeiro beta, é hora de um lançamento estável! O ramo `stable` é produzido a partir do ramo `beta`:

```text
nightly: * - - * - - * - - * - - * - - * - * - *
                     |
beta:                * - - - - - - - - *
                                       |
stable:                                *
```

Viva! O Rust 1.5 está pronto! No entanto, esquecemos uma coisa: como se passaram seis semanas, também precisamos de um novo beta da *próxima* versão do Rust, a 1.6. Portanto, após o `stable` ser criado a partir do `beta`, a próxima versão do `beta` é criada novamente a partir do `nightly`:

```text
nightly: * - - * - - * - - * - - * - - * - * - *
                     |                         |
beta:                * - - - - - - - - *       *
                                       |
stable:                                *
```

Isso é chamado de "modelo de trem" porque a cada seis semanas, um lançamento "deixa a estação", mas ainda precisa passar por um trajeto pelo canal beta antes de se tornar um lançamento estável.

O Rust lança a cada seis semanas, como um relógio. Se você souber a data de um lançamento do Rust, poderá saber a data do próximo: é seis semanas depois. Um aspecto positivo de ter lançamentos programados a cada seis semanas é que o próximo trem está chegando em breve. Se um recurso não for concluído a tempo para um lançamento específico, não há motivo para se preocupar: outro lançamento ocorrerá em breve! Isso ajuda a reduzir a pressão para incluir recursos possivelmente não refinados perto do prazo de lançamento.

Graças a esse processo, você sempre pode verificar a próxima compilação do Rust e verificar por si mesmo que é fácil fazer a atualização: se um lançamento beta não funcionar como esperado, você pode relatá-lo à equipe e corrigi-lo antes do próximo lançamento estável! Problemas em um lançamento beta são relativamente raros, mas o `rustc` ainda é um software e bugs existem.

### Recursos Instáveis

Há mais uma particularidade com este modelo de lançamento: recursos instáveis. O Rust utiliza uma técnica chamada "flags de recursos" para determinar quais recursos estão habilitados em um determinado lançamento. Se um novo recurso estiver em desenvolvimento ativo, ele é incorporado ao `master`, e, portanto, à versão noturna, mas fica atrás de uma *flag de recurso*. Se você, como usuário, deseja experimentar o recurso em desenvolvimento, pode fazê-lo, mas deve estar usando uma versão noturna do Rust e anotar seu código-fonte com a flag apropriada para optar por usá-lo.

Se você estiver usando uma versão beta ou estável do Rust, não poderá usar nenhuma flag de recurso. Esta é a chave que nos permite obter uso prático com novos recursos antes de declará-los como estáveis permanentemente. Aqueles que desejam aderir à vanguarda podem fazê-lo, e aqueles que desejam uma experiência sólida podem permanecer com a versão estável e ter a certeza de que seu código não quebrará. Estabilidade sem estagnação.

Este livro contém apenas informações sobre recursos estáveis, já que os recursos em desenvolvimento ainda estão em evolução e com certeza serão diferentes entre quando este livro foi escrito e quando forem habilitados nas compilações estáveis. Você pode encontrar documentação para recursos exclusivos da versão noturna online.

### Rustup e o Papel do Rust Nightly

O Rustup facilita a troca entre diferentes canais de lançamento do Rust, em nível global ou por projeto. Por padrão, você terá o Rust estável instalado. Para instalar o nightly, por exemplo:

```console
$ rustup toolchain install nightly
```

Você também pode ver todas as *toolchains* (lançamentos do Rust e componentes associados) que você possui instalados com o `rustup`. Aqui está um exemplo em um computador Windows de um dos seus autores:

```powershell
> rustup toolchain list
stable-x86_64-pc-windows-msvc (default)
beta-x86_64-pc-windows-msvc
nightly-x86_64-pc-windows-msvc
```

Como você pode ver, a *toolchain* estável é a padrão. A maioria dos usuários do Rust utiliza a estável na maioria das vezes. Você pode querer usar a estável na maior parte do tempo, mas usar a noturna em um projeto específico, porque se interessa por um recurso de ponta. Para fazer isso, você pode usar `rustup override` no diretório do projeto para definir a *toolchain* noturna como aquela que o `rustup` deve usar quando estiver naquele diretório:

```console
$ cd ~/projects/needs-nightly
$ rustup override set nightly
```

Agora, toda vez que você chamar `rustc` ou `cargo` dentro de
*~/projects/needs-nightly*, o `rustup` garantirá que você esteja usando o Rust noturno, em vez do seu padrão que é o Rust estável. Isso é útil quando você tem muitos projetos em Rust!

### O Processo RFC e as Equipes

Então, como você pode aprender sobre esses novos recursos? O modelo de desenvolvimento do Rust segue um *processo de Pedido de Comentários (RFC, na sigla em inglês)*. Se você deseja uma melhoria no Rust, pode elaborar uma proposta, chamada de RFC.

Qualquer pessoa pode escrever RFCs para melhorar o Rust, e as propostas são revisadas e discutidas pela equipe do Rust, que é composta por várias subequipes de tópicos. Há uma lista completa das equipes [no site do Rust](https://www.rust-lang.org/governance), que inclui equipes para cada área do projeto: design de linguagem, implementação do compilador, infraestrutura, documentação e muito mais. A equipe apropriada lê a proposta e os comentários, escreve alguns comentários próprios e, eventualmente, há um consenso para aceitar ou rejeitar o recurso.

Se o recurso for aceito, uma issue é aberta no repositório do Rust e alguém pode implementá-lo. A pessoa que o implementa muito bem pode não ser a mesma que propôs o recurso em primeiro lugar! Quando a implementação estiver pronta, ela é incorporada à ramificação `master` atrás de uma *feature gate*, como discutido na seção ["Recursos Instáveis"](###-recursos-instáveis)<!-- ignore -->.

Após algum tempo, quando os desenvolvedores do Rust que usam lançamentos noturnos tiverem a oportunidade de experimentar o novo recurso, os membros da equipe discutirão o recurso, como ele funcionou no noturno e decidirão se ele deve ou não ser incorporado ao Rust estável. Se a decisão for avançar, a *feature gate* é removida, e o recurso agora é considerado estável! Ele viaja nos trens para um novo lançamento estável do Rust.
