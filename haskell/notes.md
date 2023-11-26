# Haskell Notes

## Installation

GHCup is the latest installer for Haskell utilities - use this to manage Haskell tooling. 
Cabal is the library that handles making packages available to GHC, versioning according to the PvP, and general package management.

There is a REPL for Haskell: ghci.

### The REPL

- You can include multi-line code by surrounding it with `:{` and `:}`
- Import Haskell source files using the `:load` or `:l` command.
- Import modules `ghci> import Data.Bits`
- Get the type of an expression with `:type <yourtype>`

## Resources

- [Neovim server config docs][nvim-server-configs]
- [Cabal docs][cabal-docs]
- [Haskell Wiki][wiki]

[nvim-server-configs]: https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md
[cabal-docs]: https://cabal.readthedocs.io/en/3.4/getting-started.html
[wiki]: https://wiki.haskell.org/

## Directory Structure

```
app/             -- Root-dir
  src/           -- For keeping the sourcecode
    Main.lhs     -- The main-module
    App/         -- Use hierarchical modules
      ...
      Win32/     -- For system dependent stuff
      Unix/
    cbits/       -- For C code to be linked to the haskell program
  testsuite/     -- Contains the testing stuff
    runtests.sh  -- Will run all tests
    tests/       -- For unit-testing and checking
      App/       -- Clone the module hierarchy, so that there is one 
                    testfile per sourcefile
    benchmarks/   -- For testing performance
  doc/           -- Contains the manual, and other documentation
    examples/    -- Example inputs for the program
    dev/         -- Information for new developers about the project, 
                    and eg. related literature
  util/          -- Auxiliary scripts for various tasks
  dist/          -- Directory containing what end-users should get
    build/       -- Contains binary files, created by cabal
    doc/         -- The haddock documentation goes here, created by cabal
    resources/   -- Images, soundfiles and other non-source stuff
                    used by the program
  _DARCS/        
  README         -- Textfile with short introduction of the project
  INSTALL        -- Textfile describing how to build and install
  TODO           -- Textfile describing things that ought to be done
  AUTHORS        -- Textfile containing info on who does and has done 
                    what in this project, and their contact info
  LICENSE        -- Textfile describing licensing terms for this project
  app.cabal      -- Project-description-file for cabal
  Setup.hs       -- Program for running cabal commands
```

## Basics

### Functions

In Haskell, functions are *first class*, so they behave exactly like primitive types and can be used in data structures, as arguments, etc.

Everything is a function. `*` is a function that takes two numbers and multiplies them. 
*Infix* function -> `1 * 2`
- a function can be called as an infix if it has two parameters... So `div 21 10` becomes `92 \`div\` 10`
*Prefix* function -> min 8
*Definition* -> hello
- no parameters

- functions are called by writing the function name, a space, then the parameters, separated by spaces.
- parentheses are only used when doing g(f(x)) function calls
- function order doesn't matter in Haskell
- ' doesn't have special meaning in Haskell syntax. Often used for: strict functions (not lazy) or modified function or variable
- functions can't start with uppercase letters
- function names must be unique

#### Anonymous Functions 

Anonymous functions are also known as *lambda functions*, named after lambda calculus, the heart of all functional programming languages.

`\<argument> -> expression` where the '\' marks the head of the lambda function and the '->' marks the body.

So, when we write:
```haskell
el :: String -> String -> String
el tag content =
  "<" <> tag <> ">" <> content <> "</" <> tag <> ">"
```
Haskell actually translates this under the hood to:
```haskell
el :: String -> (String -> String)
el = \tag -> \content ->
  "<" <> tag <> ">" <> content <> "</" <> tag <> ">"
```

### Indentation

Haskell uses indentation to decide what should be grouped together. 
Rules:
- code that is part of some expression gets indented further than the beginning of the expression.
- always choose a specific amount of spaces for indentation. Always use spaces over tabs. 
- when in doubt, drop line and indent once.

### Lists

e.g.
`let lostNums = [2, 4, 6, 8]`

- "hello" is syntactic sugar for ['h', 'e', 'l', 'l', 'o']

#### Working with lists

