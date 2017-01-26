# Effective Go (RU) (Эффективное программирование на языке Go)

Оригинал смотри: https://golang.org/doc/effective_go.html
```
go version go1.7.4
```

## Введение


Go - это новый язык программирования. Хотя он заимствует идеи из существующих языков, он обладает необычными свойствами, которые делают эффективные программы Go отличающиеся по своему характеру от программ, написанных на его родственных. Прямолинейный перевод C++ или Java программ в Go вряд ли даст удовлетворительный результат, т.к. Java программы написаны на Java, не на Go. С другой стороны, думая о проблеме с точки зрения Go можно добиться успеха, но это уже другая программа. Другими словами, для написания на Go хорошо, важно понимать его особенности и идиомы.
Также важно знать установленные соглашения для программирования на Go, такие как именование, форматирование, разработка программ и так далее, так чтобы программы написанные Вами были простыми для понимания другими программистами Go.



Этот документ дает примеры для написания чистого, идеоматичного кода на Go.
Он дополняет [спецификацию языка](https://golang.org/ref/spec),
 [Тур по Go](https://tour.golang.org/),
и [Как написать на Go](https://golang.org/doc/code.html),
каждую из которых необходимо прочитать в первую очередь.


### Примеры


[Go пакеты исходных кодов](https://golang.org/src/) предназначены не только в качестве основных библиотек, но и в качестве примеров использования языка.
Кроме того, многие пакеты имеют работающие, автономные исполняемые примеры и Вы можете запустить напрямую с помощью [golang.org](https://golang.org) страницы, такие как [этот](https://golang.org/pkg/strings/#example_Map) ( если  необходимо, нажмите на слово "Примеры" чтобы открыть его).
Если у Вас есть вопрос о том как решить какую-либо проблему или как что-то реализовать, то документация, исходные коды и примеры в библиотеке могут дать ответ, идею или объяснение.



## Форматирование

Форматирование является наиболее спорным и менее важным.
Люди могут адаптироваться к различным стилям форматирования, но было бы лучше если бы этого не приходилось делать и меньше времени придавались этой теме, если бы все использовали одинаковый стиль.
Проблема данной утопии в том как это сделать без длинного руководства по стилю.


В Go мы используем нетипичный подход и передаем машине заботу о форматировании. ```gofmt``` программа (также доступна как ```go fmt```, который действует на уровне пакета а не на уровне файлов) читающая код на Go и выпускает исходный код в стандарном стиле отступов и вертикальном выравнивании, сохраняющая и если необходимо переформатирует комментарии.
Если Вы хотите знать - как справиться с новой ситуацией размещения кода, то запустите ```gofmt```; если ответ не будет правильным, то ```gofmt``` поправить Вашу программу (или файл сообщит об ошибки ```gofmt```), не работайте в обход форматирования программой ```gofmt```.


К примеру, нет необходимости тратить время на выравнивание комментариев в структуре кода. ```Gofmt``` сделает это за Вас. Принимая во внимание принятые соглашения.


```golang
type T struct {
    name string // name of the object
    value int // its value
}
```


_ ```gofmt``` будет выравнивать по колонкам:


```golang
type T struct {
    name    string // name of the object
    value   int    // its value
}
```


Все стандартные пакеты Go отформатированны с помощью ```gofmt```.




Некоторые детали форматирования. Очень кратко:


<dl>
    <dt>Абзац</dt>
    <dd>Мы используем табуляцию для абзацев и ```gofmt``` делает это по умолчанию. Используйте пробелы только если вы должны.
    </dd>
    <dt>Длина строки</dt>
    <dd>
    Go не имеет предела длинны строки. Не беспокойтесь о длинных строках. Если длина строки слишком длинная, то переведите на другуб строку и сделайте с дополнительной табуляцией.
    </dd>
    <dt>Круглые скобки</dt>
    <dd>
    Go нуждается в меньшем количестве круглых скобок чем C и Java: структуры ветвления, цикла (```if```, ```for``` , ```switch```) не имеют круглых скобок в своем синтаксисе.
    Также, иерархия операторов стала проще и короче. К примеру,
```
x<<8 + y<<16
```
    добавлением пробелов, в отличии от других языков.
    </dd>
</dl>

<h2 id="commentary">Комментарии</h2>


Go использует C-стиль ```/* */``` для блока комментариев
and C++-стиль ```//``` для комментариев однострочных.
Типично используется однострочные комментарии; Блок комментариев в основном используется для комментировании пакетов, но также для выразительности или отключения большого участка кода.



Программа -и веб-сервер-```godoc``` обрабатывает Go исходники для формирования документации о содержимом пакета.
Комментарии расположенные до верхней объявлением(диклорацией) без добавления пустых строк извлекаются вместе с диклорацией для пояснения данного элемента.
Характер и стиль комментариев напрямую влияет на качество документации производимой ```godoc```.



Каждый пакет должен иметь *комментарии пакета*, это блок комментариев предшествующий диклорации пакета.
Для пакетов состоящий из нескольких файлов, комментарии пакета должны быть расположены в одном файле или в любой из них.
Комментарии пакета должен представлять информацию о пакете целиком.
Он сначало появляется на странице ```godoc``` и должна представлять из себя детальную информацию, которой можно следовать.


```golang
/*
Package regexp implements a simple library for regular expressions.

The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term [ '*' | '+' | '?' ]
    term:
        '^'
        '$'
        '.'
        character
        '[' [ '^' ] character-ranges ']'
        '(' regexp ')'
*/
package regexp
```


Если пакет простой, то комментарий пакет может быть кратким.


```golang
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```


Не требуется дополнительное формативание, к примеру баннер из * (звездочек).
Шрифт для сформированного результата не обязательно будет миниширинный, поэтому не стои использовать выравнивание пробелами ```godoc```, также как ```gofmt```, заботиться об этом.
Комментарии это интерпретируется как простой текст, поэтому HTML и другие аннотации такие как ```_this_```поспроизводяться *дословно* и поэтому не должно использоваться.
Одна установка ```godoc``` действительно делает отображение текста как моноширинного, подходит для вставки короткого кода.
*Комментарий пакета* для [```fmt```](https://golang.org/pkg/fmt/) пакета использует это для лучшего эффекта.

В зависимости от контекста, ```godoc``` не может переформатировать комментарии, поэтому убедитесь что это выглялит хорошо:
используйте правильное правописание, знаки препинятия и структуру предложения,
use correct spelling, punctuation, and sentence structure, и так далее.

Любые комментарии внутри пакета установленые до диклорации служат как *doc comment* для диклорации.
Каждое экспортируемое(начанающаяся с большой первой буквой) должна иметь *doc comment*.

*Doc comments* работают хорошо как предложения, которые позволяют широкий спектр автоматических репрезентаций.
Первое предложения должно ключевым и начинаться с объявления имени.

```golang
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error) {
```


If the name always begins the comment, the output of ```godoc```
can usefully be run through ```grep```.
Imagine you couldn't remember the name "Compile" but were looking for
the parsing function for regular expressions, so you ran
the command,


```command
$ godoc regexp | grep parse
```


If all the doc comments in the package began, "This function...", ```grep```
wouldn't help you remember the name. But because the package starts each
doc comment with the name, you'd see something like this,
which recalls the word you're looking for.


```command
$ godoc regexp | grep parse
    Compile parses a regular expression and returns, if successful, a Regexp
    parsed. It simplifies safe initialization of global variables holding
    cannot be parsed. It simplifies safe initialization of global variables
$
```


Go's declaration syntax allows grouping of declarations.
A single doc comment can introduce a group of related constants or variables.
Since the whole declaration is presented, such a comment can often be perfunctory.


```golang
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = errors.New("regexp: internal error")
    ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
    ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
    ...
)
```


Grouping can also indicate relationships between items,
such as the fact that a set of variables is protected by a mutex.


```golang
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```

<h2 id="names">Names</h2>


Names are as important in Go as in any other language.
They even have semantic effect:
the visibility of a name outside a package is determined by whether its
first character is upper case.
It's therefore worth spending a little time talking about naming conventions
in Go programs.



<h3 id="package-names">Package names</h3>


When a package is imported, the package name becomes an accessor for the
contents.  After


```golang
import "bytes"
```


the importing package can talk about ```bytes.Buffer```.  It's
helpful if everyone using the package can use the same name to refer to
its contents, which implies that the package name should be good:
short, concise, evocative.  By convention, packages are given
lower case, single-word names; there should be no need for underscores
or mixedCaps.
Err on the side of brevity, since everyone using your
package will be typing that name.
And don't worry about collisions *a priori*.
The package name is only the default name for imports; it need not be unique
across all source code, and in the rare case of a collision the
importing package can choose a different name to use locally.
In any case, confusion is rare because the file name in the import
determines just which package is being used.



Another convention is that the package name is the base name of
its source directory;
the package in ```src/encoding/base64```
is imported as ```"encoding/base64"``` but has name ```base64```,
not ```encoding_base64``` and not ```encodingBase64```.



The importer of a package will use the name to refer to its contents,
so exported names in the package can use that fact
to avoid stutter.
(Don't use the ```import .``` notation, which can simplify
tests that must run outside the package they are testing, but should otherwise be avoided.)
For instance, the buffered reader type in the ```bufio``` package is called ```Reader```,
not ```BufReader```, because users see it as ```bufio.Reader```,
which is a clear, concise name.
Moreover,
because imported entities are always addressed with their package name, ```bufio.Reader```
does not conflict with ```io.Reader```.
Similarly, the function to make new instances of ```ring.Ring```&mdash;which
is the definition of a <em>constructor</em> in Go&mdash;would
normally be called ```NewRing```, but since
```Ring``` is the only type exported by the package, and since the
package is called ```ring```, it's called just ```New```,
which clients of the package see as ```ring.New```.
Use the package structure to help you choose good names.



Another short example is ```once.Do```;
```once.Do(setup)``` reads well and would not be improved by
writing ```once.DoOrWaitUntilDone(setup)```.
Long names don't automatically make things more readable.
A helpful doc comment can often be more valuable than an extra long name.


<h3 id="Getters">Getters</h3>


Go doesn't provide automatic support for getters and setters.
There's nothing wrong with providing getters and setters yourself,
and it's often appropriate to do so, but it's neither idiomatic nor necessary
to put ```Get``` into the getter's name.  If you have a field called
```owner``` (lower case, unexported), the getter method should be
called ```Owner``` (upper case, exported), not ```GetOwner```.
The use of upper-case names for export provides the hook to discriminate
the field from the method.
A setter function, if needed, will likely be called ```SetOwner```.
Both names read well in practice:

```golang
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

<h3 id="interface-names">Interface names</h3>


By convention, one-method interfaces are named by
the method name plus an -er suffix or similar modification
to construct an agent noun: ```Reader```, ```Writer```, ```Formatter```, ```CloseNotifier``` etc.



There are a number of such names and it's productive to honor them and the function
names they capture. ```Read```, ```Write```, ```Close```, ```Flush```, ```String``` and so on have canonical signatures and meanings.  To avoid confusion,
don't give your method one of those names unless it
has the same signature and meaning.
Conversely, if your type implements a method with the
same meaning as a method on a well-known type,
give it the same name and signature;
call your string-converter method ```String``` not ```ToString```.


<h3 id="mixed-caps">MixedCaps</h3>


Finally, the convention in Go is to use ```MixedCaps```
or ```mixedCaps``` rather than underscores to write
multiword names.


<h2 id="semicolons">Semicolons</h2>


Like C, Go's formal grammar uses semicolons to terminate statements,
but unlike in C, those semicolons do not appear in the source.
Instead the lexer uses a simple rule to insert semicolons automatically
as it scans, so the input text is mostly free of them.



The rule is this. If the last token before a newline is an identifier
(which includes words like ```int``` and ```float64```),
a basic literal such as a number or string constant, or one of the
tokens

```golang
break continue fallthrough return ++ -- ) }
```

the lexer always inserts a semicolon after the token.
This could be summarized as, &ldquo;if the newline comes
after a token that could end a statement, insert a semicolon&rdquo;.



A semicolon can also be omitted immediately before a closing brace,
so a statement such as

```
    go func() { for { dst &lt;- &lt;-src } }()
```

needs no semicolons.
Idiomatic Go programs have semicolons only in places such as
```for``` loop clauses, to separate the initializer, condition, and
continuation elements.  They are also necessary to separate multiple
statements on a line, should you write code that way.



One consequence of the semicolon insertion rules
is that you cannot put the opening brace of a
control structure (```if```, ```for```, ```switch```,
or ```select```) on the next line.  If you do, a semicolon
will be inserted before the brace, which could cause unwanted
effects.  Write them like this


```
if i &lt; f() {
    g()
}
```

not like this

```
if i &lt; f()  // wrong!
{           // wrong!
    g()
}
```


<h2 id="control-structures">Control structures</h2>


The control structures of Go are related to those of C but differ
in important ways.
There is no ```do``` or ```while``` loop, only a
slightly generalized
```for```;
```switch``` is more flexible;
```if``` and ```switch``` accept an optional
initialization statement like that of ```for```;
```break``` and ```continue``` statements
take an optional label to identify what to break or continue;
and there are new control structures including a type switch and a
multiway communications multiplexer, ```select```.
The syntax is also slightly different:
there are no parentheses
and the bodies must always be brace-delimited.


<h3 id="if">If</h3>


In Go a simple ```if``` looks like this:

```
if x &gt; 0 {
    return y
}
```


Mandatory braces encourage writing simple ```if``` statements
on multiple lines.  It's good style to do so anyway,
especially when the body contains a control statement such as a
```return``` or ```break```.



Since ```if``` and ```switch``` accept an initialization
statement, it's common to see one used to set up a local variable.


```
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

<p id="else">
In the Go libraries, you'll find that
when an ```if``` statement doesn't flow into the next statement—that is,
the body ends in ```break```, ```continue```,
```goto```, or ```return```—the unnecessary
```else``` is omitted.


```
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```


This is an example of a common situation where code must guard against a
sequence of error conditions.  The code reads well if the
successful flow of control runs down the page, eliminating error cases
as they arise.  Since error cases tend to end in ```return```
statements, the resulting code needs no ```else``` statements.


```
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}
codeUsing(f, d)
```


<h3 id="redeclaration">Redeclaration and reassignment</h3>


An aside: The last example in the previous section demonstrates a detail of how the
```:=``` short declaration form works.
The declaration that calls ```os.Open``` reads,


```
f, err := os.Open(name)
```


This statement declares two variables, ```f``` and ```err```.
A few lines later, the call to ```f.Stat``` reads,


```
d, err := f.Stat()
```


which looks as if it declares ```d``` and ```err```.
Notice, though, that ```err``` appears in both statements.
This duplication is legal: ```err``` is declared by the first statement,
but only <em>re-assigned</em> in the second.
This means that the call to ```f.Stat``` uses the existing
```err``` variable declared above, and just gives it a new value.



In a ```:=``` declaration a variable ```v``` may appear even
if it has already been declared, provided:


<ul>
<li>this declaration is in the same scope as the existing declaration of ```v```
(if ```v``` is already declared in an outer scope, the declaration will create a new variable §),</li>
<li>the corresponding value in the initialization is assignable to ```v```, and</li>
<li>there is at least one other variable in the declaration that is being declared anew.</li>
</ul>


This unusual property is pure pragmatism,
making it easy to use a single ```err``` value, for example,
in a long ```if-else``` chain.
You'll see it used often.



§ It's worth noting here that in Go the scope of function parameters and return values
is the same as the function body, even though they appear lexically outside the braces
that enclose the body.


<h3 id="for">For</h3>


The Go ```for``` loop is similar to&mdash;but not the same as&mdash;C's.
It unifies ```for```
and ```while``` and there is no ```do-while```.
There are three forms, only one of which has semicolons.

```
// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }
```


Short declarations make it easy to declare the index variable right in the loop.

```
sum := 0
for i := 0; i &lt; 10; i++ {
    sum += i
}
```


If you're looping over an array, slice, string, or map,
or reading from a channel, a ```range``` clause can
manage the loop.

```
for key, value := range oldMap {
    newMap[key] = value
}
```


If you only need the first item in the range (the key or index), drop the second:

```
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```


If you only need the second item in the range (the value), use the <em>blank identifier</em>, an underscore, to discard the first:

```
sum := 0
for _ , value := range array {
    sum += value
}
```


The blank identifier has many uses, as described in <a href="#blank">a later section</a>.



For strings, the ```range``` does more work for you, breaking out individual
Unicode code points by parsing the UTF-8.
Erroneous encodings consume one byte and produce the
replacement rune U+FFFD.
(The name (with associated builtin type) ```rune``` is Go terminology for a
single Unicode code point.
See <a href="/ref/spec#Rune_literals">the language specification</a>
for details.)
The loop

```
for pos, char := range "日本\x80語" { // \x80 is an illegal UTF-8 encoding
    fmt.Printf("character %#U starts at byte position %d\n", char, pos)
}
```

prints

```
character U+65E5 '日' starts at byte position 0
character U+672C '本' starts at byte position 3
character U+FFFD '�' starts at byte position 6
character U+8A9E '語' starts at byte position 7
```


Finally, Go has no comma operator and ```++``` and ```--```
are statements not expressions.
Thus if you want to run multiple variables in a ```for```
you should use parallel assignment (although that precludes ```++``` and ```--```).

```
// Reverse a
for i, j := 0, len(a)-1; i &lt; j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```

<h3 id="switch">Switch</h3>


Go's ```switch``` is more general than C's.
The expressions need not be constants or even integers,
the cases are evaluated top to bottom until a match is found,
and if the ```switch``` has no expression it switches on
```true```.
It's therefore possible&mdash;and idiomatic&mdash;to write an
```if```-```else```-```if```-```else```
chain as a ```switch```.


```
func unhex(c byte) byte {
    switch {
    case '0' &lt;= c &amp;&amp; c &lt;= '9':
        return c - '0'
    case 'a' &lt;= c &amp;&amp; c &lt;= 'f':
        return c - 'a' + 10
    case 'A' &lt;= c &amp;&amp; c &lt;= 'F':
        return c - 'A' + 10
    }
    return 0
}
```


There is no automatic fall through, but cases can be presented
in comma-separated lists.

```
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&amp;', '=', '#', '+', '%':
        return true
    }
    return false
}
```


Although they are not nearly as common in Go as some other C-like
languages, ```break``` statements can be used to terminate
a ```switch``` early.
Sometimes, though, it's necessary to break out of a surrounding loop,
not the switch, and in Go that can be accomplished by putting a label
on the loop and "breaking" to that label.
This example shows both uses.


```
Loop:
	for n := 0; n &lt; len(src); n += size {
		switch {
		case src[n] &lt; sizeOne:
			if validateOnly {
				break
			}
			size = 1
			update(src[n])

		case src[n] &lt; sizeTwo:
			if n+1 &gt;= len(src) {
				err = errShortInput
				break Loop
			}
			if validateOnly {
				break
			}
			size = 2
			update(src[n] + src[n+1]&lt;&lt;shift)
		}
	}
```


Of course, the ```continue``` statement also accepts an optional label
but it applies only to loops.



To close this section, here's a comparison routine for byte slices that uses two
```switch``` statements:

```
// Compare returns an integer comparing the two byte slices,
// lexicographically.
// The result will be 0 if a == b, -1 if a &lt; b, and +1 if a &gt; b
func Compare(a, b []byte) int {
    for i := 0; i &lt; len(a) &amp;&amp; i &lt; len(b); i++ {
        switch {
        case a[i] &gt; b[i]:
            return 1
        case a[i] &lt; b[i]:
            return -1
        }
    }
    switch {
    case len(a) &gt; len(b):
        return 1
    case len(a) &lt; len(b):
        return -1
    }
    return 0
}
```

<h3 id="type_switch">Type switch</h3>


A switch can also be used to discover the dynamic type of an interface
variable.  Such a <em>type switch</em> uses the syntax of a type
assertion with the keyword ```type``` inside the parentheses.
If the switch declares a variable in the expression, the variable will
have the corresponding type in each clause.
It's also idiomatic to reuse the name in such cases, in effect declaring
a new variable with the same name but a different type in each case.

```
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T\n", t)     // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t has type *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t has type *int
}
```

<h2 id="functions">Functions</h2>

<h3 id="multiple-returns">Multiple return values</h3>


One of Go's unusual features is that functions and methods
can return multiple values.  This form can be used to
improve on a couple of clumsy idioms in C programs: in-band
error returns such as ```-1``` for ```EOF```
and modifying an argument passed by address.



In C, a write error is signaled by a negative count with the
error code secreted away in a volatile location.
In Go, ```Write```
can return a count *and* an error: &ldquo;Yes, you wrote some
bytes but not all of them because you filled the device&rdquo;.
The signature of the ```Write``` method on files from
package ```os``` is:


```
func (file *File) Write(b []byte) (n int, err error)
```


and as the documentation says, it returns the number of bytes
written and a non-nil ```error``` when ```n```
```!=``` ```len(b)```.
This is a common style; see the section on error handling for more examples.



A similar approach obviates the need to pass a pointer to a return
value to simulate a reference parameter.
Here's a simple-minded function to
grab a number from a position in a byte slice, returning the number
and the next position.


```
func nextInt(b []byte, i int) (int, int) {
    for ; i &lt; len(b) &amp;&amp; !isDigit(b[i]); i++ {
    }
    x := 0
    for ; i &lt; len(b) &amp;&amp; isDigit(b[i]); i++ {
        x = x*10 + int(b[i]) - '0'
    }
    return x, i
}
```


You could use it to scan the numbers in an input slice ```b``` like this:


```
    for i := 0; i &lt; len(b); {
        x, i = nextInt(b, i)
        fmt.Println(x)
    }
```

<h3 id="named-results">Named result parameters</h3>


The return or result "parameters" of a Go function can be given names and
used as regular variables, just like the incoming parameters.
When named, they are initialized to the zero values for their types when
the function begins; if the function executes a ```return``` statement
with no arguments, the current values of the result parameters are
used as the returned values.



The names are not mandatory but they can make code shorter and clearer:
they're documentation.
If we name the results of ```nextInt``` it becomes
obvious which returned ```int```
is which.


```
func nextInt(b []byte, pos int) (value, nextPos int) {
```


Because named results are initialized and tied to an unadorned return, they can simplify
as well as clarify.  Here's a version
of ```io.ReadFull``` that uses them well:


```
func ReadFull(r Reader, buf []byte) (n int, err error) {
    for len(buf) &gt; 0 &amp;&amp; err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:]
    }
    return
}
```

<h3 id="defer">Defer</h3>


Go's ```defer``` statement schedules a function call (the
*deferred* function) to be run immediately before the function
executing the ```defer``` returns.  It's an unusual but
effective way to deal with situations such as resources that must be
released regardless of which path a function takes to return.  The
canonical examples are unlocking a mutex or closing a file.


```
// Contents returns the file's contents as a string.
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close will run when we're finished.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // append is discussed later.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // f will be closed if we return here.
        }
    }
    return string(result), nil // f will be closed if we return here.
}
```


Deferring a call to a function such as ```Close``` has two advantages.  First, it
guarantees that you will never forget to close the file, a mistake
that's easy to make if you later edit the function to add a new return
path.  Second, it means that the close sits near the open,
which is much clearer than placing it at the end of the function.



The arguments to the deferred function (which include the receiver if
the function is a method) are evaluated when the *defer*
executes, not when the *call* executes.  Besides avoiding worries
about variables changing values as the function executes, this means
that a single deferred call site can defer multiple function
executions.  Here's a silly example.


```
for i := 0; i &lt; 5; i++ {
    defer fmt.Printf("%d ", i)
}
```


Deferred functions are executed in LIFO order, so this code will cause
```4 3 2 1 0``` to be printed when the function returns.  A
more plausible example is a simple way to trace function execution
through the program.  We could write a couple of simple tracing
routines like this:


```
func trace(s string)   { fmt.Println("entering:", s) }
func untrace(s string) { fmt.Println("leaving:", s) }

// Use them like this:
func a() {
    trace("a")
    defer untrace("a")
    // do something....
}
```


We can do better by exploiting the fact that arguments to deferred
functions are evaluated when the ```defer``` executes.  The
tracing routine can set up the argument to the untracing routine.
This example:


```
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}

func main() {
    b()
}
```


prints


```
entering: b
in b
entering: a
in a
leaving: a
leaving: b
```


For programmers accustomed to block-level resource management from
other languages, ```defer``` may seem peculiar, but its most
interesting and powerful applications come precisely from the fact
that it's not block-based but function-based.  In the section on
```panic``` and ```recover``` we'll see another
example of its possibilities.


<h2 id="data">Data</h2>

<h3 id="allocation_new">Allocation with ```new```</h3>


Go has two allocation primitives, the built-in functions
```new``` and ```make```.
They do different things and apply to different types, which can be confusing,
but the rules are simple.
Let's talk about ```new``` first.
It's a built-in function that allocates memory, but unlike its namesakes
in some other languages it does not <em>initialize</em> the memory,
it only <em>zeros</em> it.
That is,
```new(T)``` allocates zeroed storage for a new item of type
```T``` and returns its address, a value of type ```*T```.
In Go terminology, it returns a pointer to a newly allocated zero value of type
```T```.



Since the memory returned by ```new``` is zeroed, it's helpful to arrange
when designing your data structures that the
zero value of each type can be used without further initialization.  This means a user of
the data structure can create one with ```new``` and get right to
work.
For example, the documentation for ```bytes.Buffer``` states that
"the zero value for ```Buffer``` is an empty buffer ready to use."
Similarly, ```sync.Mutex``` does not
have an explicit constructor or ```Init``` method.
Instead, the zero value for a ```sync.Mutex```
is defined to be an unlocked mutex.



The zero-value-is-useful property works transitively. Consider this type declaration.


```
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```


Values of type ```SyncedBuffer``` are also ready to use immediately upon allocation
or just declaration.  In the next snippet, both ```p``` and ```v``` will work
correctly without further arrangement.


```
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

<h3 id="composite_literals">Constructors and composite literals</h3>


Sometimes the zero value isn't good enough and an initializing
constructor is necessary, as in this example derived from
package ```os```.


```
func NewFile(fd int, name string) *File {
    if fd &lt; 0 {
        return nil
    }
    f := new(File)
    f.fd = fd
    f.name = name
    f.dirinfo = nil
    f.nepipe = 0
    return f
}
```


There's a lot of boiler plate in there.  We can simplify it
using a *composite literal*, which is
an expression that creates a
new instance each time it is evaluated.


```
func NewFile(fd int, name string) *File {
    if fd &lt; 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &amp;f
}
```


Note that, unlike in C, it's perfectly OK to return the address of a local variable;
the storage associated with the variable survives after the function
returns.
In fact, taking the address of a composite literal
allocates a fresh instance each time it is evaluated,
so we can combine these last two lines.


```
    return &amp;File{fd, name, nil, 0}
```


The fields of a composite literal are laid out in order and must all be present.
However, by labeling the elements explicitly as *field*```:```*value*
pairs, the initializers can appear in any
order, with the missing ones left as their respective zero values.  Thus we could say


```
    return &amp;File{fd: fd, name: name}
```


As a limiting case, if a composite literal contains no fields at all, it creates
a zero value for the type.  The expressions ```new(File)``` and ```&amp;File{}``` are equivalent.



Composite literals can also be created for arrays, slices, and maps,
with the field labels being indices or map keys as appropriate.
In these examples, the initializations work regardless of the values of ```Enone```,
```Eio```, and ```Einval```, as long as they are distinct.


```
a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
```

<h3 id="allocation_make">Allocation with ```make```</h3>


Back to allocation.
The built-in function ```make(T, ```*args*```)``` serves
a purpose different from ```new(T)```.
It creates slices, maps, and channels only, and it returns an <em>initialized</em>
(not <em>zeroed</em>)
value of type ```T``` (not ```*T```).
The reason for the distinction
is that these three types represent, under the covers, references to data structures that
must be initialized before use.
A slice, for example, is a three-item descriptor
containing a pointer to the data (inside an array), the length, and the
capacity, and until those items are initialized, the slice is ```nil```.
For slices, maps, and channels,
```make``` initializes the internal data structure and prepares
the value for use.
For instance,


```
make([]int, 10, 100)
```


allocates an array of 100 ints and then creates a slice
structure with length 10 and a capacity of 100 pointing at the first
10 elements of the array.
(When making a slice, the capacity can be omitted; see the section on slices
for more information.)
In contrast, ```new([]int)``` returns a pointer to a newly allocated, zeroed slice
structure, that is, a pointer to a ```nil``` slice value.



These examples illustrate the difference between ```new``` and
```make```.


```
var p *[]int = new([]int)       // allocates slice structure; *p == nil; rarely useful
var v  []int = make([]int, 100) // the slice v now refers to a new array of 100 ints

// Unnecessarily complex:
var p *[]int = new([]int)
*p = make([]int, 100, 100)

// Idiomatic:
v := make([]int, 100)
```


Remember that ```make``` applies only to maps, slices and channels
and does not return a pointer.
To obtain an explicit pointer allocate with ```new``` or take the address
of a variable explicitly.


<h3 id="arrays">Arrays</h3>


Arrays are useful when planning the detailed layout of memory and sometimes
can help avoid allocation, but primarily
they are a building block for slices, the subject of the next section.
To lay the foundation for that topic, here are a few words about arrays.



There are major differences between the ways arrays work in Go and C.
In Go,

<ul>
<li>
Arrays are values. Assigning one array to another copies all the elements.
</li>
<li>
In particular, if you pass an array to a function, it
will receive a *copy* of the array, not a pointer to it.
<li>
The size of an array is part of its type.  The types ```[10]int```
and ```[20]int``` are distinct.
</li>
</ul>


The value property can be useful but also expensive; if you want C-like behavior and efficiency,
you can pass a pointer to the array.


```
func Sum(a *[3]float64) (sum float64) {
    for _, v := range *a {
        sum += v
    }
    return
}

array := [...]float64{7.0, 8.5, 9.1}
x := Sum(&amp;array)  // Note the explicit address-of operator
```


But even this style isn't idiomatic Go.
Use slices instead.


<h3 id="slices">Slices</h3>


Slices wrap arrays to give a more general, powerful, and convenient
interface to sequences of data.  Except for items with explicit
dimension such as transformation matrices, most array programming in
Go is done with slices rather than simple arrays.


Slices hold references to an underlying array, and if you assign one
slice to another, both refer to the same array.
If a function takes a slice argument, changes it makes to
the elements of the slice will be visible to the caller, analogous to
passing a pointer to the underlying array.  A ```Read```
function can therefore accept a slice argument rather than a pointer
and a count; the length within the slice sets an upper
limit of how much data to read.  Here is the signature of the ```Read``` method of the ```File``` type in package ```os```:

```
func (f * File) Read(buf []byte) (n int, err error)
```

The method returns the number of bytes read and an error value, if
any.
To read into the first 32 bytes of a larger buffer
```buf```, *slice* (here used as a verb) the buffer.

```
    n, err := f.Read(buf[0:32])
```

Such slicing is common and efficient.  In fact, leaving efficiency aside for
the moment, the following snippet would also read the first 32 bytes of the buffer.

```
    var n int
    var err error
    for i := 0; i &lt; 32; i++ {
        nbytes, e := f.Read(buf[i:i+1])  // Read one byte.
        if nbytes == 0 || e != nil {
            err = e
            break
        }
        n += nbytes
    }
```

The length of a slice may be changed as long as it still fits within
the limits of the underlying array; just assign it to a slice of
itself.  The *capacity* of a slice, accessible by the built-in
function ```cap```, reports the maximum length the slice may
assume.  Here is a function to append data to a slice.  If the data
exceeds the capacity, the slice is reallocated.  The
resulting slice is returned.  The function uses the fact that
```len``` and ```cap``` are legal when applied to the
```nil``` slice, and return 0.

```
func Append(slice, data []byte) []byte {
    l := len(slice)
    if l + len(data) &gt; cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))* 2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    return slice
}
```

We must return the slice afterwards because, although ```Append```
can modify the elements of ```slice```, the slice itself (the run-time data
structure holding the pointer, length, and capacity) is passed by value.



The idea of appending to a slice is so useful it's captured by the
```append``` built-in function.  To understand that function's
design, though, we need a little more information, so we'll return
to it later.


<h3 id="two_dimensional_slices">Two-dimensional slices</h3>


Go's arrays and slices are one-dimensional.
To create the equivalent of a 2D array or slice, it is necessary to define an array-of-arrays
or slice-of-slices, like this:


```
type Transform [3][3]float64  // A 3x3 array, really an array of arrays.
type LinesOfText [][]byte     // A slice of byte slices.
```


Because slices are variable-length, it is possible to have each inner
slice be a different length.
That can be a common situation, as in our ```LinesOfText```
example: each line has an independent length.


```
text := LinesOfText{
	[]byte("Now is the time"),
	[]byte("for all good gophers"),
	[]byte("to bring some fun to the party."),
}
```


Sometimes it's necessary to allocate a 2D slice, a situation that can arise when
processing scan lines of pixels, for instance.
There are two ways to achieve this.
One is to allocate each slice independently; the other
is to allocate a single array and point the individual slices into it.
Which to use depends on your application.
If the slices might grow or shrink, they should be allocated independently
to avoid overwriting the next line; if not, it can be more efficient to construct
the object with a single allocation.
For reference, here are sketches of the two methods.
First, a line at a time:


```
// Allocate the top-level slice.
picture := make([][]uint8, YSize) // One row per unit of y.
// Loop over the rows, allocating the slice for each row.
for i := range picture {
	picture[i] = make([]uint8, XSize)
}
```


And now as one allocation, sliced into lines:


```
// Allocate the top-level slice, the same as before.
picture := make([][]uint8, YSize) // One row per unit of y.
// Allocate one large slice to hold all the pixels.
pixels := make([]uint8, XSize*YSize) // Has type []uint8 even though picture is [][]uint8.
// Loop over the rows, slicing each row from the front of the remaining pixels slice.
for i := range picture {
	picture[i], pixels = pixels[:XSize], pixels[XSize:]
}
```

<h3 id="maps">Maps</h3>


Maps are a convenient and powerful built-in data structure that associate
values of one type (the <em>key</em>) with values of another type
(the <em>element</em> or <em>value</em>)
The key can be of any type for which the equality operator is defined,
such as integers,
floating point and complex numbers,
strings, pointers, interfaces (as long as the dynamic type
supports equality), structs and arrays.
Slices cannot be used as map keys,
because equality is not defined on them.
Like slices, maps hold references to an underlying data structure.
If you pass a map to a function
that changes the contents of the map, the changes will be visible
in the caller.


Maps can be constructed using the usual composite literal syntax
with colon-separated key-value pairs,
so it's easy to build them during initialization.

```
var timeZone = map[string]int{
    "UTC":  0*60*60,
    "EST": -5*60*60,
    "CST": -6*60*60,
    "MST": -7*60*60,
    "PST": -8*60*60,
}
```

Assigning and fetching map values looks syntactically just like
doing the same for arrays and slices except that the index doesn't
need to be an integer.

```
offset := timeZone["EST"]
```

An attempt to fetch a map value with a key that
is not present in the map will return the zero value for the type
of the entries
in the map.  For instance, if the map contains integers, looking
up a non-existent key will return ```0```.
A set can be implemented as a map with value type ```bool```.
Set the map entry to ```true``` to put the value in the set, and then
test it by simple indexing.

```
attended := map[string]bool{
    "Ann": true,
    "Joe": true,
    ...
}

if attended[person] { // will be false if person is not in the map
    fmt.Println(person, "was at the meeting")
}
```

Sometimes you need to distinguish a missing entry from
a zero value.  Is there an entry for ```"UTC"```
or is that the empty string because it's not in the map at all?
You can discriminate with a form of multiple assignment.

```
var seconds int
var ok bool
seconds, ok = timeZone[tz]
```

For obvious reasons this is called the &ldquo;comma ok&rdquo; idiom.
In this example, if ```tz``` is present, ```seconds```
will be set appropriately and ```ok``` will be true; if not,
```seconds``` will be set to zero and ```ok``` will
be false.
Here's a function that puts it together with a nice error report:

```
func offset(tz string) int {
    if seconds, ok := timeZone[tz]; ok {
        return seconds
    }
    log.Println("unknown time zone:", tz)
    return 0
}
```

To test for presence in the map without worrying about the actual value,
you can use the <a href="#blank">blank identifier</a> (```_```)
in place of the usual variable for the value.

```
_ , present := timeZone[tz]
```

To delete a map entry, use the ```delete```
built-in function, whose arguments are the map and the key to be deleted.
It's safe to do this even if the key is already absent
from the map.

```
delete(timeZone, "PDT")  // Now on Standard Time
```

<h3 id="printing">Printing</h3>


Formatted printing in Go uses a style similar to C's ```printf```
family but is richer and more general. The functions live in the ```fmt```
package and have capitalized names: ```fmt.Printf```, ```fmt.Fprintf```,
```fmt.Sprintf``` and so on.  The string functions (```Sprintf``` etc.)
return a string rather than filling in a provided buffer.


You don't need to provide a format string.  For each of ```Printf```,
```Fprintf``` and ```Sprintf``` there is another pair
of functions, for instance ```Print``` and ```Println```.
These functions do not take a format string but instead generate a default
format for each argument. The ```Println``` versions also insert a blank
between arguments and append a newline to the output while
the ```Print``` versions add blanks only if the operand on neither side is a string.
In this example each line produces the same output.

```
fmt.Printf("Hello %d\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))
```

The formatted print functions ```fmt.Fprint```
and friends take as a first argument any object
that implements the ```io.Writer``` interface; the variables ```os.Stdout```
and ```os.Stderr``` are familiar instances.


Here things start to diverge from C.  First, the numeric formats such as ```%d```
do not take flags for signedness or size; instead, the printing routines use the
type of the argument to decide these properties.

```
var x uint64 = 1&lt;&lt;64 - 1
fmt.Printf("%d %x; %d %x\n", x, x, int64(x), int64(x))
```

prints

```
18446744073709551615 ffffffffffffffff; -1 -1
```

If you just want the default conversion, such as decimal for integers, you can use
the catchall format ```%v``` (for &ldquo;value&rdquo;); the result is exactly
what ```Print``` and ```Println``` would produce.
Moreover, that format can print <em>any</em> value, even arrays, slices, structs, and
maps.  Here is a print statement for the time zone map defined in the previous section.

```
fmt.Printf("%v\n", timeZone)  // or just fmt.Println(timeZone)
```

which gives output

```
map[CST:-21600 PST:-28800 EST:-18000 UTC:0 MST:-25200]
```

For maps the keys may be output in any order, of course.
When printing a struct, the modified format ```%+v``` annotates the
fields of the structure with their names, and for any value the alternate
format ```%#v``` prints the value in full Go syntax.

```
type T struct {
    a int
    b float64
    c string
}
t := &amp;T{ 7, -2.35, "abc\tdef" }
fmt.Printf("%v\n", t)
fmt.Printf("%+v\n", t)
fmt.Printf("%#v\n", t)
fmt.Printf("%#v\n", timeZone)
```

prints

```
&amp;{7 -2.35 abc   def}
&amp;{a:7 b:-2.35 c:abc     def}
&amp;main.T{a:7, b:-2.35, c:"abc\tdef"}
map[string] int{"CST":-21600, "PST":-28800, "EST":-18000, "UTC":0, "MST":-25200}
```

(Note the ampersands.)
That quoted string format is also available through ```%q``` when
applied to a value of type ```string``` or ```[]byte```.
The alternate format ```%#q``` will use backquotes instead if possible.
(The ```%q``` format also applies to integers and runes, producing a
single-quoted rune constant.)
Also, ```%x``` works on strings, byte arrays and byte slices as well as
on integers, generating a long hexadecimal string, and with
a space in the format (```%&nbsp;x```) it puts spaces between the bytes.


Another handy format is ```%T```, which prints the <em>type</em> of a value.

```
fmt.Printf(&quot;%T\n&quot;, timeZone)
```

prints

```
map[string] int
```

If you want to control the default format for a custom type, all that's required is to define
a method with the signature ```String() string``` on the type.
For our simple type ```T```, that might look like this.

```
func (t * T) String() string {
    return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c)
}
fmt.Printf("%v\n", t)
```

to print in the format

```
7/-2.35/"abc\tdef"
```

(If you need to print <em>values</em> of type ```T``` as well as pointers to ```T```,
the receiver for ```String``` must be of value type; this example used a pointer because
that's more efficient and idiomatic for struct types.
See the section below on <a href="#pointers_vs_values">pointers vs. value receivers</a> for more information.)



Our ```String``` method is able to call ```Sprintf``` because the
print routines are fully reentrant and can be wrapped this way.
There is one important detail to understand about this approach,
however: don't construct a ```String``` method by calling
```Sprintf``` in a way that will recur into your ```String```
method indefinitely.  This can happen if the ```Sprintf```
call attempts to print the receiver directly as a string, which in
turn will invoke the method again.  It's a common and easy mistake
to make, as this example shows.


```
type MyString string

func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", m) // Error: will recur forever.
}
```


It's also easy to fix: convert the argument to the basic string type, which does not have the
method.


```
type MyString string
func (m MyString) String() string {
    return fmt.Sprintf("MyString=%s", string(m)) // OK: note conversion.
}
```


In the <a href="#initialization">initialization section</a> we'll see another technique that avoids this recursion.



Another printing technique is to pass a print routine's arguments directly to another such routine.
The signature of ```Printf``` uses the type ```...interface{}```
for its final argument to specify that an arbitrary number of parameters (of arbitrary type)
can appear after the format.

```
func Printf(format string, v ...interface{}) (n int, err error) {
```

Within the function ```Printf```, ```v``` acts like a variable of type
```[]interface{}``` but if it is passed to another variadic function, it acts like
a regular list of arguments.
Here is the implementation of the
function ```log.Println``` we used above. It passes its arguments directly to
```fmt.Sprintln``` for the actual formatting.

```
// Println prints to the standard logger in the manner of fmt.Println.
func Println(v ...interface{}) {
    std.Output(2, fmt.Sprintln(v...))  // Output takes parameters (int, string)
}
```

We write ```...``` after ```v``` in the nested call to ```Sprintln``` to tell the
compiler to treat ```v``` as a list of arguments; otherwise it would just pass
```v``` as a single slice argument.


There's even more to printing than we've covered here.  See the ```godoc``` documentation
for package ```fmt``` for the details.


By the way, a ```...``` parameter can be of a specific type, for instance ```...int```
for a min function that chooses the least of a list of integers:

```
func Min(a ...int) int {
    min := int(^uint(0) >> 1)  // largest int
    for _ , i := range a {
        if i &lt; min {
            min = i
        }
    }
    return min
}
```

<h3 id="append">Append</h3>

Now we have the missing piece we needed to explain the design of
the ```append``` built-in function.  The signature of ```append```
is different from our custom ```Append``` function above.
Schematically, it's like this:

```
func append(slice []*T*, elements ...*T*) []*T*
```

where *T* is a placeholder for any given type.  You can't
actually write a function in Go where the type ```T```
is determined by the caller.
That's why ```append``` is built in: it needs support from the
compiler.


What ```append``` does is append the elements to the end of
the slice and return the result.  The result needs to be returned
because, as with our hand-written ```Append```, the underlying
array may change.  This simple example

```
x := []int{1,2,3}
x = append(x, 4, 5, 6)
fmt.Println(x)
```

prints ```[1 2 3 4 5 6]```.  So ```append``` works a
little like ```Printf```, collecting an arbitrary number of
arguments.


But what if we wanted to do what our ```Append``` does and
append a slice to a slice?  Easy: use ```...``` at the call
site, just as we did in the call to ```Output``` above.  This
snippet produces identical output to the one above.

```
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x)
```

Without that ```...```, it wouldn't compile because the types
would be wrong; ```y``` is not of type ```int```.


<h2 id="initialization">Initialization</h2>


Although it doesn't look superficially very different from
initialization in C or C++, initialization in Go is more powerful.
Complex structures can be built during initialization and the ordering
issues among initialized objects, even among different packages, are handled
correctly.


<h3 id="constants">Constants</h3>


Constants in Go are just that&mdash;constant.
They are created at compile time, even when defined as
locals in functions,
and can only be numbers, characters (runes), strings or booleans.
Because of the compile-time restriction, the expressions
that define them must be constant expressions,
evaluatable by the compiler.  For instance,
```1&lt;&lt;3``` is a constant expression, while
```math.Sin(math.Pi/4)``` is not because
the function call to ```math.Sin``` needs
to happen at run time.



In Go, enumerated constants are created using the ```iota```
enumerator.  Since ```iota``` can be part of an expression and
expressions can be implicitly repeated, it is easy to build intricate
sets of values.

{{code "/doc/progs/eff_bytesize.go" `/^type ByteSize/` `/^\)/`}}

The ability to attach a method such as ```String``` to any
user-defined type makes it possible for arbitrary values to format themselves
automatically for printing.
Although you'll see it most often applied to structs, this technique is also useful for
scalar types such as floating-point types like ```ByteSize```.

{{code "/doc/progs/eff_bytesize.go" `/^func.*ByteSize.*String/` `/^}/`}}

The expression ```YB``` prints as ```1.00YB```,
while ```ByteSize(1e13)``` prints as ```9.09TB```.



The use here of ```Sprintf```
to implement ```ByteSize```'s ```String``` method is safe
(avoids recurring indefinitely) not because of a conversion but
because it calls ```Sprintf``` with ```%f```,
which is not a string format: ```Sprintf``` will only call
the ```String``` method when it wants a string, and ```%f```
wants a floating-point value.


<h3 id="variables">Variables</h3>


Variables can be initialized just like constants but the
initializer can be a general expression computed at run time.

```
var (
    home   = os.Getenv("HOME")
    user   = os.Getenv("USER")
    gopath = os.Getenv("GOPATH")
)
```

<h3 id="init">The init function</h3>


Finally, each source file can define its own niladic ```init``` function to
set up whatever state is required.  (Actually each file can have multiple
```init``` functions.)
And finally means finally: ```init``` is called after all the
variable declarations in the package have evaluated their initializers,
and those are evaluated only after all the imported packages have been
initialized.


Besides initializations that cannot be expressed as declarations,
a common use of ```init``` functions is to verify or repair
correctness of the program state before real execution begins.


```
func init() {
    if user == "" {
        log.Fatal("$USER not set")
    }
    if home == "" {
        home = "/home/" + user
    }
    if gopath == "" {
        gopath = home + "/go"
    }
    // gopath may be overridden by --gopath flag on command line.
    flag.StringVar(&amp;gopath, "gopath", gopath, "override default GOPATH")
}
```

<h2 id="methods">Methods</h2>

<h3 id="pointers_vs_values">Pointers vs. Values</h3>

As we saw with ```ByteSize```,
methods can be defined for any named type (except a pointer or an interface);
the receiver does not have to be a struct.


In the discussion of slices above, we wrote an ```Append```
function.  We can define it as a method on slices instead.  To do
this, we first declare a named type to which we can bind the method, and
then make the receiver for the method a value of that type.

```
type ByteSlice []byte

func (slice ByteSlice) Append(data []byte) []byte {
    // Body exactly the same as the Append function defined above.
}
```

This still requires the method to return the updated slice.  We can
eliminate that clumsiness by redefining the method to take a
*pointer* to a ```ByteSlice``` as its receiver, so the
method can overwrite the caller's slice.

```
func (p *ByteSlice) Append(data []byte) {
    slice := *p
    // Body as above, without the return.
    *p = slice
}
```

In fact, we can do even better.  If we modify our function so it looks
like a standard ```Write``` method, like this,

```
func (p *ByteSlice) Write(data []byte) (n int, err error) {
    slice := *p
    // Again as above.
    *p = slice
    return len(data), nil
}
```

then the type ```*ByteSlice``` satisfies the standard interface
```io.Writer```, which is handy.  For instance, we can
print into one.

```
    var b ByteSlice
    fmt.Fprintf(&amp;b, "This hour has %d days\n", 7)
```

We pass the address of a ```ByteSlice```
because only ```*ByteSlice``` satisfies ```io.Writer```.
The rule about pointers vs. values for receivers is that value methods
can be invoked on pointers and values, but pointer methods can only be
invoked on pointers.



This rule arises because pointer methods can modify the receiver; invoking
them on a value would cause the method to receive a copy of the value, so
any modifications would be discarded.
The language therefore disallows this mistake.
There is a handy exception, though. When the value is addressable, the
language takes care of the common case of invoking a pointer method on a
value by inserting the address operator automatically.
In our example, the variable ```b``` is addressable, so we can call
its ```Write``` method with just ```b.Write```. The compiler
will rewrite that to ```(&amp;b).Write``` for us.



By the way, the idea of using ```Write``` on a slice of bytes
is central to the implementation of ```bytes.Buffer```.


<h2 id="interfaces_and_types">Interfaces and other types</h2>

<h3 id="interfaces">Interfaces</h3>

Interfaces in Go provide a way to specify the behavior of an
object: if something can do <em>this</em>, then it can be used
<em>here</em>.  We've seen a couple of simple examples already;
custom printers can be implemented by a ```String``` method
while ```Fprintf``` can generate output to anything
with a ```Write``` method.
Interfaces with only one or two methods are common in Go code, and are
usually given a name derived from the method, such as ```io.Writer```
for something that implements ```Write```.


A type can implement multiple interfaces.
For instance, a collection can be sorted
by the routines in package ```sort``` if it implements
```sort.Interface```, which contains ```Len()```,
```Less(i, j int) bool```, and ```Swap(i, j int)```,
and it could also have a custom formatter.
In this contrived example ```Sequence``` satisfies both.

{{code "/doc/progs/eff_sequence.go" `/^type/` "$"}}

<h3 id="conversions">Conversions</h3>


The ```String``` method of ```Sequence``` is recreating the
work that ```Sprint``` already does for slices.  We can share the
effort if we convert the ```Sequence``` to a plain
```[]int``` before calling ```Sprint```.

```
func (s Sequence) String() string {
    sort.Sort(s)
    return fmt.Sprint([]int(s))
}
```

This method is another example of the conversion technique for calling
```Sprintf``` safely from a ```String``` method.
Because the two types (```Sequence``` and ```[]int```)
are the same if we ignore the type name, it's legal to convert between them.
The conversion doesn't create a new value, it just temporarily acts
as though the existing value has a new type.
(There are other legal conversions, such as from integer to floating point, that
do create a new value.)


It's an idiom in Go programs to convert the
type of an expression to access a different
set of methods. As an example, we could use the existing
type ```sort.IntSlice``` to reduce the entire example
to this:

```
type Sequence []int

// Method for printing - sorts the elements before printing
func (s Sequence) String() string {
    sort.IntSlice(s).Sort()
    return fmt.Sprint([]int(s))
}
```

Now, instead of having ```Sequence``` implement multiple
interfaces (sorting and printing), we're using the ability of a data item to be
converted to multiple types (```Sequence```, ```sort.IntSlice```
and ```[]int```), each of which does some part of the job.
That's more unusual in practice but can be effective.


<h3 id="interface_conversions">Interface conversions and type assertions</h3>


<a href="#type_switch">Type switches</a> are a form of conversion: they take an interface and, for
each case in the switch, in a sense convert it to the type of that case.
Here's a simplified version of how the code under ```fmt.Printf``` turns a value into
a string using a type switch.
If it's already a string, we want the actual string value held by the interface, while if it has a
```String``` method we want the result of calling the method.


```
type Stringer interface {
    String() string
}

var value interface{} // Value provided by caller.
switch str := value.(type) {
case string:
    return str
case Stringer:
    return str.String()
}
```


The first case finds a concrete value; the second converts the interface into another interface.
It's perfectly fine to mix types this way.



What if there's only one type we care about? If we know the value holds a ```string```
and we just want to extract it?
A one-case type switch would do, but so would a <em>type assertion</em>.
A type assertion takes an interface value and extracts from it a value of the specified explicit type.
The syntax borrows from the clause opening a type switch, but with an explicit
type rather than the ```type``` keyword:


```
value.(typeName)
```


and the result is a new value with the static type ```typeName```.
That type must either be the concrete type held by the interface, or a second interface
type that the value can be converted to.
To extract the string we know is in the value, we could write:


```
str := value.(string)
```


But if it turns out that the value does not contain a string, the program will crash with a run-time error.
To guard against that, use the "comma, ok" idiom to test, safely, whether the value is a string:


```
str, ok := value.(string)
if ok {
    fmt.Printf("string value is: %q\n", str)
} else {
    fmt.Printf("value is not a string\n")
}
```


If the type assertion fails, ```str``` will still exist and be of type string, but it will have
the zero value, an empty string.



As an illustration of the capability, here's an ```if```-```else```
statement that's equivalent to the type switch that opened this section.


```
if str, ok := value.(string); ok {
    return str
} else if str, ok := value.(Stringer); ok {
    return str.String()
}
```

<h3 id="generality">Generality</h3>

If a type exists only to implement an interface and will
never have exported methods beyond that interface, there is
no need to export the type itself.
Exporting just the interface makes it clear the value has no
interesting behavior beyond what is described in the
interface.
It also avoids the need to repeat the documentation
on every instance of a common method.


In such cases, the constructor should return an interface value
rather than the implementing type.
As an example, in the hash libraries
both ```crc32.NewIEEE``` and ```adler32.New```
return the interface type ```hash.Hash32```.
Substituting the CRC-32 algorithm for Adler-32 in a Go program
requires only changing the constructor call;
the rest of the code is unaffected by the change of algorithm.


A similar approach allows the streaming cipher algorithms
in the various ```crypto``` packages to be
separated from the block ciphers they chain together.
The ```Block``` interface
in the ```crypto/cipher``` package specifies the
behavior of a block cipher, which provides encryption
of a single block of data.
Then, by analogy with the ```bufio``` package,
cipher packages that implement this interface
can be used to construct streaming ciphers, represented
by the ```Stream``` interface, without
knowing the details of the block encryption.


The  ```crypto/cipher``` interfaces look like this:

```
type Block interface {
    BlockSize() int
    Encrypt(src, dst []byte)
    Decrypt(src, dst []byte)
}

type Stream interface {
    XORKeyStream(dst, src []byte)
}
```


Here's the definition of the counter mode (CTR) stream,
which turns a block cipher into a streaming cipher; notice
that the block cipher's details are abstracted away:


```
// NewCTR returns a Stream that encrypts/decrypts using the given Block in
// counter mode. The length of iv must be the same as the Block's block size.
func NewCTR(block Block, iv []byte) Stream
```

```NewCTR``` applies not
just to one specific encryption algorithm and data source but to any
implementation of the ```Block``` interface and any
```Stream```.  Because they return
interface values, replacing CTR
encryption with other encryption modes is a localized change.  The constructor
calls must be edited, but because the surrounding code must treat the result only
as a ```Stream```, it won't notice the difference.


<h3 id="interface_methods">Interfaces and methods</h3>

Since almost anything can have methods attached, almost anything can
satisfy an interface.  One illustrative example is in the ```http```
package, which defines the ```Handler``` interface.  Any object
that implements ```Handler``` can serve HTTP requests.

```
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

```ResponseWriter``` is itself an interface that provides access
to the methods needed to return the response to the client.
Those methods include the standard ```Write``` method, so an
```http.ResponseWriter``` can be used wherever an ```io.Writer```
can be used.
```Request``` is a struct containing a parsed representation
of the request from the client.


For brevity, let's ignore POSTs and assume HTTP requests are always
GETs; that simplification does not affect the way the handlers are
set up.  Here's a trivial but complete implementation of a handler to
count the number of times the
page is visited.

```
// Simple counter server.
type Counter struct {
    n int
}

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ctr.n++
    fmt.Fprintf(w, "counter = %d\n", ctr.n)
}
```

(Keeping with our theme, note how ```Fprintf``` can print to an
```http.ResponseWriter```.)
For reference, here's how to attach such a server to a node on the URL tree.

```
import "net/http"
...
ctr := new(Counter)
http.Handle("/counter", ctr)
```

But why make ```Counter``` a struct?  An integer is all that's needed.
(The receiver needs to be a pointer so the increment is visible to the caller.)

```
// Simpler counter server.
type Counter int

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    *ctr++
    fmt.Fprintf(w, "counter = %d\n", *ctr)
}
```

What if your program has some internal state that needs to be notified that a page
has been visited?  Tie a channel to the web page.

```
// A channel that sends a notification on each visit.
// (Probably want the channel to be buffered.)
type Chan chan *http.Request

