## Comentários

Todos os programadores se esforçam para tornar seu código fácil de entender, mas às vezes uma explicação adicional é necessária. Nestes casos, os programadores deixam *comentários* em seu código-fonte que o compilador ignorará, mas as pessoas que estão lendo o código-fonte podem achar úteis.

Aqui está um comentário simples:

```rust
// hello, world
```

No Rust, o estilo de comentário idiomático inicia um comentário com duas barras e o comentário continua até o final da linha. Para comentários que se estendem por várias linhas, você precisará incluir `//` em cada linha, como este:

```rust
// So we’re doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what’s going on.
```

Comentários também podem ser colocados no final de linhas que contêm código:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-24-comments-end-of-line/src/main.rs}}
```

Mas você os verá com mais frequência usados neste formato, com o comentário em uma linha separada acima do código que está anotando:

<span class="filename">Filename: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-25-comments-above-line/src/main.rs}}
```

O Rust também possui outro tipo de comentário, chamado de comentários de documentação, que discutiremos na seção ["Publicando uma Caixa no Crates.io"][publishing]<!-- ignore --> do Capítulo 14.

[publishing]: ch14-02-publishing-to-crates-io.html