- `++` join two lists; not great for huge lists
- inserting with `:` is instantaneous. e.g. `5:[1, 2, 3, 4, 5] -> [5, 1, 2, 3, 4, 5]`

- `head [5, 4, 3, 2, 1]` => 5
- `tail [5, 4, 3, 2, 1]` => [4, 3, 2, 1]
- `last [5, 4, 3, 2, 1]` => 1
- `init [5, 4, 3, 2, 1]` => [5, 4, 3, 2]
- `length [5, 4, 3, 2, 1]` => 5
- `null [1, 2, 3]` => False || `null []` => True
- `reverse [5, 4, 3, 2, 1]` => [1, 2, 3, 4, 5]

##### take

Takes a number and a list. It extracts n number of elements from the beginning of the list.
- `take 3 [5, 4, 3, 2, 1]` => [5, 4, 3]
- `take 1 [3, 9, 3]` => [3]
- `take 5 [1, 2]` => [1, 2]
- `take 5 [1, 2]` => [1, 2]
```haskell
ghci> head []  
*** Exception: Prelude.head: empty list  
```

##### map

Using map you can apply a function to each element in a list.
```haskell
map :: (a -> b) -> [a] -> [b]`

map escapeChar ['<', 'h', '1', '>'] == ["&lt;",  "h", "1", "&gt;"]
```

this returns a `[String]` which we can flatten to a `String` with `concat`.

`concat :: [[a]] -> [a]`

## Type Signatures
Haskell is **statically** typed, so every expression has a type and the types
are checked at compile time.

Haskell also has type inference, so we don't *need* to specify the type of
expressions. Having the types declared explicitly is useful though as a form of documentation. It shows that was intended, and what was written. 

It is recommended to add type signatures to all *top-level functions*

### Multiple Arguments

The reason you see weird type signatures on some functions i.e. 
```haskell
makeHtml :: String -> String -> String
makeHtml title content = html_ (head_ (title_ title) <> body_ content)
```

Is because **every function in Haskell takes only one argument and returns
exactly one value**
> 
Note that `->` is right associative, so when we write `makeHtml :: String -> String -> String` Haskell parses it as `makeHtml :: String -> (String -> String)`.
What's interesting about this is you can actually pass in a single arg to `makeHtml` and it will run it as the rightmost arg (I'm pretty sure, should test on >2 args). This is known as *partial application*

## Custom Types

`newtype` defines a new, distinct type for an existing set of values. It's very similar to `data`. `newtype` can be subbed for `data`, but the inverse isn't true. `data` can only be replaced with `newtype` if the type had exactly one constructor with exactly one field inside it

e.g.
```haskell
-- this is *not* allowed:
-- newtype Pair a b = Pair { pairFst :: a, pairSnd :: b }
-- but this is:
data Pair a b = Pair { pairFst :: a, pairSnd :: b }
newtype NPair a b = NPair (a, b)
```

You would use `newtype` as a more minimalistic way of setting data types. With
`data`, which allows for more than one constructor, it requires an extra case
of pattern matching at runtime to account for the extra constructors. In short,
`data` types are *lifted* and not as *isomorphic* as the types they extend.

Note: both type name and constructor name don't need to match, though they
often do, must be uppercase.

Using custom types can prevent the wrong kind of information being passed in to
a function. **newtypes provide us with type safety with no performance
penalty!**

### Appending Structures

```haskell
append_ :: Elem -> Elem -> Elem
append_ (Elem a) (Elem b) = Elem (a <> b)
```
Despite `a` and `b` being declared as `Elem` types as arguments, you can't use the compose operator on them without explicitly defining their type as the complex type `Elem`.

### Parametric Polymorphism

Note: types that start with lowercase are **type variables**. A type variable
can be any type; this is known as *parametric polymorphism* (also known as
generics)

The type variables in a function signature must match.

### Type Classes AKA overloading

## Neovim Config

To use Haskell in Neovim, you can use the `hls` "Haskell Language Server".
[here][nvim-server-configs]'s a link to the server configuration docs. 

## Projects

When you're starting a project, you'll need to initialize a package with `$ cabal init --cabal-version=2.4 --license=NONE -p myfirstapp`

Note: 
> It is common to use a slightly older cabal-version, to strike a compromise between feature availability and backward compatibility.

`<>` has right *fixity*, so it puts the parentheses on the right of the operator.
e.g.

`"<html><body>" <> content <> "</body></html>"`
is the same as
`"<html><body>" <> (content <> "</body></html>")`

All of the operators have varying levels of precedence. There exist operators
with the same precedence but different fixities.

### Cabal

What is Cabal?
> Cabal is a system for building and packaging Haskell libraries and programs.
> It allows authors to build their programs in a portable way

`cabal init` - initializes a new project and creates all the files needed for an executable
`cabal run` - builds and runs the executable
`cabal update` - refreshes package index

### File Types

`hello.o` - object file
`hello.hi` - haskell interface file
`hello` - executable file file

These file types will also get generated when you run `ghc hello.hs` which
compiles your program.

## Patterns

There are two ways to pattern match:
1. Case expressions
2. Part of a function definition

### Case Expressions

```haskell
getStructureString :: Structure -> String
getStructureString struct =
  case struct of
    Structure str -> str