func (ch Chan) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ch &lt;- req
    fmt.Fprint(w, "notification sent")
}
```

Finally, let's say we wanted to present on ```/args``` the arguments
used when invoking the server binary.
It's easy to write a function to print the arguments.

```
func ArgServer() {
    fmt.Println(os.Args)
}
```

How do we turn that into an HTTP server?  We could make ```ArgServer```
a method of some type whose value we ignore, but there's a cleaner way.
Since we can define a method for any type except pointers and interfaces,
we can write a method for a function.
The ```http``` package contains this code:

```
// The HandlerFunc type is an adapter to allow the use of
// ordinary functions as HTTP handlers.  If f is a function
// with the appropriate signature, HandlerFunc(f) is a
// Handler object that calls f.
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(c, req).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, req *Request) {
    f(w, req)
}
```

```HandlerFunc``` is a type with a method, ```ServeHTTP```,
so values of that type can serve HTTP requests.  Look at the implementation
of the method: the receiver is a function, ```f```, and the method
calls ```f```.  That may seem odd but it's not that different from, say,
the receiver being a channel and the method sending on the channel.


To make ```ArgServer``` into an HTTP server, we first modify it
to have the right signature.

```
// Argument server.
func ArgServer(w http.ResponseWriter, req *http.Request) {
    fmt.Fprintln(w, os.Args)
}
```

```ArgServer``` now has same signature as ```HandlerFunc```,
so it can be converted to that type to access its methods,
just as we converted ```Sequence``` to ```IntSlice```
to access ```IntSlice.Sort```.
The code to set it up is concise:

```
http.Handle("/args", http.HandlerFunc(ArgServer))
```

When someone visits the page ```/args```,
the handler installed at that page has value ```ArgServer```
and type ```HandlerFunc```.
The HTTP server will invoke the method ```ServeHTTP```
of that type, with ```ArgServer``` as the receiver, which will in turn call
```ArgServer``` (via the invocation ```f(c, req)```
inside ```HandlerFunc.ServeHTTP```).
The arguments will then be displayed.


In this section we have made an HTTP server from a struct, an integer,
a channel, and a function, all because interfaces are just sets of
methods, which can be defined for (almost) any type.


<h2 id="blank">The blank identifier</h2>


We've mentioned the blank identifier a couple of times now, in the context of
<a href="#for">```for``` ```range``` loops</a>
and <a href="#maps">maps</a>.
The blank identifier can be assigned or declared with any value of any type, with the
value discarded harmlessly.
It's a bit like writing to the Unix ```/dev/null``` file:
it represents a write-only value
to be used as a place-holder
where a variable is needed but the actual value is irrelevant.
It has uses beyond those we've seen already.


<h3 id="blank_assign">The blank identifier in multiple assignment</h3>


The use of a blank identifier in a ```for``` ```range``` loop is a
special case of a general situation: multiple assignment.



If an assignment requires multiple values on the left side,
but one of the values will not be used by the program,
a blank identifier on the left-hand-side of
the assignment avoids the need
to create a dummy variable and makes it clear that the
value is to be discarded.
For instance, when calling a function that returns
a value and an error, but only the error is important,
use the blank identifier to discard the irrelevant value.


```
if _, err := os.Stat(path); os.IsNotExist(err) {
	fmt.Printf("%s does not exist\n", path)
}
```


Occasionally you'll see code that discards the error value in order
to ignore the error; this is terrible practice. Always check error returns;
they're provided for a reason.


```
// Bad! This code will crash if path does not exist.
fi, _ := os.Stat(path)
if fi.IsDir() {
    fmt.Printf("%s is a directory\n", path)
}
```

<h3 id="blank_unused">Unused imports and variables</h3>


It is an error to import a package or to declare a variable without using it.
Unused imports bloat the program and slow compilation,
while a variable that is initialized but not used is at least
a wasted computation and perhaps indicative of a
larger bug.
When a program is under active development, however,
unused imports and variables often arise and it can
be annoying to delete them just to have the compilation proceed,
only to have them be needed again later.
The blank identifier provides a workaround.


This half-written program has two unused imports
(```fmt``` and ```io```)
and an unused variable (```fd```),
so it will not compile, but it would be nice to see if the
code so far is correct.

{{code "/doc/progs/eff_unused1.go" `/package/` `$`}}

To silence complaints about the unused imports, use a
blank identifier to refer to a symbol from the imported package.
Similarly, assigning the unused variable ```fd```
to the blank identifier will silence the unused variable error.
This version of the program does compile.

{{code "/doc/progs/eff_unused2.go" `/package/` `$`}}


By convention, the global declarations to silence import errors
should come right after the imports and be commented,
both to make them easy to find and as a reminder to clean things up later.


<h3 id="blank_import">Import for side effect</h3>


An unused import like ```fmt``` or ```io``` in the
previous example should eventually be used or removed:
blank assignments identify code as a work in progress.
But sometimes it is useful to import a package only for its
side effects, without any explicit use.
For example, during its ```init``` function,
the ```<a href="/pkg/net/http/pprof/">net/http/pprof</a>```
package registers HTTP handlers that provide
debugging information. It has an exported API, but
most clients need only the handler registration and
access the data through a web page.
To import the package only for its side effects, rename the package
to the blank identifier:

```
import _ "net/http/pprof"
```

This form of import makes clear that the package is being
imported for its side effects, because there is no other possible
use of the package: in this file, it doesn't have a name.
(If it did, and we didn't use that name, the compiler would reject the program.)


<h3 id="blank_implements">Interface checks</h3>


As we saw in the discussion of <a href="#interfaces_and_types">interfaces</a> above,
a type need not declare explicitly that it implements an interface.
Instead, a type implements the interface just by implementing the interface's methods.
In practice, most interface conversions are static and therefore checked at compile time.
For example, passing an ```*os.File``` to a function
expecting an ```io.Reader``` will not compile unless
```*os.File``` implements the ```io.Reader``` interface.



Some interface checks do happen at run-time, though.
One instance is in the ```<a href="/pkg/encoding/json/">encoding/json</a>```
package, which defines a ```<a href="/pkg/encoding/json/#Marshaler">Marshaler</a>```
interface. When the JSON encoder receives a value that implements that interface,
the encoder invokes the value's marshaling method to convert it to JSON
instead of doing the standard conversion.
The encoder checks this property at run time with a <a href="#interface_conversions">type assertion</a> like:


```
m, ok := val.(json.Marshaler)
```


If it's necessary only to ask whether a type implements an interface, without
actually using the interface itself, perhaps as part of an error check, use the blank
identifier to ignore the type-asserted value:


```
if _, ok := val.(json.Marshaler); ok {
    fmt.Printf("value %v of type %T implements json.Marshaler\n", val, val)
}
```


One place this situation arises is when it is necessary to guarantee within the package implementing the type that
it actually satisfies the interface.
If a type—for example,
```<a href="/pkg/encoding/json/#RawMessage">json.RawMessage</a>```—needs
a custom JSON representation, it should implement
```json.Marshaler```, but there are no static conversions that would
cause the compiler to verify this automatically.
If the type inadvertently fails to satisfy the interface, the JSON encoder will still work,
but will not use the custom implementation.
To guarantee that the implementation is correct,
a global declaration using the blank identifier can be used in the package:

```
var _ json.Marshaler = (*RawMessage)(nil)
```

In this declaration, the assignment involving a conversion of a
```*RawMessage``` to a ```Marshaler```
requires that ```*RawMessage``` implements ```Marshaler```,
and that property will be checked at compile time.
Should the ```json.Marshaler``` interface change, this package
will no longer compile and we will be on notice that it needs to be updated.



The appearance of the blank identifier in this construct indicates that
the declaration exists only for the type checking,
not to create a variable.
Don't do this for every type that satisfies an interface, though.
By convention, such declarations are only used
when there are no static conversions already present in the code,
which is a rare event.



<h2 id="embedding">Embedding</h2>


Go does not provide the typical, type-driven notion of subclassing,
but it does have the ability to &ldquo;borrow&rdquo; pieces of an
implementation by <em>embedding</em> types within a struct or
interface.


Interface embedding is very simple.
We've mentioned the ```io.Reader``` and ```io.Writer``` interfaces before;
here are their definitions.

```
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

