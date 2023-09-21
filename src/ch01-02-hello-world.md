## Olá, Mundo!

Agora que você instalou o Rust, é hora de escrever seu primeiro programa Rust. É tradicional, ao aprender uma nova linguagem, escrever um pequeno programa que imprime o texto `Olá, mundo!` na tela, então faremos o mesmo aqui!

> Nota: Este livro pressupõe uma familiaridade básica com a linha de comando. O Rust não faz demandas específicas sobre a edição ou ferramentas ou onde seu código deve estar, então se preferir usar um ambiente de desenvolvimento integrado (IDE) em vez da linha de comando, sinta-se à vontade para usar seu IDE favorito. Muitos IDEs agora têm algum grau de suporte ao Rust; verifique a documentação do seu IDE para obter detalhes. A equipe do Rust tem se concentrado em oferecer um ótimo suporte ao IDE por meio do `rust-analyzer`. Veja [Apêndice D][devtools] para mais detalhes.

### Criando um Diretório de Projeto

Você começará criando um diretório para armazenar seu código Rust. Para o Rust, não importa onde seu código está, mas para os exercícios e projetos deste livro, sugerimos criar um diretório *projetos* em seu diretório pessoal e manter todos os seus projetos lá.

Abra um terminal e digite os seguintes comandos para criar um diretório *projetos* e um diretório para o projeto "Olá, mundo!" dentro do diretório *projetos*.

Para Linux, macOS e PowerShell no Windows, digite o seguinte:

```console
$ mkdir ~/projects
$ cd ~/projects
$ mkdir hello_world
$ cd hello_world
```

Para o Windows CMD, digite o seguinte:

```cmd
> mkdir "%USERPROFILE%\projects"
> cd /d "%USERPROFILE%\projects"
> mkdir hello_world
> cd hello_world
```

A seguir, crie um novo arquivo de origem e chame-o de *main.rs*. Os arquivos Rust sempre terminam com a extensão *.rs*. Se você estiver usando mais de uma palavra em seu nome de arquivo, a convenção é usar um sublinhado para separá-las. Por exemplo, use *hello_world.rs* em vez de *helloworld.rs*.

Agora abra o arquivo *main.rs* que você acabou de criar e insira o código do Exemplo 1-1.

<span class="filename">Filename: main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

<span class="caption">Exemplo 1-1: Um programa que imprime `Hello, world!`</span>

Salve o arquivo e volte para a janela do seu terminal no diretório *~/projetos/ola_mundo*. No Linux ou macOS, digite os seguintes comandos para compilar e executar o arquivo:

```console
$ rustc main.rs
$ ./main
Hello, world!
```

No Windows, digite o comando `.\main.exe` em vez de `./main`:

```powershell
> rustc main.rs
> .\main.exe
Hello, world!
```

Independentemente do seu sistema operacional, a string `Hello, world!` deve ser impressa no terminal. Se você não vir essa saída, consulte a seção de [“Solução de Problemas”][troubleshooting] da parte de Instalação para obter ajuda.

Se `Hello, world!` foi impresso, parabéns! Você oficialmente escreveu um programa em Rust. Isso faz de você um programador em Rust - bem-vindo!

Vamos analisar este programa `Hello, world!` em detalhes. Aqui está a primeira parte do quebra-cabeça:

```rust
fn main() {

}
```

Essas linhas definem uma função chamada `main`. A função `main` é especial: é sempre o primeiro código que é executado em todos os programas executáveis em Rust. Aqui, a primeira linha declara uma função chamada `main` que não possui parâmetros e não retorna nada. Se houvesse parâmetros, eles seriam colocados dentro dos parênteses `()`.

O corpo da função é envolto por `{}`. O Rust requer chaves em torno de todos os corpos de função. É uma boa prática colocar a chave de abertura na mesma linha da declaração da função, adicionando um espaço entre eles.

