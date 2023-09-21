# A Linguagem de Programação Rust

![Build Status](https://github.com/rust-lang/book/workflows/CI/badge.svg)

Este repositório contém o código-fonte do livro "A Linguagem de Programação Rust".

[O livro está disponível em formato físico pela editora No Starch Press.][nostarch].

[nostarch]: https://nostarch.com/rust-programming-language-2nd-edition

Você também pode ler o livro gratuitamente online. Consulte o livro conforme fornecido com as versões mais recentes do Rust [stable], [beta] ou [nightly]. Esteja ciente de que problemas nessas versões podem já ter sido corrigidos neste repositório, já que essas versões são atualizadas com menos frequência.

[stable]: https://doc.rust-lang.org/stable/book/
[beta]: https://doc.rust-lang.org/beta/book/
[nightly]: https://doc.rust-lang.org/nightly/book/

Consulte as [versões] para baixar apenas o código de todos os exemplos de código que aparecem no livro.

[releases]: https://github.com/rust-lang/book/releases

## Requisitos

Para construir o livro, você precisa do [mdBook], idealmente da mesma versão usada pelo rust-lang/rust neste [arquivo][rust-mdbook]. Para obtê-lo:

[mdBook]: https://github.com/rust-lang-nursery/mdBook
[rust-mdbook]: https://github.com/rust-lang/rust/blob/master/src/tools/rustbook/Cargo.toml

```bash
$ cargo install mdbook --version <version_num>
```

## Construindo

Para construir o livro, digite:

```bash
$ mdbook build
```

A saída estará no subdiretório `book`. Para ver o livro, abra-o em seu navegador da web.

_Firefox:_
```bash
$ firefox book/index.html                       # Linux
$ open -a "Firefox" book/index.html             # OS X
$ Start-Process "firefox.exe" .\book\index.html # Windows (PowerShell)
$ start firefox.exe .\book\index.html           # Windows (Cmd)
```

_Chrome:_
```bash
$ google-chrome book/index.html                 # Linux
$ open -a "Google Chrome" book/index.html       # OS X
$ Start-Process "chrome.exe" .\book\index.html  # Windows (PowerShell)
$ start chrome.exe .\book\index.html            # Windows (Cmd)
```

Para executar os testes:

```bash
$ mdbook test
```

## Contribuindo

Adoraríamos sua ajuda! Consulte [CONTRIBUTING.md][contrib] para saber mais sobre os tipos de contribuições que estamos procurando.

[contrib]: https://github.com/rust-lang/book/blob/main/CONTRIBUTING.md

Como o livro é [impresso][nostarch] e porque queremos manter a versão online do livro o mais próxima possível da versão impressa, pode levar mais tempo do que você está acostumado para abordarmos sua solicitação de problema ou pull request.

Até agora, temos feito uma revisão maior para coincidir com as [Edições do Rust](https://doc.rust-lang.org/edition-guide/). Entre essas revisões maiores, apenas corrigiremos erros. Se sua solicitação de problema ou pull request não for estritamente para corrigir um erro, ela pode permanecer pendente até a próxima vez que estivermos trabalhando em uma revisão maior: espere em uma escala de meses ou anos. Agradecemos pela sua paciência!

### Traduções

Adoraríamos receber ajuda na tradução do livro! Consulte a etiqueta [Translations] para participar dos esforços que estão em andamento. Abra um novo problema para começar a trabalhar em um novo idioma! Estamos aguardando o [suporte do mdbook] para vários idiomas antes de mesclar qualquer tradução, mas sinta-se à vontade para começar!

[Translations]: https://github.com/rust-lang/book/issues?q=is%3Aopen+is%3Aissue+label%3ATranslations
[mdbook support]: https://github.com/rust-lang-nursery/mdBook/issues/5

## Verificação Ortográfica

Para verificar arquivos de origem em busca de erros de ortografia, você pode usar o script `spellcheck.sh` disponível no diretório `ci`. Ele precisa de um dicionário de palavras válidas, que é fornecido em `ci/dictionary.txt`. Se o script produzir um falso positivo (por exemplo, você usou a palavra `BTreeMap`, que o script considera inválida), você precisará adicionar essa palavra a `ci/dictionary.txt` (mantenha a ordem ordenada para consistência).