The ```io``` package also exports several other interfaces
that specify objects that can implement several such methods.
For instance, there is ```io.ReadWriter```, an interface
containing both ```Read``` and ```Write```.
We could specify ```io.ReadWriter``` by listing the
two methods explicitly, but it's easier and more evocative
to embed the two interfaces to form the new one, like this:

```
// ReadWriter is the interface that combines the Reader and Writer interfaces.
type ReadWriter interface {
    Reader
    Writer
}
```

This says just what it looks like: A ```ReadWriter``` can do
what a ```Reader``` does <em>and</em> what a ```Writer```
does; it is a union of the embedded interfaces (which must be disjoint
sets of methods).
Only interfaces can be embedded within interfaces.


The same basic idea applies to structs, but with more far-reaching
implications.  The ```bufio``` package has two struct types,
```bufio.Reader``` and ```bufio.Writer```, each of
which of course implements the analogous interfaces from package
```io```.
And ```bufio``` also implements a buffered reader/writer,
which it does by combining a reader and a writer into one struct
using embedding: it lists the types within the struct
but does not give them field names.

```
// ReadWriter stores pointers to a Reader and a Writer.
// It implements io.ReadWriter.
type ReadWriter struct {
    *Reader  // *bufio.Reader
    *Writer  // *bufio.Writer
}
```

