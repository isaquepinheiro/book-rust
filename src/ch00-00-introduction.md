# Introdução

> Nota: Esta edição do livro é a mesma que [The Rust Programming Language][nsprust], disponível em formato impresso e ebook pela [No Starch Press][nsp].

[nsprust]: https://nostarch.com/rust-programming-language-2nd-edition
[nsp]: https://nostarch.com/

Bem-vindo ao *The Rust Programming Language*, um livro introdutório sobre Rust. A linguagem de programação Rust ajuda você a escrever software mais rápido e confiável. A ergonomia de alto nível e o controle de baixo nível muitas vezes estão em conflito no design de linguagens de programação; Rust desafia esse conflito. Ao equilibrar capacidade técnica poderosa e uma ótima experiência de desenvolvedor, Rust oferece a opção de controlar detalhes de baixo nível (como o uso de memória) sem toda a complicação tradicionalmente associada a esse controle.

## Para Quem é o Rust

O Rust é ideal para muitas pessoas por diversas razões. Vamos dar uma olhada em alguns dos grupos mais importantes.

### Equipes de Desenvolvedores

O Rust está se mostrando uma ferramenta produtiva para colaboração entre grandes equipes de desenvolvedores com diferentes níveis de conhecimento em programação de sistemas. O código de baixo nível está sujeito a vários bugs sutis, que na maioria das outras linguagens só podem ser detectados por meio de extensos testes e revisões cuidadosas do código por desenvolvedores experientes. No Rust, o compilador desempenha o papel de gatekeeper ao se recusar a compilar código com esses bugs elusivos, incluindo bugs de concorrência. Ao trabalhar em conjunto com o compilador, a equipe pode gastar seu tempo concentrando-se na lógica do programa em vez de perseguir bugs.

O Rust também traz ferramentas de desenvolvimento contemporâneas para o mundo da programação de sistemas:

- Cargo, o gerenciador de dependências e ferramenta de compilação inclusos, torna a adição, compilação e gerenciamento de dependências indolores e consistentes em todo o ecossistema Rust.
- A ferramenta de formatação Rustfmt garante um estilo de codificação consistente entre os desenvolvedores.
- O Rust Language Server alimenta a integração com Ambiente de Desenvolvimento Integrado (IDE) para conclusão de código e mensagens de erro em linha.

Ao usar essas e outras ferramentas no ecossistema Rust, os desenvolvedores podem ser produtivos ao escrever código de nível de sistema.

### Estudantes

O Rust é para estudantes e aqueles que estão interessados em aprender sobre conceitos de sistemas. Usando o Rust, muitas pessoas aprenderam sobre tópicos como desenvolvimento de sistemas operacionais. A comunidade é muito acolhedora e feliz em responder às perguntas dos estudantes. Através de esforços como este livro, as equipes do Rust desejam tornar os conceitos de sistemas mais acessíveis a mais pessoas, especialmente aquelas que estão começando na programação.

### Empresas

Centenas de empresas, grandes e pequenas, utilizam o Rust em produção para uma variedade de tarefas, incluindo ferramentas de linha de comando, serviços web, ferramentas de DevOps, dispositivos embarcados, análise e transcodificação de áudio e vídeo, criptomoedas, bioinformática, mecanismos de busca, aplicativos de Internet das Coisas, aprendizado de máquina e até mesmo partes importantes do navegador web Firefox.

### Desenvolvedores de Código Aberto

O Rust é para pessoas que desejam contribuir para a linguagem de programação Rust, a comunidade, ferramentas de desenvolvedor e bibliotecas. Ficaríamos felizes em tê-lo contribuindo para a linguagem Rust.

### Pessoas que Valorizam Velocidade e Estabilidade

O Rust é para pessoas que valorizam velocidade e estabilidade em uma linguagem. Com velocidade, queremos dizer tanto o quão rapidamente o código Rust pode ser executado quanto a velocidade com que o Rust permite que você escreva programas. As verificações do compilador Rust garantem estabilidade por meio de adições de recursos e refatorações. Isso contrasta com o código legado frágil em linguagens sem essas verificações, que os desenvolvedores frequentemente têm medo de modificar. Ao buscar abstrações de custo zero, recursos de nível superior que compilam para código de nível inferior tão rápido quanto o código escrito manualmente, o Rust se esforça para fazer com que o código seguro seja também código rápido.

A linguagem Rust espera atender a muitos outros tipos de usuários; aqueles mencionados aqui são apenas alguns dos maiores interessados. Em geral, a maior ambição do Rust é eliminar as compensações que os programadores têm aceitado há décadas, fornecendo segurança *e* produtividade, velocidade *e* facilidade de uso. Experimente o Rust e veja se suas escolhas funcionam para você.

Este livro pressupõe que você já escreveu código em outra linguagem de programação, mas não faz suposições sobre qual. Tentamos tornar o material amplamente acessível para pessoas com uma ampla variedade de experiências em programação. Não gastamos muito tempo falando sobre o que é a programação ou como pensar sobre ela. Se você é completamente novo na programação, seria melhor ler um livro que forneça especificamente uma introdução à programação.

## Como Usar Este Livro

Em geral, este livro pressupõe que você o está lendo sequencialmente, da frente para trás. Capítulos posteriores constroem sobre conceitos apresentados em capítulos anteriores, e capítulos anteriores podem não aprofundar detalhes sobre um tópico específico, mas irão revisitar o tópico em um capítulo posterior.

Você encontrará dois tipos de capítulos neste livro: capítulos de conceitos e capítulos de projetos. Nos capítulos de conceitos, você aprenderá sobre um aspecto do Rust. Nos capítulos de projetos, construiremos pequenos programas juntos, aplicando o que você aprendeu até o momento. Os capítulos 2, 12 e 20 são capítulos de projetos; os demais são capítulos de conceitos.