> Nota: Se você deseja seguir um estilo padrão em seus projetos em Rust, pode usar uma ferramenta de formatação automática chamada `rustfmt` para formatar seu código em um estilo específico (mais sobre `rustfmt` em [Apêndice D][devtools]). A equipe do Rust incluiu essa ferramenta na distribuição padrão do Rust, assim como o `rustc`, portanto, ela já deve estar instalada no seu computador!

O corpo da função `main` contém o seguinte código:

```rust
    println!("Hello, world!");
```

Esta linha faz todo o trabalho neste pequeno programa: ela imprime texto na tela. Existem quatro detalhes importantes a serem observados aqui.

Primeiro, o estilo do Rust é indentar com quatro espaços, não com uma tabulação.

Segundo, `println!` chama uma macro em Rust. Se tivesse chamado uma função em vez disso, seria inserido como `println` (sem o `!`). Discutiremos macros em Rust com mais detalhes no Capítulo 19. Por enquanto, você só precisa saber que usar `!` significa que você está chamando uma macro em vez de uma função normal e que as macros nem sempre seguem as mesmas regras que as funções.

Terceiro, você vê a string `"Hello, world!"`. Passamos essa string como argumento para `println!`, e a string é impressa na tela.

Quarto, terminamos a linha com um ponto e vírgula (`;`), o que indica que esta expressão terminou e a próxima está pronta para começar. A maioria das linhas de código Rust termina com um ponto e vírgula.

### Compilar e Executar São Etapas Separadas

Você acabou de executar um programa recém-criado, então vamos examinar cada etapa do processo.

Antes de executar um programa Rust, você deve compilá-lo usando o compilador Rust, inserindo o comando `rustc` e passando a ele o nome do seu arquivo de origem, como este:

```console
$ rustc main.rs
```

Se você tem experiência em C ou C++, notará que isso é semelhante ao `gcc` ou `clang`. Após a compilação bem-sucedida, o Rust gera um executável binário.

No Linux, macOS e no PowerShell no Windows, você pode ver o executável digitando o comando `ls` no seu shell:

```console
$ ls
main  main.rs
```

No Linux e macOS, você verá dois arquivos. No PowerShell do Windows, você verá os mesmos três arquivos que veria usando o CMD. No CMD do Windows, você digitará o seguinte:

```cmd
> dir /B %= the /B option says to only show the file names =%
main.exe
main.pdb
main.rs
```

Isso mostra o arquivo de código-fonte com a extensão *.rs*, o arquivo executável (*main.exe* no Windows, mas *main* em todas as outras plataformas) e, ao usar o Windows, um arquivo contendo informações de depuração com a extensão *.pdb*. A partir daqui, você executa o arquivo *main* ou *main.exe*, assim:

```console
$ ./main # or .\main.exe on Windows
```

Se o seu *main.rs* é o seu programa "Hello, world!", esta linha imprime `Hello, world!` no seu terminal.

Se você estiver mais familiarizado com uma linguagem dinâmica, como Ruby, Python ou JavaScript, pode não estar acostumado a compilar e executar um programa como etapas separadas. O Rust é uma linguagem compilada *ahead-of-time*, o que significa que você pode compilar um programa e dar o executável para outra pessoa, e ela poderá executá-lo mesmo sem ter o Rust instalado. Se você der a alguém um arquivo *.rb*, *.py* ou *.js*, eles precisarão ter uma implementação do Ruby, Python ou JavaScript instalada (respectivamente). Mas nessas linguagens, você só precisa de um comando para compilar e executar seu programa. Tudo é um compromisso no design da linguagem.

Apenas compilar com `rustc` é bom para programas simples, mas à medida que seu projeto cresce, você vai querer gerenciar todas as opções e facilitar o compartilhamento do seu código. Em seguida, apresentaremos a você a ferramenta Cargo, que o ajudará a escrever programas Rust do mundo real.

[troubleshooting]: ch01-01-installation.html#troubleshooting
[devtools]: appendix-04-useful-development-tools.md