The embedded elements are pointers to structs and of course
must be initialized to point to valid structs before they
can be used.
The ```ReadWriter``` struct could be written as

```
type ReadWriter struct {
    reader *Reader
    writer *Writer
}
```

but then to promote the methods of the fields and to
satisfy the ```io``` interfaces, we would also need
to provide forwarding methods, like this:

```
func (rw *ReadWriter) Read(p []byte) (n int, err error) {
    return rw.reader.Read(p)
}
```

By embedding the structs directly, we avoid this bookkeeping.
The methods of embedded types come along for free, which means that ```bufio.ReadWriter```
not only has the methods of ```bufio.Reader``` and ```bufio.Writer```,
it also satisfies all three interfaces:
```io.Reader```,
```io.Writer```, and
```io.ReadWriter```.


There's an important way in which embedding differs from subclassing.  When we embed a type,
the methods of that type become methods of the outer type,
but when they are invoked the receiver of the method is the inner type, not the outer one.
In our example, when the ```Read``` method of a ```bufio.ReadWriter``` is
invoked, it has exactly the same effect as the forwarding method written out above;
the receiver is the ```reader``` field of the ```ReadWriter```, not the
```ReadWriter``` itself.


Embedding can also be a simple convenience.
This example shows an embedded field alongside a regular, named field.

