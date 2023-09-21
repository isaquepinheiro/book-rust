## Apêndice B: Operadores e Símbolos

Este apêndice contém um glossário da sintaxe do Rust, incluindo operadores e outros símbolos que aparecem por si mesmos ou no contexto de caminhos, genéricos, limites de trait, macros, atributos, comentários, tuplas e colchetes.

### Operadores

A Tabela B-1 contém os operadores em Rust, um exemplo de como o operador apareceria no contexto, uma breve explicação e se esse operador é sobrecarregável. Se um operador for sobrecarregável, o traço relevante a ser usado para sobrecarregar esse operador é listado.

<span class="caption">Tabela B-1: Operadores</span>

| Operador | Exemplo | Explicação  | Sobrecarregável? |
|----------|---------|-------------|------------------|
| `!` | `ident!(...)`, `ident!{...}`, `ident![...]` | Expansão de macro | |
| `!` | `!expr` | Complemento lógico ou de bits | `Not` |
| `!=` | `expr != expr` | Comparação de não igualdade | `PartialEq` |
| `%` | `expr % expr` | Resto aritmético | `Rem` |
| `%=` | `var %= expr` | Resto aritmético e atribuição | `RemAssign` |
| `&` | `&expr`, `&mut expr` | Empréstimo | |
| `&` | `&type`, `&mut type`, `&'a type`, `&'a mut type` | Tipo de ponteiro emprestado | |
| `&` | `expr & expr` | E bit a bit E | `BitAnd` |
| `&=` | `var &= expr` | E bit a bit E e atribuição | `BitAndAssign` |
| `&&` | `expr && expr` | E lógico de curto-circuito | |
| `*` | `expr * expr` | Multiplicação aritmética | `Mul` |
| `*=` | `var *= expr` | Multiplicação aritmética e atribuição | `MulAssign` |
| `*` | `*expr` | Desreferência | `Deref` |
| `*` | `*const type`, `*mut type` | Ponteiro bruto | |
| `+` | `trait + trait`, `'a + trait` | Restrição de tipo composto | |
| `+` | `expr + expr` | Adição aritmética | `Add` |
| `+=` | `var += expr` | Adição aritmética e atribuição | `AddAssign` |
| `,` | `expr, expr` | Separador de argumento e elemento | |
| `-` | `- expr` | Negação aritmética | `Neg` |
| `-` | `expr - expr` | Subtração aritmética | `Sub` |
| `-=` | `var -= expr` | Subtração aritmética e atribuição | `SubAssign` |
| `->` | `fn(...) -> type`, <code>&vert;...&vert; -> type</code> | Tipo de retorno de função e fechamento | |
| `.` | `expr.ident` | Acesso a membro | |
| `..` | `..`, `expr..`, `..expr`, `expr..expr` | Literal de intervalo direito-exclusivo | `PartialOrd` |
| `..=` | `..=expr`, `expr..=expr` | Literal de intervalo direito-inclusivo | `PartialOrd` |
| `..` | `..expr` | Sintaxe de atualização de struct literal | |
| `..` | `variant(x, ..)`, `struct_type { x, .. }` | Ligação de padrão "e o resto" | |
| `...` | `expr...expr` | (Depreciado, use `..=`) Em um padrão: padrão de intervalo inclusivo | |
| `/` | `expr / expr` | Divisão aritmética | `Div` |
| `/=` | `var /= expr` | Divisão aritmética e atribuição | `DivAssign` |
| `:` | `pat: type`, `ident: type` | Restrições | |
| `:` | `ident: expr` | Inicializador de campo de struct | |
| `:` | `'a: loop {...}` | Rótulo de loop | |
| `;` | `expr;` | Terminador de instrução e item | |
| `;` | `[...; len]` | Parte da sintaxe de array de tamanho fixo | |
| `<<` | `expr << expr` | Deslocamento à esquerda | `Shl` |
| `<<=` | `var <<= expr` | Deslocamento à esquerda e atribuição | `ShlAssign` |
| `<` | `expr < expr` | Comparação menor que | `PartialOrd` |
| `<=` | `expr <= expr` | Comparação menor ou igual | `PartialOrd` |
| `=` | `var = expr`, `ident = type` | Atribuição/equivalência | |
| `==` | `expr == expr` | Comparação de igualdade | `PartialEq` |
| `=>` | `pat => expr` | Parte da sintaxe do braço `match` | |
| `>` | `expr > expr` |