```

### Function Definition

```haskell
getStructureString :: Structure -> String
getStructureString (Structure str) = str
```

## Chaining Functions

`.` AKA compose
```haskell
(.) :: (b -> c) -> (a -> b) -> a -> c
(.) f g x = f (g x)
```

To break down this function signature further, `f` is `(b -> c)`, `g` is `(a ->
b)`, and `x` is `a`, and the final output is `c`.

### Embedded Domain Specific Languages

An embedded domain specific language is a little language which is embedded
inside another programming language, making a program written in the EDSL a
valid program in the language it was written in.

In Haskell we frequently create and use EDSLs to express domain specific logic.
We have EDSLs for concurrency, command-line options parsing, JSON and HTML,
creating build systems, writing tests, and many more.

e.g.
make - for defining build systems
DOT - for defining graphs
Sed - for defining text transformations
CSS - for defining styling

Pros:
- solve specific problems in a concise way
- get the full power of the host language for our domain logic
    - e.g. syntax highlighting + tooling

Cons:
- need to adhere to the rules of the language we embed in

This is where some langs have come to support meta-programming with macros, etc
to extend the language. Haskell has this, but because it is so concise, you
generally don't need it for EDSLs.

The blog generator we built is an HTML EDSL. 

## Comparing Types

If the types are concrete (not a type variable), then we can do a simple type
check. If not, we try to match their inputs and outputs.  If they match, then
the two types match.

If only one of the types is a type variable, then we treat the match like an equation. The next time we see that type variable, we replace it with its match in the equation. Kind of like assigning a type variable with a value

## Type or newtype

type acts as an alias to an existing type; they are interchangeable. newtype
creates a new type definition that allows us to distinguish between the original
type (e.g. String) and our custom type.

## Modules
- same name as the source file
- start with a capital letter
- sub-directories are also part of the name; denoted with a "."

```haskell
module <module-name>
  ( <export-list>
  )
  where
  
module Html
  ( Html
  , Title
  , Elem
  , html_
  , p_
  , append_
  , render_
  )
  where
```

You don't need to export constructors for new types, just the type itself is
fine (see Html above).That limits the user from creating their own `Elem` by
writing `Elem "hello"`.

You also don't need to export internal functions (tag_, getString)

## Suffixing with _

You may want to suffix with _ when you're defining function names that might
clash with the standard library.

## Linked Lists

Strings are linked lists in Haskell, because of this they can be quite inefficient in a lot of situations. As a result, the author of "Learn Haskell by building a blog generator" recommends using the `Text` type in place of strings most of the time.

# Questions

- is there hoisting in Haskell?
    - apparently the order in which we declare the bindings doesn't matter
- does Haskell compile without main?

- see appending structures; I don't fully understand why that works to get the underlying strings joined...

# Errors

> Failed to find a HLS version for GHC 9.2.5

`ghcup compile hls -g master --ghc 9.2.5`

If you run into trouble with this try:
- `cabal update` if the `cabal v2-install` fails

You'll also need to modify your `.cabal` file to make sure it points to the right entry point, or you'll get a "multi-cradle error".
