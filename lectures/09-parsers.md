---
title: Parser Combinators
date: 2020-05-22
headerImg: books.jpg
---


## Parsers

A _parser_ is a function that

- converts _unstructured_ data (e.g. `String`, array of `Byte`,...)
- into _structured_ data (e.g. JSON object, Markdown, AST...)

```haskell
type Parser = String -> StructuredObject
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Every large software system contains a Parser


| **System**    | **Parses**            |
|:--------------|:----------------------|
| Compiler      | Source code           |
| Shell Scripts | Command-line options  |
| Database      | SQL queries           |
| Web Browser   | HTML, CSS, JS, ...    |
| Games         | Level descriptors     |
| Routers       | Packets               |


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## How to build Parsers?

Two standard methods

### Regular Expressions

- Doesn't really scale beyond simple things
- No nesting, recursion

### Parser Generators

1. Specify *grammar* via rules

```haskell
Expr : Var            { EVar $1       }
     | Num            { ENum $1       }
     | Expr Op Expr   { EBin $1 $2 $3 }
     | '(' Expr ')'   { $2            }
     ;
```

2. Tools like `yacc`, `bison`, `antlr`, `happy`
  - convert *grammar* into *executable function*

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Grammars Don't Compose!

If we have *two* kinds of structured objects `Thingy` and `Whatsit`.

```haskell
Thingy : rule 	{ action }
;

Whatsit : rule  { action }
;
```

To parse *sequences* of `Thingy` and `Whatsit` we must *repeat ourselves*:

```haskell
Thingies : Thingy Thingies  { ... }
           EmptyThingy      { ... }
;

Whatsits : Whatsit Whatsits { ... }
           EmptyWhatsit     { ... }
;
```

No way to:

- Define a generic parser for sequences
- Reuse this generic parser for `Thingy` and `Whatsit`


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A New Hope: Parsers as Functions

Lets think of parsers directly **as functions** that

- **Take** as input a `String`
- **Convert** a part of the input into a `StructuredObject`
- Return the **remainder** unconsumed to be parsed _later_

```haskell
data Parser a = P (String -> (a, String))
```

A `Parser a`

- Converts a _prefix_ of a `String`
- Into a _structured_ object of type `a` and
- Returns the _suffix_ `String` unchanged

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parsers Can Produce Many Results

Sometimes we want to parse a `String` like

```haskell
"2 + 3 + 4"
```

into a **list** of possible results

```haskell
[(Plus (Plus 2 3) 4),   Plus 2 (Plus 3 4)]
```

So we generalize the `Parser` type to

```haskell
data Parser a = P (String -> [(a, String)])
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Running a Parser

Given the definition

```haskell
data Parser a = P (String -> [(a, String)])
```

how should we implement?

```haskell
runParser :: Parser a -> String -> [(a, String)]
runParser p s = ???
```

<br>
<br>
<br>
<br>
<br>
<br>

```haskell
runParser :: Parser a -> String -> [(a, String)]
runParser (P f) s = f s
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

Given the definition

```haskell
data Parser a = P (String -> [(a, String)])
```

Which of the following is a valid `oneChar :: Parser Char`

that returns the **first** `Char` from a string (if one exists)

```haskell
-- A
oneChar = P (\s -> head s)

-- B
oneChar = P (\s -> case s of
                      []   -> [('', s)]
                      c:cs -> [c, cs])

-- C
oneChar = P (\s -> (head s, tail s))

-- D
oneChar = P (\s -> [(head s, tail s)])

-- E
oneChar = P (\s -> case s of
                      [] -> []
                      c:cs -> [(c, cs)])
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Lets Run Our First Parser!

```haskell
>>> runParser oneChar "hey!"
[('h', "ey")]

>>> runParser oneChar "yippee"
[('y', "ippee")]

>>> runParser oneChar ""
[]
```

**Failure** to parse means result is an **empty** list!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE

Your turn: Write a parser to grab **first two chars**

```haskell
twoChar :: Parser (Char, Char)
twoChar = P (\cs -> ???)
```

When you are done, we should get

