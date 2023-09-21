# A Linguagem de Programação Rust

[A Linguagem de Programação Rust](title-page.md)
[Prefácio](foreword.md)
[Introdução](ch00-00-introduction.md)

## Começando

- [Iniciando](ch01-00-getting-started.md)
    - [Instalação](ch01-01-installation.md)
    - [Olá, Mundo!](ch01-02-hello-world.md)
    - [Olá, Cargo!](ch01-03-hello-cargo.md)

- [Programando um Jogo de Adivinhação](ch02-00-guessing-game-tutorial.md)

- [Conceitos de Programação Comuns](ch03-00-common-programming-concepts.md)
    - [Variáveis e Mutabilidade](ch03-01-variables-and-mutability.md)
    - [Tipos de Dados](ch03-02-data-types.md)
    - [Funções](ch03-03-how-functions-work.md)
    - [Comentários](ch03-04-comments.md)
    - [Fluxo de Controle](ch03-05-control-flow.md)

- [Compreendendo a Propriedade](ch04-00-understanding-ownership.md)
    - [O que é a Propriedade?](ch04-01-what-is-ownership.md)
    - [Referências e Empréstimos](ch04-02-references-and-borrowing.md)
    - [O Tipo Slice](ch04-03-slices.md)

- [Usando Structs para Estruturar Dados Relacionados](ch05-00-structs.md)
    - [Definindo e Instanciando Structs](ch05-01-defining-structs.md)
    - [Um Programa de Exemplo Usando Structs](ch05-02-example-structs.md)
    - [Sintaxe de Métodos](ch05-03-method-syntax.md)

- [Enums e Correspondência de Padrões](ch06-00-enums.md)
    - [Definindo um Enum](ch06-01-defining-an-enum.md)
    - [O Construto de Controle `match`](ch06-02-match.md)
    - [Fluxo de Controle Conciso com `if let`](ch06-03-if-let.md)

## Conhecimento Básico em Rust

- [Gerenciando Projetos em Crescimento com Pacotes, Crates e Módulos](ch07-00-managing-growing-projects-with-packages-crates-and-modules.md)
    - [Pacotes e Crates](ch07-01-packages-and-crates.md)
    - [Definindo Módulos para Controlar Escopo e Privacidade](ch07-02-defining-modules-to-control-scope-and-privacy.md)
    - [Caminhos para Referenciar um Item na Árvore de Módulos](ch07-03-paths-for-referring-to-an-item-in-the-module-tree.md)
    - [Trabalhando com Caminhos Usando a Palavra-chave `use`](ch07-04-bringing-paths-into-scope-with-the-use-keyword.md)
    - [Separando Módulos em Arquivos Diferentes](ch07-05-separating-modules-into-different-files.md)

- [Coleções Comuns](ch08-00-common-collections.md)
    - [Armazenando Listas de Valores com Vetores](ch08-01-vectors.md)
    - [Armazenando Texto Codificado em UTF-8 com Strings](ch08-02-strings.md)
    - [Armazenando Chaves com Valores Associados em Mapas de Hash](ch08-03-hash-maps.md)

- [Tratamento de Erros](ch09-00-error-handling.md)
    - [Erros Irrecuperáveis com `panic!`](ch09-01-unrecoverable-errors-with-panic.md)
    - [Erros Recuperáveis com `Result`](ch09-02-recoverable-errors-with-result.md)
    - [Usando ou Não `panic!`](ch09-03-to-panic-or-not-to-panic.md)

- [Tipos Genéricos, Traits e Lifetimes](ch10-00-generics.md)
    - [Tipos Genéricos de Dados](ch10-01-syntax.md)
    - [Traits: Definindo Comportamentos Compartilhados](ch10-02-traits.md)
    - [Validando Referências com Lifetimes](ch10-03-lifetime-syntax.md)

- [Escrevendo Testes Automatizados](ch11-00-testing.md)
    - [Como Escrever Testes](ch11-01-writing-tests.md)
    - [Controlando a Execução de Testes](ch11-02-running-tests.md)
    - [Organização de Testes](ch11-03-test-organization.md)

- [Um Projeto de E/S: Construindo um Programa de Linha de Comando](ch12-00-an-io-project.md)
    - [Aceitando Argumentos da Linha de Comando](ch12-01-accepting-command-line-arguments.md)
    - [Lendo um Arquivo](ch12-02-reading-a-file.md)
    - [Refatorando para Melhorar Modularidade e Tratamento de Erros](ch12-03-improving-error-handling-and-modularity.md)
    - [Desenvolvendo a Funcionalidade da Biblioteca com Desenvolvimento Orientado a Testes](ch12-04-testing-the-librarys-functionality.md)
    - [Trabalhando com Variáveis de Ambiente](ch12-05-working-with-environment-variables.md)
    - [Escrevendo Mensagens de Erro na Saída de Erro Padrão em Vez de Saída Padrão](ch12-06-writing-to-stderr-instead-of-stdout.md)