### Símbolos não-operadores

A lista a seguir contém todos os símbolos que não funcionam como operadores; isto é, eles não se comportam como uma função ou chamada de método.

A Tabela B-2 mostra símbolos que aparecem por si só e são válidos em várias localizações.

<span class="caption">Tabela B-2: Sintaxe Autônoma</span>

| Símbolo |  Explicação |
|---------|-------------|
| `'ident` | Vida nomeada ou rótulo de loop |
| `...u8`, `...i32`, `...f64`, `...usize`, etc. | Literal numérico de tipo específico |
| `"..."` | String literal |
| `r"..."`, `r#"..."#`, `r##"..."##`, etc. | Literal de string bruta, caracteres de escape não processados |
| `b"..."` | Literal de string de bytes; constrói um array de bytes em vez de uma string |
| `br"..."`, `br#"..."#`, `br##"..."##`, etc. | Literal de string de bytes crua; combinação de um literal de string de bytes e crua |
| `'...'` | Literal de caractere |
| `b'...'` | Literal de byte ASCII |
| <code>&vert;...&vert; expr</code> | Fechamento |
| `!` | Sempre vazia, tipo inferior para funções divergentes |
| `_` | "Padrão" de ligação ignorado; também usado para tornar literais de inteiros legíveis |

A Tabela B-3 mostra símbolos que aparecem no contexto de um caminho através da hierarquia de módulos para um item.

<span class="caption">Tabela B-3: Sintaxe Relacionada a Caminhos</span>

| Símbolo | Explicação  |
|---------|-------------|
| `ident::ident` | Caminho de namespace |
| `::path` | Caminho relativo à raiz da crate (ou seja, um caminho explicitamente absoluto) |
| `self::path` | Caminho relativo ao módulo atual (ou seja, um caminho explicitamente relativo).
| `super::path` | Caminho relativo ao pai do módulo atual |
| `type::ident`, `<type as trait>::ident` | Constantes, funções e tipos associados |
| `<type>::...` | Item associado a um tipo que não pode ser nomeado diretamente (e.g., `<&T>::...`, `<[T]>::...`, etc.) |
| `trait::method(...)` | Resolvendo a ambiguidade uma chamada de método ao nomear o traço que a define |
| `type::method(...)` | Resolvendo a ambiguidade uma chamada de método ao nomear o tipo para o qual ela está definida. |
| `<type as trait>::method(...)` | Resolvendo a ambiguidade em uma chamada de método ao especificar o nome do traço e o tipo. |

Tabela B-4 mostra símbolos que aparecem no contexto do uso de parâmetros de tipo genérico.

<span class="caption">Tabela B-4: Genéricos</span>

| Símbolo | Explicação  |
|---------|-------------|
| `path<...>` | Especifica parâmetros para um tipo genérico em um tipo (e.g., `Vec<u8>`) |
| `path::<...>`, `method::<...>` | Especifica parâmetros para um tipo genérico, função ou método em uma expressão; frequentemente chamado de turbofish (e.g., `"42".parse::<i32>()`) |
| `fn ident<...> ...` | Define generic function |
| `struct ident<...> ...` | Define uma estrutura genérica |
| `enum ident<...> ...` | Define uma enumeração genérica |
| `impl<...> ...` | Define uma implementação genérica |
| `for<...> type` | Limites de tempo de vida de ordem superior |
| `type<ident=type>` | Um tipo genérico no qual um ou mais tipos associados têm atribuições específicas. (e.g., `Iterator<Item=T>`) |

