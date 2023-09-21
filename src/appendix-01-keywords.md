## Apêndice A: Palavras-chave

A lista a seguir contém palavras-chave que são reservadas para uso atual ou futuro pela linguagem Rust. Como tal, elas não podem ser usadas como identificadores (exceto como identificadores brutos, como discutiremos na seção "[Identificadores brutos][raw-identifiers]<!-- ignore -->"). Identificadores são nomes de funções, variáveis, parâmetros, campos de estrutura, módulos, crates, constantes, macros, valores estáticos, atributos, tipos, traits ou lifetimes.

[raw-identifiers]: #raw-identifiers

### Palavras-chave Atualmente em Uso

A seguir está uma lista de palavras-chave atualmente em uso, com suas funcionalidades descritas.

* `as` - realizar conversão primitiva, desambiguar o trait específico que contém um item, ou renomear itens em declarações `use`
* `async` - retornar um `Future` em vez de bloquear a thread atual
* `await` - suspender a execução até que o resultado de um `Future` esteja pronto
* `break` - sair imediatamente de um loop
* `const` - definir itens constantes ou ponteiros crus constantes
* `continue` - continuar para a próxima iteração do loop
* `crate` - em um caminho de módulo, refere-se à raiz da crate
* `dyn` - despacho dinâmico para um objeto trait
* `else` - fallback para construções de fluxo de controle `if` e `if let`
* `enum` - definir uma enumeração
* `extern` - linkar uma função ou variável externa
* `false` - literal booleano `false`
* `fn` - definir uma função ou o tipo de ponteiro de função
* `for` - iterar sobre itens de um iterador, implementar um trait ou especificar um tempo de vida com ranking superior
* `if` - ramificar com base no resultado de uma expressão condicional
* `impl` - implementar funcionalidade inerente ou de trait
* `in` - parte da sintaxe do loop `for`
* `let` - vincular uma variável
* `loop` - criar um loop incondicional
* `match` - corresponder um valor a padrões
* `mod` - definir um módulo
* `move` - fazer com que um fechamento assuma a propriedade de todas as suas capturas
* `mut` - denotar mutabilidade em referências, ponteiros crus ou vinculações de padrão
* `pub` - denotar visibilidade pública em campos de estrutura, blocos `impl` ou módulos
* `ref` - vincular por referência
* `return` - retornar de uma função
* `Self` - um alias de tipo para o tipo que estamos definindo ou implementando
* `self` - sujeito do método ou módulo atual
* `static` - variável global ou tempo de vida que dura a execução do programa inteiro
* `struct` - definir uma estrutura
* `super` - módulo pai do módulo atual
* `trait` - definir um trait
* `true` - literal booleano `true`
* `type` - definir um alias de tipo ou tipo associado
* `union` - definir uma [união][union]<!-- ignore -->; só é uma palavra-chave quando usada em uma declaração de união
* `unsafe` - denotar código, funções, traits ou implementações inseguras
* `use` - trazer símbolos para o escopo
* `where` - denotar cláusulas que restringem um tipo
* `while` - criar um loop condicional com base no resultado de uma expressão

[union]: ../reference/items/unions.html

### Palavras-chave Reservadas para Uso Futuro

As seguintes palavras-chave ainda não têm nenhuma funcionalidade, mas são reservadas pelo Rust para uso futuro potencial.

* `abstract`
* `become`
* `box`
* `do`
* `final`
* `macro`
* `override`
* `priv`
* `try`
* `typeof`
* `unsized`
* `virtual`
* `yield`

### Identificadores Raw

*Identificadores raw* são a sintaxe que permite que você use palavras-chave onde normalmente não seriam permitidas. Você usa um identificador raw prefixando uma palavra-chave com `r#`.

Por exemplo, `match` é uma palavra-chave. Se você tentar compilar a seguinte função que usa `match` como seu nome:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
fn match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}
```

você receberá este erro:

```text
error: expected identifier, found keyword `match`
 --> src/main.rs:4:4
  |
4 | fn match(needle: &str, haystack: &str) -> bool {
  |    ^^^^^ expected identifier, found keyword
```

O erro mostra que você não pode usar a palavra-chave `match` como identificador da função. Para usar `match` como nome de função, você precisa usar a sintaxe de identificador raw, como neste exemplo:

<span class="filename">Filename: src/main.rs</span>

```rust
fn r#match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}

fn main() {
    assert!(r#match("foo", "foobar"));
}
```

Este código será compilado sem erros. Observe o prefixo `r#` no nome da função em sua definição, bem como onde a função é chamada em `main`.

Identificadores raw permitem que você use qualquer palavra que escolher como identificador, mesmo que essa palavra seja uma palavra-chave reservada. Isso nos dá mais liberdade para escolher nomes de identificadores e também nos permite integrar com programas escritos em uma linguagem onde essas palavras não são palavras-chave. Além disso, identificadores raw permitem que você use bibliotecas escritas em uma edição diferente do Rust do que a sua crate usa. Por exemplo, `try` não é uma palavra-chave na edição de 2015, mas é na edição de 2018. Se você depender de uma biblioteca escrita usando a edição de 2015 e ela tiver uma função `try`, você precisará usar a sintaxe de identificador raw, `r#try` neste caso, para chamar essa função do seu código da edição de 2018. Consulte [Apêndice E][apendice-e] para obter mais informações sobre edições.

[appendix-e]: appendix-05-editions.html