```haskell
>>> runParser twoChar "hey!"
[(('h', 'e'), "y!")]

>>> runParser twoChar ""
[]

>>> runParser twoChar "h"
[]
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Composing Parsers

We can write `twoChars` from scratch like so:

```haskell
twoChar :: Parser (Char, Char)
twoChar  = P (\cs -> case cs of
                       c1:c2:cs' -> [((c1, c2), cs')]
                       _         -> [])
```

But this is **sad**: would be much better to reuse `oneChar`!

```haskell
twoChar :: Parser (Char, Char)
twoChar = pairP oneChar oneChar
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## QUIZ

```haskell
twoChar :: Parser (Char, Char)
twoChar = pairP oneChar oneChar
```

What must the type of `pairP` be?

**A.** `Parser (Char, Char)`

**B.** `Parser Char -> Parser (Char, Char)`

**C.** `Parser a -> Parser a -> Parser (a, a)`

**D.** `Parser a -> Parser b -> Parser (a, b)`

**E.** `Parser a -> Parser (a, a)`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A Pair Combinator

Lets implement `pairP`!

```haskell
pairP :: Parser a -> Parser b -> Parser (a, b)
pairP aP bP = ???
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

High-level idea:

- Run `aP` on the input string
- *For each* result `(a, s')` of `aP`, run `bP` on `s'`
- *For each* result `(b, s'')` of `bP`, return `((a, b), s'')`

<br>
<br>
<br>
<br>

```haskell
-- | We have just seen this "for-each" function in the previous lecture!
forEach :: [a] -> (a -> [b]) -> [b]
forEach xs f = concatMap f xs

pairP :: Parser a -> Parser b -> Parser (a, b)
pairP aP bP = P (\s -> forEach (runParser aP s)  (\(a, s') ->
                       forEach (runParser bP s') (\(b, s'') ->
                       [((a, b), s'')])
                ))
```

<br>
<br>
<br>
<br>

This works, but is was a bit of a pain to write!

Does this code look familiar?

<br>
<br>
<br>
<br>

It's like doing *mutable state* + *non-determinism* the hard way!

  - i.e. without monads

<br>
<br>
<br>
<br>

Also, take a second look at the `Parser` type:


```haskell
data Parser a = P (String -> [(a, String)])

data State s a   = S (s -> (a, s))
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parser is a Monad!

Like `State` and `[]`, [`Parser` is a monad!][2]

We need to implement two functions

```haskell
returnP :: a -> Parser a

bindP   :: Parser a -> (a -> Parser b) -> Parser b
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

Which of the following is a valid implementation of `returnP`

```haskell
data Parser a = P (String -> [(a, String)])

returnP   :: a -> Parser a

returnP a = P (\s -> [])          -- A

returnP a = P (\s -> [(a, s)])    -- B

returnP a = P (\s -> (a, s))      -- C

returnP a = P (\s -> [(a, "")])   -- D

returnP a = P (\s -> [(s, a)])    -- E
```

**HINT:** what did `return` do for `State` and `[]`?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Bind for Parsers

Next, lets implement `bindP`

```haskell
bindP :: Parser a -> (a -> Parser b) -> Parser b
bindP aP fbP = ???
```

High-level idea:

- Run `aP` on the input string
- *For each* result `(a, s')` of `aP`, run `bp = fbP a` on `s'`
- *For each* result `(b, s'')` of `bP`, return `(b, s'')`

In other words, this is just a *generalization* of `pairP`!

![](/static/img/bind-0.png)

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


```haskell
bindP :: Parser a -> (a -> Parser b) -> Parser b
bindP aP fbP = P (\s ->
  forEach (runParser aP s) (\(a, s') ->
    forEach (runParser (fbP a) s') (\(b, s'') ->
      [(b, s'')]
    )
  )
)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## The Parser Monad

We can now make `Parser` an instance of `Monad`

```haskell
instance Monad Parser where
  (>>=)  = bindP
  return = returnP
```

![](https://www.artgroup.com/assets/img/products/WDC44013)

And now, let the *wild rumpus begin!*

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parser Combinators

Lets write lots of *high-level* operators to **combine** parsers!

Here's a cleaned up `pairP`

```haskell
pairP :: Parser a -> Parser b -> Parser (a, b)
pairP aP bP = do
  a <- aP
  b <- bP
  return (a, b)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Failures are the Pillars of Success!

Surprisingly useful, always _fails_

- i.e. returns `[]` no successful parses

```haskell
failP :: Parser a
failP = P (\_ -> [])
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

Consider the parser

```haskell
satP :: (Char -> Bool) -> Parser Char
satP p = do
  c <- oneChar
  if p c then return c else failP
```

What is the value of

```haskell
quiz1 = runParser (satP (\c -> c == 'h')) "hello"
quiz2 = runParser (satP (\c -> c == 'h')) "yellow"
```

|       | `quiz1`           | `quiz2`              |
|------:|------------------:|---------------------:|
| **A** | `[]`              | `[]`                 |
| **B** | `[('h', "ello")]` | `[('y', "ellow")]`   |
| **C** | `[('h', "ello")]` | `[]`                 |
| **D** | `[]`              | `[('y', "ellow")]`   |


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parsing Alphabets and Numerics

We can now use `satP` to write

```haskell
-- parse ONLY the Char c
charP :: Char -> Parser Char
charP c = satP (\c' -> c == c')

-- parse ANY ALPHABET
alphaCharP :: Parser Char
alphaCharP = satP isAlpha

-- parse ANY NUMERIC DIGIT
digitCharP :: Parser Char
digitCharP = satP isDigit
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

We can parse a single `Int` digit

```haskell
digitP :: Parser Int
digitP = do
  c <- digitCharP     -- parse the Char c
  return (read [c])   -- convert Char to Int
```

What is the result of

```haskell
quiz1 = runParser digitP "92"
quiz2 = runParser digitP "cat"
```

|       | `quiz1`           | `quiz2`          |
|------:|------------------:|-----------------:|
| **A** | `[]`              | `[]`             |
| **B** | `[('9', "2")]`    | `[('c', "at")]`  |
| **C** | `[(9, "2")]`      | `[]`             |
| **D** | `[]`              | `[('c', "at")]`  |

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A Choice Combinator

Lets write a combinator `p1 <|> p2` that

- returns the results of `p1`

**or, else** _if those are empty_

- returns the results of `p2`


E.g. `<|>` lets us build a parser that produces an alphabet _OR_ a numeric character

```haskell
alphaNumCharP :: Parser Char
alphaNumCharP = alphaCharP <|> digitCharP
```

Which should produce

```haskell
>>> runParser alphaNumCharP "cat"
[('c', "at")]

>>> runParser alphaNumCharP "2cat"
[('2', "cat")]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

```haskell
(<|>) :: Parser a -> Parser a -> Parser a
p1 <|> p2 = ??? -- produce results of `p1` if non-empty
                -- OR-ELSE results of `p2`
```

Which of the following implements `(<|>)`?

```haskell
do -- (A)
  r1s <- p1
  r2s <- p2
  return (r1s ++ r2s)

do -- (B)
  r1s <- p1
  case r1s of
    [] -> p2
    _  -> return r1s

-- (C)
P (\s -> runParser p1 s ++ runParser p2 s)

-- (D)
P (\s ->
  case runParser p1 s of
    []  -> runParser p2 s
    r1s -> r1s
  )
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A Simple Expression Parser

Let's write a parser `expr :: Parser Int` that *parses and evaluates* simple arithmetic expressions

  - an expression is `a + b`, `a - b`, `a * b`, `a / b`
  - where `a` and `b` are *single-digit* integers

```haskell
>>> runParser expr "8/2"
[(4,"")]

>>> runParser expr "8+2cat"
[(10,"cat")]

>>> runParser expr "8-2cat"
[(6,"cat")]

>>> runParser expr "8*2cat"
[(16,"cat")]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
-- 1. First, parse the operator
opP      :: Parser (Int -> Int -> Int)
opP      = plus <|> minus <|> times <|> divide
  where
    plus   = do { _ <- charP '+'; return (+) }
    minus  = do { _ <- charP '-'; return (-) }
    times  = do { _ <- charP '*'; return (*) }
    divide = do { _ <- charP '/'; return div }

-- 2. Now parse the expression!
expr :: Parser Int
expr = do x  <- digitP
          op <- opP
          y  <- digitP
          return (x `op` y)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

What will `quiz` evaluate to?

```haskell
expr :: Parser Int
expr = do x  <- digitP
          op <- opP
          y  <- digitP
          return (x `op` y)

quiz = runParser expr "10+2"
```

**A.** `[(12,"")]`

**B.** `[]`

**C.** `[(10, "+2")]`

**D.** `[(1, "0+2")]`

**E.** Run-time exception

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Next: Recursive Parsing

Its cool to parse individual `Char` ...

... but _way_ more interesting to parse recursive structures!

```haskell
"((2 + 10) * (7 - 4)) * (5 + 2)"
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE: Parsing Many Times

Often we want to _repeat_ parsing some object

- E.g. to parse an `Int` we need to parse a digit _many times_

Implement a function  `manyP` that:

  - applies a parser `p` as many times as it can
  - returns a list of the results

```haskell
-- | `manyP p` repeatedly runs `p` to return a list of [a]
manyP  :: Parser a -> Parser [a]
manyP = ???

>>> runParser (manyP digitCharP) "2022cat"
[("2022","cat")]

>>> runParser (manyP digitCharP) "cat"
[("","cat")]
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
-- | `manyP p` repeatedly runs `p` to return a list of [a]
manyP  :: Parser a -> Parser [a]
manyP p = m1 <|> m0
  where
    m0  = return []
    m1  = do { x <- p; xs <- manyP p; return (x:xs) }
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parsing at least one

Often we need to parse *at least one* repetition:

```haskell
manyOneP :: Parser a -> Parser [a]
manyOneP p = do { x <- p; xs <- manyP p; return (x:xs) }
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parsing Numbers

Now, we can write a parser for natural numbers:

```haskell
int :: Parser Int
int = do { xs <- manyOneP digitChar; return (read xs) }

>>> runParser int "2022cat"
[(2022,"cat")]

>>> runParser int "cat"
[] -- parser fails, as expected!
```
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Parsing Arithmetic Expressions

_Now_ we can build a proper calculator!

```haskell
expr ::  Parser Int
expr = binExp <|> int

binExp :: Parser Int
binExp = do
  x <- int
  o <- opP
  y <- expr
  return (x `o` y)
```

Works pretty well!

```haskell
>>> runParser expr "11+22+33"
[(66,"")]

>>> runParser expr "11+22-33"
[(0,"")]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## WARNING

The real `(<|>)` combinator in Parsec has different semantics:

It only runs `p2` if `p1` fails **without consuming any input**

- This makes parsers more efficient
- And helps with error reporting

To choose between objects that can have the same prefix, you need to use `try`:

```haskell
-- In Parsec
expr ::  Parser Int
expr = try binExp <|> int
```

Otherwise, `runParser expr "10"` would fail

  - because `binExp` consumes the `10`
  - so `int` is never tried

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

```haskell
expr ::  Parser Int
expr = binExp <|> int

binExp :: Parser Int
binExp = do
  x <- int
  o <- opP
  y <- expr
  return (x `o` y)
```

What does `quiz` evaluate to?

```haskell
quiz = runParser expr "10-5-5"
```

**A.** `[(0, "")]`

**B.** `[]`

**C.** `[(10, "")]`

**D.** `[(10, "-5-5")]`

**E.** `[(5, "-5")]`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Problem: Right-Associativity

```haskell
binExp :: Parser Int
binExp = do
  x <- int
  o <- opP
  y <- expr
  return (x `o` y)
```

`"10-5-5"` gets parsed as `10 - (5 - 5)` because

![](/static/img/parse-left.png)

The `expr` parser implicitly forces each operator to be **right associative**

- doesn't matter for `+`, `*`

- but is incorrect for `-`, `div`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Left-Associative Parsing

How can we make the parser *left associative*

- i.e. parse "10-5-5" as `(10 - 5) - 5` ?

Lets flip the order!

```haskell
binExp :: Parser Int
binExp = do
  x <- expr -- now expr is on the left
  o <- intOp
  y <- int
  return (x `o` y)
```

But ...

```haskell
>>> runParser expr "2+2"
...
```

Infinite loop! `expr --> binExp --> expr --> binExp --> ...`

- without _consuming_ any input :-(

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Instead of

```haskell
expr :== int "-" (int "-" (... "-" (int "-" int)))
```

we want

```haskell
expr :== (((int "-" int) "-" int) "-" ... "-" int)
```

Does this remind you of any standard computation patterns?

<br>
<br>
<br>
<br>
<br>

Fold right vs fold left!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Fold Left Parser

Lets implement `foldLP aP oP` as a combinator

  - `vP` parses a *single* `a` value
  - `oP` parses a *binary operator* `a -> a -> a`
  - `foldLP vP oP` parses and returns the result `((v1 o v2) o v3) o v4) o ... o vn)`

But how?

1. *grab* the first `v1` using `vP`

2. *continue* by
    - either trying `oP` then `v2` ... and recursively continue with `v1 o v2`
    - *or else* (no more `o`) just return `v1`

<br>
<br>
<br>
<br>

```haskell
foldLP :: Parser a -> Parser (a -> a -> a) -> Parser a
foldLP vP oP = do {v1 <- vP; continue v1}
  where
    continue v1 = do { o <- oP; v2 <- vP; continue (v1 `o` v2) }
               <|> return v1
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

Now we can define

```haskell
expr = foldLP int opP
```

Lets make sure it works!

```haskell
>>> runParser expr "10-5-5"
[(0,"")]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## QUIZ

```haskell
expr = foldLP int opP
```

What is the value of

```haskell
quiz1 = runParser expr "10*2+100"
quiz2 = runParser expr "100+10*2"
```

|       | `quiz1`           | `quiz2`              |
|------:|------------------:|---------------------:|
| **A** | `[]`              | `[]`                 |
| **B** | `[120, "")]`      | `[(120, "")]`        |
| **C** | `[120, "")]`      | `[(220, "")]`        |
| **D** | `[1020, "")]`     | `[(120, "")]`        |

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Operator precedence

Our parser treats all operators at the same **precendence level**!

- in reality, `*` and `/` should have higher precedence than `+` and `-`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Solution: Grammar Factoring

Any expression is a **sum-of-products**!

```haskell
  expr :== (((prod "+" prod) "+" prod) "+" ... "+" prod)
  prod :== (((atom "*" atom) "*" atom) "*" ... "*" atom)
  atom :== "(" expr ")" | int
```

Or in Haskell:

```haskell
expr = foldLP prod (plus <|> minus)
prod = foldLP atom (times <|> div)
atom = parensP expr <|> int
```

All that is left is to implement `parensP`!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE: Parsing Parenthetic Expressions

Implement the function `parensP`:

```haskell
-- | Parse what `p` parses, surrounded by parentheses:
parensP :: Parser a -> Parser a
parensP p = ???

>>> runParser (parensP int) "(10)"
[(10,"")]

>>> runParser (parensP int) "10"
[]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


Lets make sure it works!

```haskell
>>> runParser expr "10*2+100"
[(120,"")]

>>> runParser expr  "100+10*2"
[(120,"")]

>>> runParser expr  "(100+10)*2"
[(220,"")]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parser combinators

That was a taste of Parser Combinators

- Transferred from Haskell to [_many_ other languages][3].

Many libraries including [Parsec][3] used in your homework
  -  `oneOrMore` is called `chainl`

Read more about the *theory*
  - in these [recent][4] [papers][5]

Read more about the *practice*
  - in this recent post that I like [JSON parsing from scratch][6]

[2]: http://homepages.inf.ed.ac.uk/wadler/papers/marktoberdorf/baastad.pdf
[3]: http://www.haskell.org/haskellwiki/Parsec
[4]: http://www.cse.chalmers.se/~nad/publications/danielsson-parser-combinators.html
[5]: http://portal.acm.org/citation.cfm?doid=1706299.1706347
[6]: https://abhinavsarkar.net/posts/json-parsing-from-scratch-in-haskell/