```
type Job struct {
    Command string
    *log.Logger
}
```

The ```Job``` type now has the ```Log```, ```Logf```
and other
methods of ```*log.Logger```.  We could have given the ```Logger```
a field name, of course, but it's not necessary to do so.  And now, once
initialized, we can
log to the ```Job```:

```
job.Log("starting now...")
```

The ```Logger``` is a regular field of the ```Job``` struct,
so we can initialize it in the usual way inside the constructor for ```Job```, like this,

```
func NewJob(command string, logger *log.Logger) *Job {
    return &amp;Job{command, logger}
}
```

or with a composite literal,

```
job := &amp;Job{command, log.New(os.Stderr, "Job: ", log.Ldate)}
```

If we need to refer to an embedded field directly, the type name of the field,
ignoring the package qualifier, serves as a field name, as it did
in the ```Read``` method of our ```ReaderWriter``` struct.
Here, if we needed to access the
```*log.Logger``` of a ```Job``` variable ```job```,
we would write ```job.Logger```,
which would be useful if we wanted to refine the methods of ```Logger```.

```
func (job *Job) Logf(format string, args ...interface{}) {
    job.Logger.Logf("%q: %s", job.Command, fmt.Sprintf(format, args...))
}
```

Embedding types introduces the problem of name conflicts but the rules to resolve
them are simple.
First, a field or method ```X``` hides any other item ```X``` in a more deeply
nested part of the type.
If ```log.Logger``` contained a field or method called ```Command```, the ```Command``` field
of ```Job``` would dominate it.