## Pensando em Rust

- [Recursos de Linguagem Funcional: Iteradores e Closures](ch13-00-functional-features.md)
    - [Closures: Funções Anônimas que Capturam seu Ambiente](ch13-01-closures.md)
    - [Processando uma Série de Itens com Iteradores](ch13-02-iterators.md)
    - [Melhorando Nosso Projeto de E/S](ch13-03-improving-our-io-project.md)
    - [Comparando Desempenho: Loops vs. Iteradores](ch13-04-performance.md)

- [Mais sobre Cargo e Crates.io](ch14-00-more-about-cargo.md)
    - [Customizando Builds com Perfis de Release](ch14-01-release-profiles.md)
    - [Publicando uma Crate no Crates.io](ch14-02-publishing-to-crates-io.md)
    - [Workspaces do Cargo](ch14-03-cargo-workspaces.md)
    - [Instalando Binários do Crates.io com `cargo install`](ch14-04-installing-binaries.md)
    - [Estendendo o Cargo com Comandos Personalizados](ch14-05-extending-cargo.md)

- [Ponteiros Inteligentes](ch15-00-smart-pointers.md)
    - [Usando `Box<T>` para Apontar para Dados no Heap](ch15-01-box.md)
    - [Tratando Ponteiros Inteligentes como Referências Comuns com o Trait `Deref`](ch15-02-deref.md)
    - [Executando Código na Limpeza com o Trait `Drop`](ch15-03-drop.md)
    - [`Rc<T>`, o Ponteiro Inteligente com Contagem de Referência](ch15-04-rc.md)
    - [`RefCell<T>` e o Padrão de Mutabilidade Interior](ch15-05-interior-mutability.md)
    - [Ciclos de Referência Podem Causar Vazamento de Memória](ch15-06-reference-cycles.md)

- [Concorrência sem Medo](ch16-00-concurrency.md)
    - [Usando Threads para Executar Código Simultaneamente](ch16-01-threads.md)
    - [Usando Passagem de Mensagem para Transferir Dados Entre Threads](ch16-02-message-passing.md)
    - [Concorrência com Estado Compartilhado](ch16-03-shared-state.md)
    - [Concorrência Extensível com os Traits `Sync` e `Send`](ch16-04-extensible-concurrency-sync-and-send.md)

- [Recursos de Programação Orientada a Objetos em Rust](ch17-00-oop.md)
    - [Características de Linguagens Orientadas a Objetos](ch17-01-what-is-oo.md)
    - [Usando Trait Objects que Permitem Valores de Tipos Diferentes](ch17-02-trait-objects.md)
    - [Implementando um Padrão de Design Orientado a Objetos](ch17-03-oo-design-patterns.md)

## Tópicos Avançados

- [Padrões e Correspondência](ch18-00-patterns.md)
    - [Todos os Lugares Onde Padrões Podem Ser Usados](ch18-01-all-the-places-for-patterns.md)
    - [Refutabilidade: Se um Padrão Pode Falhar em Correspondência](ch18-02-refutability.md)
    - [Sintaxe de Padrões](ch18-03-pattern-syntax.md)

- [Recursos Avançados](ch19-00-advanced-features.md)
    - [Rust Inseguro](ch19-01-unsafe-rust.md)
    - [Traits Avançados](ch19-03-advanced-traits.md)
    - [Tipos Avançados](ch19-04-advanced-types.md)
    - [Funções e Closures Avançados](ch19-05-advanced-functions-and-closures.md)
    - [Macros](ch19-06-macros.md)

- [Projeto Final: Construindo um Servidor Web Multithreaded](ch20-00-final-project-a-web-server.md)
    - [Construindo um Servidor Web de Thread Única](ch20-01-single-threaded.md)
    - [Transformando Nosso Servidor de Thread Única em um Servidor Multithreaded](ch20-02-multithreaded.md)
    - [Desligamento Gracioso e Limpeza](ch20-03-graceful-shutdown-and-cleanup.md)

- [Apêndice](appendix-00.md)
    - [A - Palavras-chave](appendix-01-keywords.md)
    - [B - Operadores e Símbolos](appendix-02-operators.md)
    - [C - Traits Deriváveis](appendix-03-derivable-traits.md)
    - [D - Ferramentas de Desenvolvimento Úteis](appendix-04-useful-development-tools.md)
    - [E - Edições](appendix-05-editions.md)
    - [F - Traduções do Livro](appendix-06-translation.md)
    - [G - Como o Rust é Feito e o "Rust Noturno"](appendix-07-nightly-rust.md)
