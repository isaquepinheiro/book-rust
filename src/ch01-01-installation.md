## Instalação

O primeiro passo é instalar o Rust. Vamos baixar o Rust através do `rustup`, uma ferramenta de linha de comando para gerenciar versões do Rust e ferramentas associadas. Você precisará de uma conexão com a internet para o download.

> Observação: Se você preferir não usar o rustup por algum motivo, consulte a página
> [Other Rust Installation Methods page][otherinstall] para mais opções..

Os seguintes passos instalam a versão estável mais recente do compilador Rust. As garantias de estabilidade do Rust garantem que todos os exemplos no livro que são compiláveis continuarão a ser compiláveis com versões mais recentes do Rust. A saída pode variar um pouco entre as versões, porque o Rust frequentemente melhora as mensagens de erro e avisos. Em outras palavras, qualquer versão estável mais recente do Rust que você instalar usando esses passos deve funcionar conforme o esperado com o conteúdo deste livro.

> ### Notação da Linha de Comando
>
> Neste capítulo e ao longo do livro, mostraremos alguns comandos usados no terminal. As linhas que você deve inserir em um terminal começam com `$`. Você não precisa digitar o caractere `$`; ele é o prompt da linha de comando mostrado para indicar o início de cada comando. As linhas que não começam com `$` normalmente mostram a saída do comando anterior. Além disso, exemplos específicos do PowerShell usarão `>` em vez de `$`.

### Instalando o `rustup` no Linux ou macOS

Se você estiver usando Linux ou macOS, abra um terminal e insira o seguinte comando:

```console
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

### Instalando o `rustup` no Linux ou macOS

Se você estiver usando Linux ou macOS, abra um terminal e insira o seguinte comando:

```text
Rust is installed now. Great!
```

Você também precisará de um *linker*, que é um programa que o Rust usa para unir suas saídas compiladas em um arquivo único. É provável que você já tenha um. Se você receber erros de linker, deverá instalar um compilador C, que normalmente incluirá um linker. Um compilador C também é útil porque alguns pacotes Rust comuns dependem de código C e precisarão de um compilador C.

No macOS, você pode obter um compilador C executando:

```console
$ xcode-select --install
```

Usuários do Linux geralmente devem instalar o GCC ou o Clang, de acordo com a documentação de sua distribuição. Por exemplo, se você usa o Ubuntu, pode instalar o pacote `build-essential`.

### Instalando o `rustup` no Windows

No Windows, vá para [https://www.rust-lang.org/tools/install][install] e siga as instruções para instalar o Rust. Em algum momento da instalação, você receberá uma mensagem explicando que também precisará das ferramentas de compilação MSVC para o Visual Studio 2013 ou posterior.

Para adquirir as ferramentas de compilação, você precisará instalar o [Visual Studio 2022][visualstudio]. Quando perguntado quais workloads instalar, inclua:

* "Desenvolvimento para Desktop com C++"
* O SDK do Windows 10 ou 11
* O componente de pacote de idioma em inglês, juntamente com qualquer outro pacote de idioma de sua escolha

O restante deste livro utiliza comandos que funcionam tanto no *cmd.exe* quanto no PowerShell. Se houver diferenças específicas, explicaremos qual usar.

### Solução de Problemas

Para verificar se você instalou o Rust corretamente, abra um shell e insira esta linha:

```console
$ rustc --version
```

Você deverá ver o número da versão, o hash do commit e a data do commit para a versão estável mais recente que foi lançada, no seguinte formato:

```text
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

Se você ver essas informações, instalou o Rust com sucesso! Se não ver essas informações, verifique se o Rust está na variável de sistema `%PATH%` da seguinte maneira.

No Windows CMD, use:

```console
> echo %PATH%
```

No PowerShell, use:

```powershell
> echo $env:Path
```

No Linux e macOS, use:

```console
$ echo $PATH
```

Se tudo estiver correto e o Rust ainda não estiver funcionando, existem várias maneiras de obter ajuda. Descubra como entrar em contato com outros Rustaceans (um apelido engraçado que usamos para nos referir a nós mesmos) na [página da comunidade][community].

### Atualização e Desinstalação

Depois que o Rust for instalado via `rustup`, atualizar para uma versão recém-lançada é fácil. Do seu shell, execute o seguinte script de atualização:

```console
$ rustup update
```

Para desinstalar o Rust e o `rustup`, execute o seguinte script de desinstalação a partir do seu shell:

```console
$ rustup self uninstall
```

### Documentação Local

A instalação do Rust também inclui uma cópia local da documentação para que você possa lê-la offline. Execute `rustup doc` para abrir a documentação local em seu navegador.

Sempre que um tipo ou função é fornecido pela biblioteca padrão e você não tem certeza do que ele faz ou como usá-lo, utilize a documentação da interface de programação de aplicativos (API) para descobrir!

[otherinstall]: https://forge.rust-lang.org/infra/other-installation-methods.html
[install]: https://www.rust-lang.org/tools/install
[visualstudio]: https://visualstudio.microsoft.com/downloads/
[community]: https://www.rust-lang.org/community