Second, if the same name appears at the same nesting level, it is usually an error;
it would be erroneous to embed ```log.Logger``` if the ```Job``` struct
contained another field or method called ```Logger```.
However, if the duplicate name is never mentioned in the program outside the type definition, it is OK.
This qualification provides some protection against changes made to types embedded from outside; there
is no problem if a field is added that conflicts with another field in another subtype if neither field
is ever used.



<h2 id="concurrency">Concurrency</h2>

<h3 id="sharing">Share by communicating</h3>


Concurrent programming is a large topic and there is space only for some
Go-specific highlights here.


Concurrent programming in many environments is made difficult by the
subtleties required to implement correct access to shared variables.  Go encourages
a different approach in which shared values are passed around on channels
and, in fact, never actively shared by separate threads of execution.
Only one goroutine has access to the value at any given time.
Data races cannot occur, by design.
To encourage this way of thinking we have reduced it to a slogan:

<blockquote>
Do not communicate by sharing memory;
instead, share memory by communicating.
</blockquote>

This approach can be taken too far.  Reference counts may be best done
by putting a mutex around an integer variable, for instance.  But as a
high-level approach, using channels to control access makes it easier
to write clear, correct programs.


One way to think about this model is to consider a typical single-threaded
program running on one CPU. It has no need for synchronization primitives.
Now run another such instance; it too needs no synchronization.  Now let those
two communicate; if the communication is the synchronizer, there's still no need
for other synchronization.  Unix pipelines, for example, fit this model
perfectly.  Although Go's approach to concurrency originates in Hoare's
Communicating Sequential Processes (CSP),
it can also be seen as a type-safe generalization of Unix pipes.


