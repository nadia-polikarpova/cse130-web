---
title: Lexing and Parsing
date: 2018-05-16
headerImg: books.jpg
---

## Plan for this week


**Last week:**

- How do we *evaluate* a program given its AST?

```haskell
eval :: Env -> Expr -> Value
```

**This week:**

- How do we *convert* program text into an AST?

```haskell
parse :: String -> Expr
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

## Example: calculator with variables

AST representation:

```haskell
data Aexpr 
  = AConst  Int
  | AVar    Id
  | APlus   Aexpr Aexpr
  | AMinus  Aexpr Aexpr
  | AMul    Aexpr Aexpr
```

<br>

Evaluator:

```haskell
eval :: Env -> Aexpr -> Value
...
```

<br>

Using the evaluator:

```haskell
λ> eval [] (APlus (AConst 2) (AConst 6))
8

λ> eval [("x", 16), ("y", 10)] (AMinus (AVar "x") (AVar "y"))
6

λ> eval [("x", 16), ("y", 10)] (AMinus (AVar "x") (AVar "z"))
*** Exception: Error {errMsg = "Unbound variable z"}
```

<br>

But writing ASTs explicitly is really tedious,
we are used to writing programs as text!

<br>

We want to write a function that converts strings to ASTs if possible:

```haskell
parse :: String -> Aexpr
```

<br>

For example:

```haskell
λ> parse "2 + 6"
APlus (AConst 2) (AConst 6)

λ> parse "(x - y) * 2"
AMul (AMinus (AVar "x") (AVar "y")) (AConst 2)

λ> parse "2 +"
*** Exception: Error {errMsg = "Syntax error"}
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


## Two-step-strategy

How do I read a sentence "He ate a bagel"?

  * First split into words: `["He", "ate", "a", "bagel"]`
  * Then relate words to each other: "He" is the subject, "ate" is the verb, etc
  
<br>  
  
Let's do the same thing to "read" programs! 

<br>
<br>
<br>
<br> 

### Step 1 (Lexing) : From String to Tokens

A string is a list of *characters*:

![Characters](/static/img/info_parser.001a.jpg){#fig:chars width=70%}

First we aggregate characters that "belong together"
into **tokens** (i.e. the "words" of the program):

![Tokens](/static/img/info_parser.001b.jpg){#fig:tokens width=70%}

We distinguish tokens of different kinds based on their format:

* all numbers: integer constant 
* alphanumeric, starts with a letter: identifier
* `+`: plus operator
* etc

<br>
<br>
<br>
<br>

### Step 2 (Parsing) : From Tokens to AST

Next, we convert a sequence of tokens into an AST

  * This is hard...
  * ... but the hard parts do not depend on the language!
  
<br>  
  
**Parser generators**

  * Given the description of the *token format* generates a *lexer*
  * Given the description of the *grammar* generates a *parser*
  
We will be using parser generators,
so we only care about how to describe the token format and the grammar 

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Lexing

We will use the tool called `alex` to generate the **lexer**

Input to `alex`: a `.x` file that describes the *token format*

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Tokens

First we list the kinds of tokens we have in the language:

```haskell
data Token
  = NUM    AlexPosn Int
  | ID     AlexPosn String
  | PLUS   AlexPosn
  | MINUS  AlexPosn
  | MUL    AlexPosn
  | DIV    AlexPosn
  | LPAREN AlexPosn
  | RPAREN AlexPosn
  | EOF    AlexPosn
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

## Token rules

Next we describe the format of each kind of token using a rule:

```haskell
-- Regex:                       Function: AlexPosn -> String -> Token
  \+                            { \p _ -> PLUS   p }
  \-                            { \p _ -> MINUS  p }
  \*                            { \p _ -> MUL    p }
  \/                            { \p _ -> DIV    p }
  \(                            { \p _ -> LPAREN p }
  \)                            { \p _ -> RPAREN p }
  $alpha [$alpha $digit \_ \']* { \p s -> ID     p s }
  $digit+                       { \p s -> NUM p (read s) }
```

Each line consist of:
  
  * a *regular expression* that describes which strings should be recognized as this token
  * a Haskell expression that generates the token
  
You read it as:

  * if at position `p` in the input string 
  * you encounter a substring `s` that matches the *regular expression*
  * evaluate the Haskell expression with arguments `p` and `s`   
  
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Regular Expressions

A regular expression has one of the following forms:

* `[c1 c2 ... cn]` matches *any of* the characters `c1 .. cn`
    
    * `[0-9]` matches any *digit*
    * `[a-z]` matches any *lower-case letter*    
    * `[A-Z]` matches any *upper-case letter*
    * `[a-z A-Z]` matches any *letter*    
    * `[a-z A-Z 0-9]` matches any *alpha-numeric character*   
    
* `R1 R2` matches a string `s1 ++ s2` where `s1` matches `R1` and `s2` matches `R2`
    
    * e.g. `[0-9] [0-9]` matches any two-digit string
    
* `R+` matches *one or more* repetitions of what `R` matches

    * e.g. `[0-9]+` matches a natural number
    
* `R*` matches *zero or more* repetitions of what `R` matches    
    
  
<br>
<br>

## EXERCISE

Write a regular expression for *identifiers*, which

- must have only alphanumeric characters 
- must start with a letter
- cannot be empty

<!--
## QUIZ

Which of the following strings are matched by `[a-z A-Z] [a-z A-Z 0-9]*`?

**(A)** (empty string)

**(B)** `5`

**(C)** `x5`

**(D)** `x`

**(E)** C and D


<br>

(I) final

    *Answer:* E
-->

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Back to token rules

We can **name** some common regexps like:

```haskell
$digit = [0-9]
$alpha = [a-z A-Z]
```

and write `[a-z A-Z] [a-z A-Z 0-9]*` as `$alpha [$alpha $digit]*`

<br>

```haskell
  \+                            { \p _ -> PLUS   p }
  \-                            { \p _ -> MINUS  p }
  \*                            { \p _ -> MUL    p }
  \/                            { \p _ -> DIV    p }
  \(                            { \p _ -> LPAREN p }
  \)                            { \p _ -> RPAREN p }
  $alpha [$alpha $digit \_ \']* { \p s -> ID     p s }
  $digit+                       { \p s -> NUM p (read s) }
```  

* When you encounter a `+`, generate a `PLUS` token
* ...
* When you encounter a nonempty string of digits, convert it into an integer and generate a `NUM`
* When you encounter an alphanumeric string that starts with a letter, save it in an `ID token


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Running the Lexer

From the token rules, `alex` generates a function `alexScan` which

  * given an input string, find the *longest* prefix `p` that matches one of the rules
  * if `p` is empty, it fails
  * otherwise, it converts `p` into a token and returns the rest of the string

We wrap this function into a handy function

```haskell
parseTokens :: String -> Either ErrMsg [Token]
```

which repeatedly calls `alexScan` until it consumes the whole input string or fails

<br>

We can test the function like so:

```haskell
λ> parseTokens "23 + 4 / off -"
Right [ NUM (AlexPosn 0 1 1) 23
      , PLUS (AlexPosn 3 1 4)
      , NUM (AlexPosn 5 1 6) 4
      , DIV (AlexPosn 7 1 8)
      , ID (AlexPosn 9 1 10) "off"
      , MINUS (AlexPosn 13 1 14) 
      ]      
```

```haskell
λ> parseTokens "%"
Left "lexical error at 1 line, 1 column"
```

<br>
<br>
<br>
<br>

## QUIZ

What is the result of `parseTokens "92zoo"`
(positions omitted for readability)?

**(A)** Lexical error

**(B)** `[ID "92zoo"]`

**(C)** `[NUM "92"]`

**(D)** `[NUM "92", ID "zoo"]`

<br>

(I) final

    *Answer:* D

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Parsing

We will use the tool called `happy` to generate the **parser**

Input to `happy`: a `.y` file that describes the *grammar*

<br>
<br>

Wait, wasn't this the grammar?

```haskell
-- Abstract Syntax Tree for expressions
data Aexpr 
  = AConst  Int
  | AVar    Id
  | APlus   Aexpr Aexpr
  | AMinus  Aexpr Aexpr
  | AMul    Aexpr Aexpr
```

This was *abstract syntax*

Now we need to describe *concrete syntax*

  * What programs look like when written as text
  * and how to map that text into the abstract syntax


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Grammars

A grammar is a recursive definition of a set of *parse trees*
  
<br>
<br>  

A grammar is made of:

- **Terminals**: the leaves of the tree (tokens!)

- **Nonterminals:** the internal nodes of the tree

- **Production Rules** that describe how to "produce" a non-terminal from terminals and other non-terminals

    - i.e. what children each nonterminal can have:

```haskell 
Aexpr :      -- Non-term Aexpr can be either:
  | TNUM             -- Term of format "number", or
  | ID               -- Term of format "identifier", or
  | '(' Aexpr ')'    -- Term '(', non-term Aexpr, term ')'
  | Aexpr '*' Aexpr  -- Non-term Aexpr, term '*', non-term Aexpr
  | Aexpr '+' Aexpr  -- Non-term Aexpr, term '+', non-term Aexpr
  | Aexpr '-' Aexpr  -- Non-term Aexpr, term '-', non-term Aexpr
```

<br>
<br>

**Parse** a string `s` = find a parse tree from the grammar, whose leaves spell out `s`

  - here "string" means "list of tokens" (output of the lexer)

<br>
<br>

**Example:** Here is a parse tree for the string `(x + 2)`:
```        
        Aexpr
      /   |   \      
   '('    |    ')'
        Aexpr
     /    |    \
  Aexpr  '+'  Aexpr
    |           |
 'x' (ID)   '2' (TNUM)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

Which string *cannot* be parsed as `Aexpr`?

```haskell
Aexpr : TNUM
      | ID
      | '(' Aexpr ')'
      | Aexpr '*' Aexpr
      | Aexpr '+' Aexpr
      | Aexpr '-' Aexpr
```

**(A)** `x`

**(B)** `x 5`

**(C)** `(x +) 5`

**(D)** `x + 5 + 1`

**(E)** B and C


<br>

(I) final

    *Answer:* E
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Attribute Grammars

*So far:* grammar tells us whether a string is syntactically correct or not

*We want more:* convert a string into an AST

```
parse :: String -> AExpr
```

<br>
<br>

An **attribute grammar** associates a *value* with each node in the parse tree

  - each production is annotated with a *rule* 
  - a rule computes the *value* of a non-terminal from the *values* of its children
  - here *value = AST* (i.e. Haskell value of type `AExpr`) 
  
<br>
<br>

Attribute grammar for arithmetic expressions:

```haskell
--      Format                    Value
Aexpr : TNUM                    { AConst $1    }
      | ID                      { AVar   $1    }
      | '(' Aexpr ')'           { $2           }
      | Aexpr '*' Aexpr         { AMul   $1 $3 }
      | Aexpr '+' Aexpr         { APlus  $1 $3 } 
      | Aexpr '-' Aexpr         { AMinus $1 $3 }
```

- `$1` refers to the *value* of the first child
- `$2` refers to the *value* of the second child
- ...
  
<br>
<br>

**Example:** Computing the value (AST) of `(x + 2)`:
```        
                     Aexpr  ===> APlus (AVar "x") (AConst 2)
                    /  |  \   
                 '('   |  ')'
                     Aexpr  ===> APlus (AVar "x") (AConst 2)
                    /  |  \
AVar "x" <===  Aexpr  '+'  Aexpr  ===> AConst 2
                 |           |
             'x' (ID)   '2' (TNUM)
```
    
<br>
<br>

How do we compute the *value* of a terminal?

  - How do we map terminal `'x'` to string `"x"`?
  
  - How do we map terminal `'2'` to integer `2`?
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
    


## Terminals

Terminals correspond to the *tokens* returned by the lexer

In the `.y` file, we have to declare which terminals in the rules 
correspond to which tokens from the `Token` datatype:

```haskell
-- Terminals:   Tokens from lexer:
    TNUM        { NUM _ $$ }
    ID          { ID _ $$  }
    '+'         { PLUS _   }
    '-'         { MINUS _  }
    '*'         { MUL _    }
    '/'         { DIV _    }
    '('         { LPAREN _ }
    ')'         { RPAREN _ }
```

- Each thing on the left is terminal (as appears in the production rules)

- Each thing on the right is a Haskell pattern for datatype `Token`

- We use `$$` to designate one parameter of a token constructor as the **value** of that token

    - we will refer back to it from the production rules
      
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>  
  
## QUIZ

What is the value of the root `AExpr` node when parsing `1 + 2 + 3`?

```haskell
Aexpr : TNUM                    { AConst $1    }
      | ID                      { AVar   $1    }
      | '(' Aexpr ')'           { $2           }
      | Aexpr '*' Aexpr         { AMul   $1 $3 }
      | Aexpr '+' Aexpr         { APlus  $1 $3 } 
      | Aexpr '-' Aexpr         { AMinus $1 $3 }
```

**(A)** Cannot be parsed as `AExpr`

**(B)** `6`

**(C)** `APlus (APlus (AConst 1) (AConst 2)) (AConst 3)`

**(D)** `APlus (AConst 1) (APlus (AConst 2) (AConst 3))`


<br>

(I) final

    *Answer:* Could be C or D

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Running the Parser

First, we should tell the parser that the top-level non-terminal is `AExpr`:

```haskell
%name aexpr
```

From the production rules and this line, 
`happy` generates a function `aexpr` that tries to parse a sequence of tokens as `AExpr`

We package this function together with the lexer and the evaluator into a handy function

```haskell
evalString :: Env -> String -> Int
```

<br>

We can test the function like so:

(I) lecture

    ```haskell
    λ> evalString [] "1 + 3 + 6"
    10

    λ> evalString [("x", 100), ("y", 20)] "x - y"
    ???

    λ> evalString [] "2 * 5 + 5"
    ???

    λ> evalString [] "2 - 1 - 1"
    ???
    ```
    
(I) final

    ```haskell
    λ> evalString [] "1 + 3 + 6"
    10

    λ> evalString [("x", 100), ("y", 20)] "x - y"
    80

    λ> evalString [] "2 * 5 + 5"
    20

    λ> evalString [] "2 - 1 - 1"
    2
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

## Precedence

```haskell
λ> evalString [] "2 * 5 + 5"
20
```

The problem is that our grammar is **ambiguous**!

There are multiple ways of parsing the string `2 * 5 + 5`, namely

```
--         Good:                    Bad:

           Aexpr                   Aexpr
          /  |  \                 /  |  \
     Aexpr  '+'  Aexpr       Aexpr  '*'   Aexpr
    /  |  \        |           |         /  |  \
Aexpr '*' Aexpr   '5'         '2'    Aexpr '+' Aexpr
 |          |                          |         |
'2'        '5'                        '5'       '5'
```

*Wanted:* tell `happy` that `*` has higher **precedence** than `+`!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


### Solution 1: Grammar factoring

We can split the `AExpr` non-terminal into multiple "levels"

```haskell
Aexpr : Aexpr '+' Aexpr
      | Aexpr '-' Aexpr
      | Factor

Factor : Factor '*' Factor
       | Factor '/' Factor
       | TNUM
       | ID
       | '(' Aexpr ')'
```

Intuition: 

- Only a `Factor` can be multiplied / divided
- Any `Factor` can be promoted to an `AExpr`
- But to turn an `AExpr` into a `Factor`, we need parentheses


<br>
<br>

Now the only way to parse `2 * 5 + 5` is:

```
--           Good:

             Aexpr
          /    |    \
       Aexpr  '+'   Aexpr
         |            |
       Factor       Factor
    /    |    \       |
Factor  '*'  Factor  '5'
  |            |    
 '2'          '5'
```

If we start parsing the wrong way, we get:

```
--     Bad???

       Aexpr
         |
       Factor
    /    |    \
Factor  '*'  Factor
  |            | 
 '2'   -- cannot parse "5 + 5" as Factor!
```

<br>
<br>
<br>
<br>
<br>
<br>

## Associativity

```haskell
λ> evalString [] "2 - 1 - 1"
2
```

With our original grammar,
there are multiple ways of parsing `2 - 1 - 1`, namely

```
--         Good:                    Bad:

           Aexpr                   Aexpr
          /  |  \                 /  |  \
     Aexpr  '-'  Aexpr       Aexpr  '-'   Aexpr
    /  |  \        |           |         /  |  \
Aexpr '-' Aexpr   '1'         '2'    Aexpr '-' Aexpr
 |          |                          |         |
'2'        '1'                        '1'       '1'
```

*Wanted:* tell `happy` that `-` is **left-associative**!

<br>
<br>
<br>
<br>
<br>
<br>


## QUIZ

Can our new grammar still parse `2 - 1 - 1` the wrong way?

```haskell
Aexpr : Aexpr '+' Aexpr
      | Aexpr '-' Aexpr
      | Factor

Factor : Factor '*' Factor
       | Factor '/' Factor
       | TNUM
       | ID
       | '(' Aexpr ')'
```

**(A)** Yes

**(B)** No

<br>

(I) final

    *Answer:* A

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

There are **still** multiple ways of parsing `2 - 1 - 1`, namely

```
--         Good:                    Bad:

           Aexpr                   Aexpr
          /  |  \                 /  |  \
     Aexpr  '-'  Aexpr       Aexpr  '-'   Aexpr
    /  |  \        |           |         /  |  \
Aexpr '-' Aexpr  Factor      Factor   Aexpr '-' Aexpr
  |         |      |           |        |         |
Factor    Factor  '1'         '2'   Factor    Factor
  |         |                           |         |
 '2'       '1'                         '1'       '1'
```

<br>

How do we fix this?

*Hint:* how do we disallow the RHS of a minus to be a minus?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

More grammar factoring!


```haskell
Aexpr : Aexpr '+' Factor
      | Aexpr '-' Factor
      | Factor

Factor : Factor '*' Atom
       | Factor '/' Atom
       | Atom

Atom : TNUM
       | ID
       | '(' Aexpr ')'
```

<br>
<br>

```
--         Good: 

           Aexpr 
          /  |  \ 
     Aexpr  '-'  Factor
    /  |  \        |
Aexpr '-' Factor  Atom
  |         |      |
Factor    Atom    '1'
  |         |     
Atom       '1'    
  |         
 '2'
```

<br>
<br>

```
--      Bad???

        Aexpr
       /  |  \
  Aexpr  '-'   Factor
    |       -- cannot parse "1 - 1" as Factor!     
  Factor
    | 
  Atom
    | 
   '2'
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>



### Solution 2: Parser directives

This problem is so common that parser generators have a special syntax for it!

```haskell 
%left '+' '-'
%left '*' '/'
```

What this means:

  - All our operators are left-associative
  - Operators on the lower line have higher precedence
  
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

That's all folks!  



[0]: https://github.com/ucsd-cse130/arith/blob/master/src/Language/Arith/Types.hs 
[1]: https://github.com/ucsd-cse130/arith/blob/master/src/Language/Arith/Parser0.y
[2]: https://github.com/ucsd-cse130/arith/blob/master/src/Language/Arith/Lexer.x
[3]: https://github.com/ucsd-cse130/arith/blob/master/src/Language/Arith/Parser1.y
[4]: https://github.com/ucsd-cse130/arith/blob/master/src/Language/Arith/Parser2.y
[7]: http://en.wikipedia.org/wiki/Regular_expression