A Tabela B-5 mostra símbolos que aparecem no contexto de restringir os parâmetros de tipo genérico com limites de traço.

<span class="caption">Tabela B-5: Restrições de Limites de Traço</span>

| Símbolo| Explicação  |
|--------|-------------|
| `T: U` | Parâmetro genérico `T` limitado a tipos que implementam `U` |
| `T: 'a` | Tipo genérico `T` deve sobreviver ao tempo de vida `'a` (o que significa que o tipo não pode conter transitivamente referências com tempos de vida mais curtos que `'a`) |
| `T: 'static` | O tipo genérico `T` não contém referências emprestadas, exceto aquelas com tempo de vida `'static`. |
| `'b: 'a` | A vida genérica `'b` deve ser mais longa do que a vida (lifetime). `'a` |
| `T: ?Sized` | Permitir que o parâmetro de tipo genérico seja um tipo de tamanho dinâmico. |
| `'a + trait`, `trait + trait` | Restrição de tipo composto. |

A Tabela B-6 mostra símbolos que aparecem no contexto de chamar ou definir macros e especificar atributos em um item.

<span class="caption">Tabela B-6: Macros e Atributos</span>

| Símbolo | Explicação  |
|-------- |-------------|
| `#[meta]` | Atributo Externo|
| `#![meta]` | Atributo Interno |
| `$ident` | Substituição de Macro |
| `$ident:kind` | Captura de Macro |
| `$(…)…` | Repetição de Macro |
| `ident!(...)`, `ident!{...}`, `ident![...]` | Invocação de Macro |

Tabela B-7: Símbolos que criam comentários

<span class="caption">Tabela B-7: Comentários</span>

| Símbolo | Explicação |
|---------|------------|
| `//` | Comentário de Linha |
| `//!` | Comentário de Documentação Interno de Linha |
| `///` | Comentário de Documentação Externo de Linha |
| `/*...*/` | Comentário de Bloco |
| `/*!...*/` | Comentário de Documentação Interno de Bloco |
| `/**...*/` | Comentário de Documentação Externo de Bloco |

Tabela B-8 mostra símbolos que aparecem no contexto do uso de tuplas.

<span class="caption">Tabela B-8: Tuplas</span>

| Símbolo | Explicação |
|---------|------------|
| `()` | Tupla vazia (também conhecida como unidade), tanto como literal quanto como tipo |
| `(expr)` | Expressão com parênteses |
| `(expr,)` | Expressão de tupla de um único elemento |
| `(type,)` | Tipo de tupla de um único elemento |
| `(expr, ...)` | Expressão de tupla |
| `(type, ...)` | Tipo de tupla |
| `expr(expr, ...)` | Expressão de chamada de função; também usada para inicializar tuplas em `struct`s de tupla e variantes de `enum` de tupla |
| `expr.0`, `expr.1`, etc. | Indexação de tupla |

Tabela B-9 mostra os contextos nos quais as chaves são usadas.

<span class="caption">Table B-9: Curly Brackets</span>

| Contexto | Explicação |
|----------|------------|
| `{...}` | Expressão de bloco|
| `Type {...}` | `struct` Literal |

Table B-10 shows the contexts in which square brackets are used.

<span class="caption">Table B-10: Square Brackets</span>

| Contexto | Explicação |
|----------|------------|
| `[...]` | Literal de array |
| `[expr; len]` | Literal de array contendo `len` cópias de `expr` |
| `[type; len]` | Tipo de array contendo `len` instâncias de `type` |
| `expr[expr]` | Indexação de coleção. Pode ser sobrecarregado (`Index`, `IndexMut`) |
| `expr[..]`, `expr[a..]`, `expr[..b]`, `expr[a..b]` | Indexação de coleção simulando fatiamento de coleção, usando `Range`, `RangeFrom`, `RangeTo` ou `RangeFull` como o "índice" |