<h3 id="goroutines">Goroutines</h3>


They're called <em>goroutines</em> because the existing
terms&mdash;threads, coroutines, processes, and so on&mdash;convey
inaccurate connotations.  A goroutine has a simple model: it is a
function executing concurrently with other goroutines in the same
address space.  It is lightweight, costing little more than the
allocation of stack space.
And the stacks start small, so they are cheap, and grow
by allocating (and freeing) heap storage as required.


Goroutines are multiplexed onto multiple OS threads so if one should
block, such as while waiting for I/O, others continue to run.  Their
design hides many of the complexities of thread creation and
management.


Prefix a function or method call with the ```go```
keyword to run the call in a new goroutine.
When the call completes, the goroutine
exits, silently.  (The effect is similar to the Unix shell's
```&amp;``` notation for running a command in the
background.)

```
go list.Sort()  // run list.Sort concurrently; don't wait for it.
```

A function literal can be handy in a goroutine invocation.

```
func Announce(message string, delay time.Duration) {
    go func() {
        time.Sleep(delay)
        fmt.Println(message)
    }()  // Note the parentheses - must call the function.
}
```

In Go, function literals are closures: the implementation makes
sure the variables referred to by the function survive as long as they are active.


These examples aren't too practical because the functions have no way of signaling
completion.  For that, we need channels.


<h3 id="channels">Channels</h3>


Like maps, channels are allocated with ```make```, and
the resulting value acts as a reference to an underlying data structure.
If an optional integer parameter is provided, it sets the buffer size for the channel.
The default is zero, for an unbuffered or synchronous channel.

```
ci := make(chan int)            // unbuffered channel of integers
cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files
```

Unbuffered channels combine communication&mdash;the exchange of a value&mdash;with
synchronization&mdash;guaranteeing that two calculations (goroutines) are in
a known state.


There are lots of nice idioms using channels.  Here's one to get us started.
In the previous section we launched a sort in the background. A channel
can allow the launching goroutine to wait for the sort to complete.

```
c := make(chan int)  // Allocate a channel.
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c &lt;- 1  // Send a signal; value does not matter.
}()
doSomethingForAWhile()
&lt;-c   // Wait for sort to finish; discard sent value.
```

Receivers always block until there is data to receive.
If the channel is unbuffered, the sender blocks until the receiver has
received the value.
If the channel has a buffer, the sender blocks only until the
value has been copied to the buffer; if the buffer is full, this
means waiting until some receiver has retrieved a value.


A buffered channel can be used like a semaphore, for instance to
limit throughput.  In this example, incoming requests are passed
to ```handle```, which sends a value into the channel, processes
the request, and then receives a value from the channel
to ready the &ldquo;semaphore&rdquo; for the next consumer.
The capacity of the channel buffer limits the number of
simultaneous calls to ```process```.

```
var sem = make(chan int, MaxOutstanding)

func handle(r *Request) {
    sem &lt;- 1    // Wait for active queue to drain.
    process(r)  // May take a long time.
    &lt;-sem       // Done; enable next request to run.
}

func Serve(queue chan *Request) {
    for {
        req := &lt;-queue
        go handle(req)  // Don't wait for handle to finish.
    }
}
```


Once ```MaxOutstanding``` handlers are executing ```process```,
any more will block trying to send into the filled channel buffer,
until one of the existing handlers finishes and receives from the buffer.



This design has a problem, though: ```Serve```
creates a new goroutine for
every incoming request, even though only ```MaxOutstanding```
of them can run at any moment.
As a result, the program can consume unlimited resources if the requests come in too fast.
We can address that deficiency by changing ```Serve``` to
gate the creation of the goroutines.
Here's an obvious solution, but beware it has a bug we'll fix subsequently:


```
func Serve(queue chan *Request) {
    for req := range queue {
        sem &lt;- 1
        go func() {
            process(req) // Buggy; see explanation below.
            &lt;-sem
        }()
    }
}```


The bug is that in a Go ```for``` loop, the loop variable
is reused for each iteration, so the ```req```
variable is shared across all goroutines.
That's not what we want.
We need to make sure that ```req``` is unique for each goroutine.
Here's one way to do that, passing the value of ```req``` as an argument
to the closure in the goroutine:


```
func Serve(queue chan *Request) {
    for req := range queue {
        sem &lt;- 1
        go func(req *Request) {
            process(req)
            &lt;-sem
        }(req)
    }
}```


Compare this version with the previous to see the difference in how
the closure is declared and run.
Another solution is just to create a new variable with the same
name, as in this example:


```
func Serve(queue chan *Request) {
    for req := range queue {
        req := req // Create new instance of req for the goroutine.
        sem &lt;- 1
        go func() {
            process(req)
            &lt;-sem
        }()
    }
}```


It may seem odd to write


```
req := req
```


but it's legal and idiomatic in Go to do this.
You get a fresh version of the variable with the same name, deliberately
shadowing the loop variable locally but unique to each goroutine.



Going back to the general problem of writing the server,
another approach that manages resources well is to start a fixed
number of ```handle``` goroutines all reading from the request
channel.
The number of goroutines limits the number of simultaneous
calls to ```process```.
This ```Serve``` function also accepts a channel on which
it will be told to exit; after launching the goroutines it blocks
receiving from that channel.


```
func handle(queue chan *Request) {
    for r := range queue {
        process(r)
    }
}

func Serve(clientRequests chan *Request, quit chan bool) {
    // Start handlers
    for i := 0; i &lt; MaxOutstanding; i++ {
        go handle(clientRequests)
    }
    &lt;-quit  // Wait to be told to exit.
}
```

<h3 id="chan_of_chan">Channels of channels</h3>

One of the most important properties of Go is that
a channel is a first-class value that can be allocated and passed
around like any other.  A common use of this property is
to implement safe, parallel demultiplexing.


In the example in the previous section, ```handle``` was
an idealized handler for a request but we didn't define the
type it was handling.  If that type includes a channel on which
to reply, each client can provide its own path for the answer.
Here's a schematic definition of type ```Request```.

```
type Request struct {
    args        []int
    f           func([]int) int
    resultChan  chan int
}
```

The client provides a function and its arguments, as well as
a channel inside the request object on which to receive the answer.

```
func sum(a []int) (s int) {
    for _, v := range a {
        s += v
    }
    return
}

request := &amp;Request{[]int{3, 4, 5}, sum, make(chan int)}
// Send request
clientRequests &lt;- request
// Wait for response.
fmt.Printf("answer: %d\n", &lt;-request.resultChan)
```

On the server side, the handler function is the only thing that changes.

```
func handle(queue chan *Request) {
    for req := range queue {
        req.resultChan &lt;- req.f(req.args)
    }
}
```

There's clearly a lot more to do to make it realistic, but this
code is a framework for a rate-limited, parallel, non-blocking RPC
system, and there's not a mutex in sight.


<h3 id="parallel">Parallelization</h3>

Another application of these ideas is to parallelize a calculation
across multiple CPU cores.  If the calculation can be broken into
separate pieces that can execute independently, it can be parallelized,
with a channel to signal when each piece completes.


Let's say we have an expensive operation to perform on a vector of items,
and that the value of the operation on each item is independent,
as in this idealized example.

```
type Vector []float64

// Apply the operation to v[i], v[i+1] ... up to v[n-1].
func (v Vector) DoSome(i, n int, u Vector, c chan int) {
    for ; i &lt; n; i++ {
        v[i] += u.Op(v[i])
    }
    c &lt;- 1    // signal that this piece is done
}
```

We launch the pieces independently in a loop, one per CPU.
They can complete in any order but it doesn't matter; we just
count the completion signals by draining the channel after
launching all the goroutines.

```
const numCPU = 4 // number of CPU cores

func (v Vector) DoAll(u Vector) {
    c := make(chan int, numCPU)  // Buffering optional but sensible.
    for i := 0; i &lt; numCPU; i++ {
        go v.DoSome(i*len(v)/numCPU, (i+1)*len(v)/numCPU, u, c)
    }
    // Drain the channel.
    for i := 0; i &lt; numCPU; i++ {
        &lt;-c    // wait for one task to complete
    }
    // All done.
}
```

Rather than create a constant value for numCPU, we can ask the runtime what
value is appropriate.
The function ```<a href="/pkg/runtime#NumCPU">runtime.NumCPU</a>```
returns the number of hardware CPU cores in the machine, so we could write

```
var numCPU = runtime.NumCPU()
```

There is also a function
```<a href="/pkg/runtime#GOMAXPROCS">runtime.GOMAXPROCS</a>```,
which reports (or sets)
the user-specified number of cores that a Go program can have running
simultaneously.
It defaults to the value of ```runtime.NumCPU``` but can be
overridden by setting the similarly named shell environment variable
or by calling the function with a positive number.  Calling it with
zero just queries the value.
Therefore if we want to honor the user's resource request, we should write

```
var numCPU = runtime.GOMAXPROCS(0)
```

Be sure not to confuse the ideas of concurrency—structuring a program
as independently executing components—and parallelism—executing
calculations in parallel for efficiency on multiple CPUs.
Although the concurrency features of Go can make some problems easy
to structure as parallel computations, Go is a concurrent language,
not a parallel one, and not all parallelization problems fit Go's model.
For a discussion of the distinction, see the talk cited in
<a href="//blog.golang.org/2013/01/concurrency-is-not-parallelism.html">this
blog post</a>.