O Capítulo 1 explica como instalar o Rust, como escrever um programa "Olá, mundo!" e como usar o Cargo, o gerenciador de pacotes e ferramenta de construção do Rust. O Capítulo 2 é uma introdução prática à escrita de um programa em Rust, onde você construirá um jogo de adivinhação de números. Aqui, cobrimos conceitos em alto nível, e capítulos posteriores fornecerão detalhes adicionais. Se você deseja começar a trabalhar imediatamente, o Capítulo 2 é o lugar para isso. O Capítulo 3 aborda recursos do Rust que são semelhantes aos de outras linguagens de programação, e no Capítulo 4, você aprenderá sobre o sistema de propriedade do Rust. Se você é um aprendiz especialmente meticuloso que prefere aprender todos os detalhes antes de avançar para o próximo, talvez queira pular o Capítulo 2 e ir direto para o Capítulo 3, retornando ao Capítulo 2 quando desejar trabalhar em um projeto aplicando os detalhes que aprendeu.

O Capítulo 5 discute estruturas e métodos, e o Capítulo 6 aborda enumerações, expressões `match` e a construção de fluxo de controle `if let`. Você usará estruturas e enumerações para criar tipos personalizados em Rust.

No Capítulo 7, você aprenderá sobre o sistema de módulos do Rust e sobre as regras de privacidade para organizar seu código e sua Interface de Programação de Aplicativos (API) pública. O Capítulo 8 discute algumas estruturas de dados de coleção comuns fornecidas pela biblioteca padrão, como vetores, strings e mapas de hash. O Capítulo 9 explora a filosofia e as técnicas de tratamento de erros do Rust.

O Capítulo 10 aborda os conceitos de genéricos, traits e lifetimes, que lhe dão o poder de definir código que se aplica a vários tipos. O Capítulo 11 trata de testes, que, mesmo com as garantias de segurança do Rust, são necessários para garantir que a lógica do seu programa esteja correta. No Capítulo 12, construiremos nossa própria implementação de uma parte da funcionalidade da ferramenta de linha de comando `grep`, que pesquisa texto dentro de arquivos. Para isso, usaremos muitos dos conceitos discutidos nos capítulos anteriores.

No Capítulo 13, exploramos closures e iteradores: recursos do Rust que vêm de linguagens de programação funcionais. No Capítulo 14, examinaremos o Cargo com mais profundidade e discutiremos as melhores práticas para compartilhar suas bibliotecas com outras pessoas. O Capítulo 15 discute ponteiros inteligentes que a biblioteca padrão fornece e os traits que habilitam sua funcionalidade.

No Capítulo 16, percorreremos diferentes modelos de programação concorrente e discutiremos como o Rust ajuda você a programar em várias threads com segurança. O Capítulo 17 examina como os idiomas do Rust se comparam aos princípios da programação orientada a objetos com os quais você pode estar familiarizado.

O Capítulo 18 é uma referência sobre padrões e correspondência de padrões, que são formas poderosas de expressar ideias em programas Rust. O Capítulo 19 contém uma miscelânea de tópicos avançados de interesse, incluindo Rust inseguro, macros e mais sobre lifetimes, traits, tipos, funções e closures.

No Capítulo 20, completaremos um projeto no qual implementaremos um servidor web multithreaded de baixo nível!

Finalmente, alguns apêndices contêm informações úteis sobre a linguagem em um formato mais semelhante a um guia de referência. O Apêndice A aborda as palavras-chave do Rust, o Apêndice B aborda os operadores e símbolos do Rust, o Apêndice C aborda os traits deriváveis fornecidos pela biblioteca padrão, o Apêndice D aborda algumas ferramentas de desenvolvimento úteis, e o Apêndice E explica as edições do Rust. No Apêndice F, você pode encontrar traduções do livro, e no Apêndice G, abordaremos como o Rust é criado e o que é o Rust noturno.

Não há maneira errada de ler este livro: se você quiser pular adiante, vá em frente! Você pode precisar voltar a capítulos anteriores se encontrar alguma confusão. Mas faça o que funcionar melhor para você.

<span id="ferris"></span>

Uma parte importante do processo de aprendizado do Rust é aprender a ler as mensagens de erro que o compilador exibe: essas mensagens o guiarão em direção ao código funcional. Como tal, forneceremos muitos exemplos que não compilam, juntamente com a mensagem de erro que o compilador mostrará em cada situação. Saiba que se você inserir e executar um exemplo aleatório, ele pode não ser compilado! Certifique-se de ler o texto circundante para ver se o exemplo que você está tentando executar é destinado a gerar um erro. Ferris também o ajudará a distinguir o código que não tem a intenção de funcionar:

| Ferris                                                                                                           | Significado                                      |
|------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| <img src="img/ferris/does_not_compile.svg" class="ferris-explain" alt="Ferris with a question mark"/>            | Este código não compila!                      |
| <img src="img/ferris/panics.svg" class="ferris-explain" alt="Ferris throwing up their hands"/>                   | Este código gera uma falha (panic)!                                |
| <img src="img/ferris/not_desired_behavior.svg" class="ferris-explain" alt="Ferris with one claw up, shrugging"/> | Este código não produz o comportamento desejado. |

Na maioria das situações, o orientaremos para a versão correta de qualquer código que não compile.

## Código Fonte

Os arquivos de origem dos quais este livro é gerado podem ser encontrados em
[GitHub][book].

[book]: https://github.com/rust-lang/book/tree/main/src