<h3 id="leaky_buffer">A leaky buffer</h3>


The tools of concurrent programming can even make non-concurrent
ideas easier to express.  Here's an example abstracted from an RPC
package.  The client goroutine loops receiving data from some source,
perhaps a network.  To avoid allocating and freeing buffers, it keeps
a free list, and uses a buffered channel to represent it.  If the
channel is empty, a new buffer gets allocated.
Once the message buffer is ready, it's sent to the server on
```serverChan```.

```
var freeList = make(chan *Buffer, 100)
var serverChan = make(chan *Buffer)

func client() {
    for {
        var b *Buffer
        // Grab a buffer if available; allocate if not.
        select {
        case b = &lt;-freeList:
            // Got one; nothing more to do.
        default:
            // None free, so allocate a new one.
            b = new(Buffer)
        }
        load(b)              // Read next message from the net.
        serverChan &lt;- b      // Send to server.
    }
}
```

The server loop receives each message from the client, processes it,
and returns the buffer to the free list.

```
func server() {
    for {
        b := &lt;-serverChan    // Wait for work.
        process(b)
        // Reuse buffer if there's room.
        select {
        case freeList &lt;- b:
            // Buffer on free list; nothing more to do.
        default:
            // Free list full, just carry on.
        }
    }
}
```

The client attempts to retrieve a buffer from ```freeList```;
if none is available, it allocates a fresh one.
The server's send to ```freeList``` puts ```b``` back
on the free list unless the list is full, in which case the
buffer is dropped on the floor to be reclaimed by
the garbage collector.
(The ```default``` clauses in the ```select```
statements execute when no other case is ready,
meaning that the ```selects``` never block.)
This implementation builds a leaky bucket free list
in just a few lines, relying on the buffered channel and
the garbage collector for bookkeeping.


<h2 id="errors">Errors</h2>


Library routines must often return some sort of error indication to
the caller.
As mentioned earlier, Go's multivalue return makes it
easy to return a detailed error description alongside the normal
return value.
It is good style to use this feature to provide detailed error information.
For example, as we'll see, ```os.Open``` doesn't
just return a ```nil``` pointer on failure, it also returns an
error value that describes what went wrong.



By convention, errors have type ```error```,
a simple built-in interface.

```
type error interface {
    Error() string
}
```

A library writer is free to implement this interface with a
richer model under the covers, making it possible not only
to see the error but also to provide some context.
As mentioned, alongside the usual ```*os.File```
return value, ```os.Open``` also returns an
error value.
If the file is opened successfully, the error will be ```nil```,
but when there is a problem, it will hold an
```os.PathError```:

```
// PathError records an error and the operation and
// file path that caused it.
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Err error    // Returned by the system call.
}

func (e *PathError) Error() string {
    return e.Op + " " + e.Path + ": " + e.Err.Error()
}
```

```PathError```'s ```Error``` generates
a string like this:

```
open /etc/passwx: no such file or directory
```

Such an error, which includes the problematic file name, the
operation, and the operating system error it triggered, is useful even
if printed far from the call that caused it;
it is much more informative than the plain
"no such file or directory".



When feasible, error strings should identify their origin, such as by having
a prefix naming the operation or package that generated the error.  For example, in package
```image```, the string representation for a decoding error due to an
unknown format is "image: unknown format".



Callers that care about the precise error details can
use a type switch or a type assertion to look for specific
errors and extract details.  For ```PathErrors```
this might include examining the internal ```Err```
field for recoverable failures.


```
for try := 0; try &lt; 2; try++ {
    file, err = os.Create(filename)
    if err == nil {
        return
    }
    if e, ok := err.(*os.PathError); ok &amp;&amp; e.Err == syscall.ENOSPC {
        deleteTempFiles()  // Recover some space.
        continue
    }
    return
}
```


The second ```if``` statement here is another <a href="#interface_conversions">type assertion</a>.
If it fails, ```ok``` will be false, and ```e```
will be ```nil```.
If it succeeds,  ```ok``` will be true, which means the
error was of type ```*os.PathError```, and then so is ```e```,
which we can examine for more information about the error.


<h3 id="panic">Panic</h3>


The usual way to report an error to a caller is to return an
```error``` as an extra return value.  The canonical
```Read``` method is a well-known instance; it returns a byte
count and an ```error```.  But what if the error is
unrecoverable?  Sometimes the program simply cannot continue.



For this purpose, there is a built-in function ```panic```
that in effect creates a run-time error that will stop the program
(but see the next section).  The function takes a single argument
of arbitrary type&mdash;often a string&mdash;to be printed as the
program dies.  It's also a way to indicate that something impossible has
happened, such as exiting an infinite loop.



```
// A toy implementation of cube root using Newton's method.
func CubeRoot(x float64) float64 {
    z := x/3   // Arbitrary initial value
    for i := 0; i &lt; 1e6; i++ {
        prevz := z
        z -= (z*z*z-x) / (3*z*z)
        if veryClose(z, prevz) {
            return z
        }
    }
    // A million iterations has not converged; something is wrong.
    panic(fmt.Sprintf("CubeRoot(%g) did not converge", x))
}
```


This is only an example but real library functions should
avoid ```panic```.  If the problem can be masked or worked
around, it's always better to let things continue to run rather
than taking down the whole program.  One possible counterexample
is during initialization: if the library truly cannot set itself up,
it might be reasonable to panic, so to speak.


```
var user = os.Getenv("USER")

func init() {
    if user == "" {
        panic("no value for $USER")
    }
}
```

<h3 id="recover">Recover</h3>


When ```panic``` is called, including implicitly for run-time
errors such as indexing a slice out of bounds or failing a type
assertion, it immediately stops execution of the current function
and begins unwinding the stack of the goroutine, running any deferred
functions along the way.  If that unwinding reaches the top of the
goroutine's stack, the program dies.  However, it is possible to
use the built-in function ```recover``` to regain control
of the goroutine and resume normal execution.



A call to ```recover``` stops the unwinding and returns the
argument passed to ```panic```.  Because the only code that
runs while unwinding is inside deferred functions, ```recover```
is only useful inside deferred functions.



One application of ```recover``` is to shut down a failing goroutine
inside a server without killing the other executing goroutines.


```
func server(workChan &lt;-chan *Work) {
    for work := range workChan {
        go safelyDo(work)
    }
}

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}
```


In this example, if ```do(work)``` panics, the result will be
logged and the goroutine will exit cleanly without disturbing the
others.  There's no need to do anything else in the deferred closure;
calling ```recover``` handles the condition completely.



Because ```recover``` always returns ```nil``` unless called directly
from a deferred function, deferred code can call library routines that themselves
use ```panic``` and ```recover``` without failing.  As an example,
the deferred function in ```safelyDo``` might call a logging function before
calling ```recover```, and that logging code would run unaffected
by the panicking state.



With our recovery pattern in place, the ```do```
function (and anything it calls) can get out of any bad situation
cleanly by calling ```panic```.  We can use that idea to
simplify error handling in complex software.  Let's look at an
idealized version of a ```regexp``` package, which reports
parsing errors by calling ```panic``` with a local
error type.  Here's the definition of ```Error```,
an ```error``` method, and the ```Compile``` function.


```
// Error is the type of a parse error; it satisfies the error interface.
type Error string
func (e Error) Error() string {
    return string(e)
}

// error is a method of *Regexp that reports parsing errors by
// panicking with an Error.
func (regexp *Regexp) error(err string) {
    panic(Error(err))
}

// Compile returns a parsed representation of the regular expression.
func Compile(str string) (regexp *Regexp, err error) {
    regexp = new(Regexp)
    // doParse will panic if there is a parse error.
    defer func() {
        if e := recover(); e != nil {
            regexp = nil    // Clear return value.
            err = e.(Error) // Will re-panic if not a parse error.
        }
    }()
    return regexp.doParse(str), nil
}
```


If ```doParse``` panics, the recovery block will set the
return value to ```nil```&mdash;deferred functions can modify
named return values.  It will then check, in the assignment
to ```err```, that the problem was a parse error by asserting
that it has the local type ```Error```.
If it does not, the type assertion will fail, causing a run-time error
that continues the stack unwinding as though nothing had interrupted
it.
This check means that if something unexpected happens, such
as an index out of bounds, the code will fail even though we
are using ```panic``` and ```recover``` to handle
parse errors.



With error handling in place, the ```error``` method (because it's a
method bound to a type, it's fine, even natural, for it to have the same name
as the builtin ```error``` type)
makes it easy to report parse errors without worrying about unwinding
the parse stack by hand:


```
if pos == 0 {
    re.error("'*' illegal at start of expression")
}
```


Useful though this pattern is, it should be used only within a package.
```Parse``` turns its internal ```panic``` calls into
```error``` values; it does not expose ```panics```
to its client.  That is a good rule to follow.



By the way, this re-panic idiom changes the panic value if an actual
error occurs.  However, both the original and new failures will be
presented in the crash report, so the root cause of the problem will
still be visible.  Thus this simple re-panic approach is usually
sufficient&mdash;it's a crash after all&mdash;but if you want to
display only the original value, you can write a little more code to
filter unexpected problems and re-panic with the original error.
That's left as an exercise for the reader.



<h2 id="web_server">A web server</h2>


Let's finish with a complete Go program, a web server.
This one is actually a kind of web re-server.
Google provides a service at
<a href="http://chart.apis.google.com">http://chart.apis.google.com</a>
that does automatic formatting of data into charts and graphs.
It's hard to use interactively, though,
because you need to put the data into the URL as a query.
The program here provides a nicer interface to one form of data: given a short piece of text,
it calls on the chart server to produce a QR code, a matrix of boxes that encode the
text.
That image can be grabbed with your cell phone's camera and interpreted as,
for instance, a URL, saving you typing the URL into the phone's tiny keyboard.


Here's the complete program.
An explanation follows.

{{code "/doc/progs/eff_qr.go" `/package/` `$`}}

The pieces up to ```main``` should be easy to follow.
The one flag sets a default HTTP port for our server.  The template
variable ```templ``` is where the fun happens. It builds an HTML template
that will be executed by the server to display the page; more about
that in a moment.


The ```main``` function parses the flags and, using the mechanism
we talked about above, binds the function ```QR``` to the root path
for the server.  Then ```http.ListenAndServe``` is called to start the
server; it blocks while the server runs.


```QR``` just receives the request, which contains form data, and
executes the template on the data in the form value named ```s```.


The template package ```html/template``` is powerful;
this program just touches on its capabilities.
In essence, it rewrites a piece of HTML text on the fly by substituting elements derived
from data items passed to ```templ.Execute```, in this case the
form value.
Within the template text (```templateStr```),
double-brace-delimited pieces denote template actions.
The piece from ```{{html "{{if .}}"}}```
to ```{{html "{{end}}"}}``` executes only if the value of the current data item, called ```.``` (dot),
is non-empty.
That is, when the string is empty, this piece of the template is suppressed.


The two snippets ```{{html "{{.}}"}}``` say to show the data presented to
the template—the query string—on the web page.
The HTML template package automatically provides appropriate escaping so the
text is safe to display.


The rest of the template string is just the HTML to show when the page loads.
If this is too quick an explanation, see the <a href="/pkg/html/template/">documentation</a>
for the template package for a more thorough discussion.


And there you have it: a useful web server in a few lines of code plus some
data-driven HTML text.
Go is powerful enough to make a lot happen in a few lines.
