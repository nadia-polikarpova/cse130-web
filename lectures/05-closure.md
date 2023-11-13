---
title: Environments and Closures
date: 2018-05-07
headerImg: books.jpg
---

## Roadmap


**Past three weeks:**

- How do we *use* a functional language?


**Next three weeks:**

- How do we *implement* a functional language?
- ... in a functional language (of course)

**This week: Interpreter**

- How do we *evaluate* a program given its abstract syntax tree (AST)?
- How do we *prove properties* about our interpreter 
  (e.g. that certain programs never crash)?  
  
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The Nano Language

Features of Nano:

1. **Arithmetic**
2. Variables
3. Let-bindings
4. Functions
5. Recursion

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## 1. Nano: Arithmetic

A grammar of arithmetic expressions:

```haskell
e ::= n
    | e1 + e2
    | e1 - e2
    | e1 * e2
```

<br>

Examples:

| Expression    |               | Value |
| ------------- | ------------- | ----- |
| `4`           | `==>`         | 4     |
| `4 + 12`      | `==>`         | 16    |
| `(4 + 12) - 5`| `==>`         | 11    |

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Representing Expressions and Values

Let's *represent* arithmetic expressions as a type:

```haskell
data Expr = Num Int
          | Add Expr Expr
          | Sub Expr Expr
          | Mul Expr Expr
```

<br>
<br>

Let's *represent* arithmetic values as a type:

```haskell
type Value = Int
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

## Evaluating Arithmetic Expressions

We can now write a Haskell function to  *evaluate* an expression:

```haskell
eval :: Expr -> Value
eval (Num n)     = n
eval (Add e1 e2) = eval e1 + eval e2
eval (Sub e1 e2) = eval e1 - eval e2
eval (Mul e1 e2) = eval e1 * eval e2
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Alternative representation

Let's factor out the operators into a separate type

```haskell
data Binop = Add | Sub | Mul

data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
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

## QUIZ

Evaluator for alternative representation:

```haskell
eval :: Expr -> Value
eval (Num n)        = n
eval (Bin op e1 e2) = evalOp op (eval e1) (eval e2)
```

What's a suitable type for `evalOp`?

**(A)** `Binop -> Value`

**(B)** `Binop -> Value -> Value -> Value`

**(C)** `Binop -> Expr -> Expr -> Value`

**(D)** `Binop -> Expr -> Expr -> Expr`

**(E)** `Binop -> Expr -> Value`

<br>

(I) final

    *Answer:* B

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The Nano Language

Features of Nano:

1. Arithmetic [done]
2. **Variables**
3. Let-bindings
4. Functions
5. Recursion


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## 2. Nano: Variables

Let's add variables!

```haskell
e ::= n        -- OLD
    | e1 + e2 
    | e1 - e2 
    | e1 * e2
    | x        -- NEW
```

<br>
<br>
<br>
<br>

Let's extend the representation of expressions:

```haskell
data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
          | ???                  -- variable
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

```haskell
type Id = String

data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
          | Var Id               -- variable
```

<br>
<br>
<br>
<br>

Now let's extend the evaluation function!

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

What should the following expression evaluate to?

```
x + 1
```

**(A)** `0`

**(B)** `1`

**(C)** Runtime error

<br>

(I) final

    *Answer:* C
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Environment

An expression is evaluated in an **environment**

  - It's a dictionary that maps *variables* to *values*
  
```
["x" := 0, "y" := 12, ...]
```  

<br>

We can *represent* an environment using the following type:

```
type Env = [(Id, Value)]
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Evaluation in an Environment

We write

```
eval env expr  ==> value
```

To mean that evaluating `expr` *in the environment* `env` returns `value`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

What should the result of?

```
eval ["x" := 0, "y" := 12, ...] (x + 1)
```

**(A)** `0`

**(B)** `1`

**(C)** Runtime error

<br>

(I) final

    *Answer:* B

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

To evaluate a variable, **look up** its value in the environment!

| Environment    | Expression    |              | Value |
| -------------- | ------------- |------------- | ----- |
| `["x" := 5]`   | `x`           |`==>`         | 5     |
| `["x" := 5]`   | `x + 12`      |`==>`         | 17     |
| `["x" := 5]`   | `y - 5`       |`==>`         | error  |


<br>
<br>
<br>
<br>


## Evaluating Variables

We need to update our evaluation function to take the environment *as an argument*:

```haskell
eval :: Env -> Expr -> Value
eval env (Num n)        = ???
eval env (Bin op e1 e2) = ???
eval env (Var x)        = ???
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
eval :: Env -> Expr -> Value
eval env (Num n)        = n
eval env (Bin op e1 e2) = evalOp op (eval env e1) (eval env e2)
eval env (Var x)        = lookup x env
```

<br>
<br>
<br>

But how do variables get into the environment?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>




## The Nano Language

Features of Nano:

1. Arithmetic [done]
2. Variables [done]
3. **Let-bindings**
4. Functions
5. Recursion


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## 3. Nano: Let Bindings

Let's add let bindings!

```haskell
e ::= n                -- OLD
    | e1 + e2 
    | e1 - e2 
    | e1 * e2
    | x
    | let x = e1 in e2 -- NEW
```

<br>

Example:


| Environment    | Expression    |              | Value |
| -------------- | ------------- |------------- | ----- |
| `[]`           | `let x = 2 + 3 in x * 2`           |`==>`         | 10     |

<br>
<br>
<br>
<br>


Let's extend the representation of expressions:

```haskell
data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
          | Var x                -- variable
          | ???                  -- let binding
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

```haskell
data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
          | Var Id               -- variable
          | Let Id Expr Expr     -- let binding
```

<br>
<br>
<br>
<br>

Now let's extend the evaluation function!

```haskell
eval :: Env -> Expr -> Value
eval env (Num n)          = n
eval env (Bin op e1 e2)   = evalOp op (eval env e1) (eval env e2)
eval env (Var x)          = lookup x env
eval env (Let x def body) = ???
```

<br>

Let's develop intuition with examples!

<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

What should this evaluate to?

```haskell
let x = 2 + 3 
in
  x + 1
```

**(A)** `1`

**(B)** `5`

**(C)** `6`

**(D)** Error: unbound variable `x`

**(E)** Error: unbound variable `y`


<br>

(I) final

    *Answer:* C

<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

What should this evaluate to?

```haskell
let x = 5 
in
  let y = x + 1 
  in
    x * y
```

**(A)** `5`

**(B)** `6`

**(C)** `30`

**(D)** Error: unbound variable `x`

**(E)** Error: unbound variable `y`


<br>

(I) final

    *Answer:* C

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

    
## QUIZ

What should this evaluate to?

```haskell
let x = 0 
in
  (let x = 100 
   in
     x + 1
  ) + x
```

**(A)** `1`

**(B)** `101`

**(C)** `201`

**(D)** `2`

**(E)** Error: multiple definitions of `x`


<br>

(I) final

    *Answer:* B

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Principle: Static (Lexical) Scoping
    
Every variable *use* (occurrence) gets its value from the most local *definition* (binding)

  - in a *pure* language, the value never changes once defined
  - easy to tell by looking at the program, where a variable's value came from!
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Implementing Lexical Scoping

**Example 1**:

```haskell
               -- environment:
let x = 2 + 3  -- []
in             --   [x := 5]
  x + 1        --   |
```

*Note:* first *evaluate* `2 + 3` and *bind* `x` to the resulting value in the environment

<br>
<br>

**Example 2**:

```haskell
                 -- environment:
let x = 5        -- []
in               --   [x := 5]
  let y = x + 1  --   |
  in             --   | [y := 6, x := 5]
    x * y        --   | |
```

*Note:* `[y := 6]` got *added* to the environment

<br>
<br>

**Example 3**:

```haskell
                 -- environment:
let x = 0        -- []
in               --   [x := 0]
  (let x = 100   --   |
   in            --   | [x := 100, x := 0]
     x + 1       --   | |
  )              --   | |
  + x            --   |     
```

*Note:* `[x := 100]` was only added for the inner scope

<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Evaluating let Expressions

To evaluate `let x = e1 in e2` in `env`:

  1. Evaluate `e1` in `env` to `val`
  2. *Extend* `env` with a mapping `["x" := val]`
  3. Evaluate `e2` in this extended environment
  
<br>
<br>
<br>
<br>
<br>
<br>
<br>  

```haskell
eval :: Env -> Expr -> Value
eval env (Num n)          = n
eval env (Bin op e1 e2)   = evalOp op (eval e1) (eval e2)
eval env (Var x)          = lookup x env
eval env (Let x e1 e2)    = eval env' e2
  where
    v    = eval env e1
    env' = (x, v) : env    
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## The Nano Language

Features of Nano:

1. Arithmetic [done]
2. Variables [done]
3. Let binding [done]
4. **Functions**
5. Recursion
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>



## 4. Nano: Functions

Let's add:
 
  - lambda abstraction (aka function definitions) 
  - applications (aka function calls)


```haskell
e ::= n                -- OLD
    | e1 + e2 
    | e1 - e2 
    | e1 * e2
    | x
    | let x = e1 in e2
                       -- NEW
    | \x -> e  -- abstraction
    | e1 e2    -- application        
```

<br>

Example:

```haskell
let inc = \x -> x + 1 in 
inc 10
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

## QUIZ

What should this evaluate to?

```haskell
let inc = \x -> x + 1 in 
inc 10
```    

**(A)** Undefined variable `x`

**(B)** Undefined variable `inc`

**(C)** `1`

**(D)** `10`

**(E)** `11`

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
<br>

## Representing functions

Let's extend the representation of expressions:

```haskell
data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
          | Var Id               -- variable
          | Let Id Expr Expr     -- let expression
          | ???                  -- abstraction
          | ???                  -- application
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


```haskell
data Expr = Num Int              -- number
          | Bin Binop Expr Expr  -- binary expression
          | Var Id               -- variable
          | Let Id Expr Expr     -- let expression
          | Lam Id Expr          -- abstraction: formal + body
          | App Expr Expr        -- application: function + actual
```

<br>

Example:

```haskell
let inc = \x -> x + 1 in 
inc 10
```

represented as:

```haskell
Let "inc" 
  (Lam "x" (Bin Add (Var "x") (Num 1)))
  (App (Var "inc") (Num 10))
```

<br>
<br>
<br>
<br>
<br>
<br>

## Evaluating Functions

```haskell
                      -- environment  
let inc = \x -> x + 1 
in                    -- [inc := ???]
  inc 10              -- use the value of inc to evaluate this
```

<br>

What is the **value** of `inc`???

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Rethinking our values

**Until now:** a program *evaluates* to an integer (or fails)

```haskell
type Value = Int

type Env = [(Id, Value)]

eval :: Env -> Expr -> Value
```

<br>
<br>

What do these programs evaluate to?

```haskell
(1)
\x -> x + 1
==> ???

(2)
let f = \x y -> x + y in
f 1
==> ???
```

(I) final

    Conceptually, they both evaluate to a function that increments its argument
    

<br>
<br>
<br>
<br>
<br>
<br>

**Now:** a program evaluates to an integer or *a function* (or fails)

  - Remember: functions are *first-class* values
  
<br>

Let's change our definition of values!  

```haskell
data Value = VNum Int
           | VFun ??? -- What info do we need to store?
           
-- Other types stay the same
type Env = [(Id, Value)]

eval :: Env -> Expr -> Value           
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

## Function values

How should we represent a function value?

```haskell
let inc = \x -> x + 1 in 
inc 10
```

We need to store enough information about `inc`
so that we can later evaluate any *application* of `inc`
(like `inc 0`, `inc 5`, `inc 10`, `inc (factorial 100)`)

<br>
<br>
<br>
<br>

The **value** of a function is its **code**!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Representing Function Values (First Attempt)

Grammar for values:

```haskell
v ::= n       -- OLD: number
    | <x, e>  -- NEW: formal + body
```

<br>
<br>

Haskell representation:

```haskell
data Value = VNum Int
           | VFun Id Expr -- formal + body
```

<br>
<br>

Let's try this!

```haskell
                      -- environment  
let inc = \x -> x + 1 
in                    -- [inc := <x, x + 1>]
  inc 10              -- how do we evaluate this?
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

## Evaluating applications

```haskell
                      -- environment  
let inc = \x -> x + 1 
in                    -- [inc := <x, x + 1>]
  inc 10              -- how do we evaluate this?
```

To evaluate `inc 10`:
  
  1. Evaluate `inc`, get `<x, x + 1>` 
  2. Evaluate `10`, get `10`
  3. Evaluate `x + 1` in an environment *extended* with `[x := 10]`


<br>
<br>
<br>
<br>
<br>

Let's extend our `eval` function!

```haskell
eval :: Env -> Expr -> Value
eval env (Num n)          = ???
eval env (Bin op e1 e2)   = ???
eval env (Var x)          = ???
eval env (Let x e1 e2)    = ???
eval env (Lam x e)        = ???
eval env (App e1 e2)      = ???    
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
eval :: Env -> Expr -> Value
eval env (Num n)        = VNum n
eval env (Var x)        = lookup x env
eval env (Bin op e1 e2) = evalOp op (eval env e1) (eval env e1)
eval env (Let x e1 e2)  = eval env' e2
  where
    v = eval env e1
    env' = (x, v) : env
eval env (Lam x body)   = VFun x body
eval env (App fun arg)  = evalApp env (eval env e1) (eval env e1)

evalApp :: Env -> Value -> Value -> Value
evalApp env (VFun x body) vArg = eval env' body
  where
    env' = (x, vArg) : env
evalApp _ _ _                  = error "applying a non-function"
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

What should this evaluate to?

```haskell
let c = 1 
in
  let inc = \x -> x + c
  in
    inc 10
```

**(A)** Undefined variable `x`

**(B)** Undefined variable `c`

**(C)** `1`

**(D)** `10`

**(E)** `11`

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

## QUIZ

And what should this evaluate to?

```haskell
let c = 1 
in
  let inc = \x -> x + c
  in
    let c = 100
    in
      inc 10
```

**(A)** Error: multiple definitions of `c`

**(B)** `11`

**(C)** `110`

<br>

(I) final

    *Answer:* B
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Reminder: Referential Transparency

The same expression must *always* evaluate to the same value

  - In particular: a function must *always* return the same output for a given input
  
<br>
<br>  
  
Why?

```haskell
> myFunc 10
11

> myFunc 10
110
```

Oh no! How do I find the bug???

  - Is it in `myFunc`?
  - Is it in a global variable?
  - Is it in a library somewhere else?
  
My worst debugging nightmare!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Static vs Dynamic Scoping

What we want:

```haskell
let c = 1               -- <-------------------
in                      --                    \
  let inc = \x -> x + c -- refers to this def \ 
  in
    let c = 100
    in
      inc 10
      
==> 11
```

<br>
<br>

**Lexical** (or **static**) scoping:

  - each occurrence of a variable refers to the most recent binding *in the program text*
  - definition of each variable is unique and known *statically*
  - guarantees referential transparency:

```haskell
let c = 1               -- <-------------------
in                      --                    \
  let inc = \x -> x + c -- refers to this def \ 
  in
    let c = 100
    in
      let res1 = inc 10      -- ==> 11
      in
        let c = 200
        in 
           let res2 = inc 10 -- ==> 11
           in res1 == res2   -- ==> True
```
  
<br>
<br>
<br>
<br>

What we **don't** want:

```haskell
let c = 1               
in
  let inc = \x -> x + c -- refers to this def \ 
  in                    --                    \
    let c = 100         -- <-------------------
    in
      inc 10
      
==> 110
```

<br>
<br>

**Dynamic** scoping:

  - each occurrence of a variable refers to the most recent binding *during program execution*
  - can't tell where a variable is defined just by looking at the function body
  - *violates* referential transparency:
    
```haskell
let c = 1               
in
  let inc = \x -> x + c     -- refers to this def \  \
  in                        --                    \  \
    let c = 100             -- <-------------------  \
    in                      --                       \
      let res1 = inc 10     -- ==> 110               \
      in                    --                       \
        let c = 200         -- <----------------------
        in 
          let res2 = inc 10 -- ==> 210!!!
          in res1 == res2   -- ==> False
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

## QUIZ

Which scoping does our `eval` function implement?

```haskell
...
eval env (Lam x body) = VFun x body
eval env (App fun arg) = eval env' body
  where
    VFun x body = eval env fun
    vArg        = eval env arg
    env'        = (x, vArg) : env
```

**(A)** Static

**(B)** Dynamic

**(C)** Neither

<br>

(I) final

    *Answer:* B
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Let's find out!

```haskell
                         -- env:
let c = 1                --                                          []
in                       --                                  ["c" := 1]
  let inc = \x -> x + c
  in                     --             ["inc" := <x, x + c>, "c" := 1]
    let c = 100
    in                   -- ["c" := 100, "inc" := <x, x + c>, "c" := 1]
      inc             10
      
-- 1. ==> <x, x + c>

-- 2.                 ==> 10

-- 3. x + c      ["x" := 10, "c" := 100, "inc" := <x, x + c>, "c" := 1]      

--    ==> 110
```

Ouch.

What went wrong?

<br>
<br>
<br>
<br>

```haskell
let c = 1
in                       --                                  ["c" := 1]
  let inc = \x -> x + c  -- we want this "c" to ALWAYS mean 1!
  in                     --             ["inc" := <x, x + c>, "c" := 1]
    let c = 100
    in                   -- ["c" := 100, "inc" := <x, x + c>, "c" := 1]
      inc 10       -- but now it means 100 because we are in a new env!
```

<br>
<br>
<br>
<br>

**Lesson learned:** need to remember what `c` was bound to when `inc` was **defined**!

  - i.e. "freeze" the environment at the point of function definition

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Implementing Static Scoping

Key ideas:

 - **At definition:** Freeze the environment in the function's value
 - **At call:** Use the *frozen* environment to evaluate the body
     - instead of the *current* environment
 
```haskell
                         -- env:
let c = 1                --                                          []
in                       --                                  ["c" := 1]
  let inc = \x -> x + c
  in                     --        ["inc" := <fro, x, x + c>, "c" := 1]
                         --             where fro = ["c" := 1]
    let c = 100
    in                   -- ["c" := 100, "inc" := <fro, x, x + c>, ...]
      inc             10
      
-- 1. ==> <fro, x, x + c>

-- 2.                 ==> 10
--               add "x" to fro instead of env:
-- 3. x + c      ["x" := 10, "c" := 1]      

--    ==> 11
```

Tada!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Function Values as Closures

To implement lexical scoping, we will represent function values as *closures*

**Closure** = *lambda abstraction* (formal + body) + *environment* at function definition 

<br>
<br>

*Updated* grammar for values:

```haskell
v ::= n
    | <env, x, e>  -- NEW: frozen env + formal + body
    
env ::= []
      | (x := v) : env
```

<br>
<br>

*Updated* Haskell representation:

```haskell
data Value = VNum Int
           | VClos Env Id Expr -- frozen env + formal + body
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


## Evaluating function definitions

How should we modify our `eval` for `Lam`?

```haskell
data Value = VNum Int
           | VClos Env Id Expr -- env + formal + body
           
eval :: Env -> Expr -> Value
eval env (Lam x body) = ??? -- construct a closure
``` 

<br>

Recall: **At definition:** Freeze the environment in the function's value

Exact code for you to figure out in HW4

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Evaluating function calls

How should we modify our `eval` for `App`?

```haskell
data Value = VNum Int
           | VClos Env Id Expr -- env + formal + body
           
eval :: Env -> Expr -> Value
eval env (App e1 e2) = ??? -- apply the closure
``` 

<br>

Recall: **At call:** Use the *frozen* environment to evaluate the body

Exact code for you to figure out in HW4

<br>
<br>

**Hint:** Recall evaluating `inc 10`:

1. Evaluate `inc` to get `<fro, x, x + c>`
2. Evaluate `10` to get `10`
3. Evaluate `x + c` in `(x := 10) : fro`

<br>
<br>

Let's generalize to `e1 e2`:

1. Evaluate `e1` to get `<fro, param, body>`
2. Evaluate `e2` to get `v2`
3. Evaluate `body` in `(param := v2) : fro`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Advanced Features of Functions

- Functions returning functions
  - aka *partial applications*
- Functions taking functions as arguments
  - aka *higher-order functions*
- Recursion

Does our `eval` support this?

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

What should the following evaluate to?

```haskell
let add = \x y -> x + y
in
  let add1 = add 1
  in
    let add10 = add 10
    in
      add1 100 + add10 1000
```

**(A)** Runtime error

**(B)** 1102

**(C)** 1120

**(D)** 1111

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
<br>

## Partial Applications Achieved!

Closures support functions returning functions!

```haskell
let add = \x -> (\y -> x + y) --                           env0 = []
in                         -- env1 = ["add" := <[], x, \y -> x + y>]
  let add1 = 
        add 1 -- eval ("x" := 1 : env0) (\y -> x + y) 
              --   ==> <["x" := 1], y, x + y>
  in       -- env2 = ["add1" := <["x" := 1], y, x + y>, "add" := ...]
    let add10 = 
      add 10 -- eval ("x" := 10 : env0) (\y -> x + y) 
             --   ==> <["x" := 10], y, x + y>
    in  -- env3 = ["add10" := <["x" := 10], y, x + y>, "add1" := ...]
      add1 100 -- eval ["y" := 100, "x" := 1] (x + y) 
               --   ==> 101
      + 
      add10 1000 -- eval ["y" := 1000, "x" := 10] (x + y) 
                 --  ==> 1010

==> 1111                 
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

## QUIZ

What should the following evaluate to?

```haskell
let inc = \x -> x + 1
in
  let doTwice = \f -> (\x -> f (f x))
  in
    doTwice inc 10
```

**(A)** Runtime error

**(B)** 11

**(C)** 12

<br>

(I) final

    *Answer:* C

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Higher-order Functions Achieved

Closures support functions taking functions as arguments!

```haskell
let inc = \x -> x + 1                                -- env0 = []
in                              -- env1 = [inc := <[], x, x + 1>]
  let doTwice = \f -> (\x -> f (f x))
  in  -- env2 = [doTwice := <env1, f, \x -> f (f x)>, inc := ...]
    ((doTwice inc) -- eval ("f" := <[],x,x + 1> : env1) (\x -> f (f x))
                   -- ==> <("f" := <[],x,x + 1> : env1), x, f (f x)>
                   
      10)          -- eval ["x" := 10, "f" := <[],x,x + 1>, ...] f (f x)
      
-- f   ==> <[], x, x + 1>
-- x   ==> 10
-- <[], x, x + 1> 10 ==> eval ["x" := 10] x + 1 ==> 11
-- <[], x, x + 1> 11 ==> eval ["x" := 11] x + 1 ==> 12
    
==> 12    
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

## QUIZ

What does this evaluate to?

```haskell
let f = \n -> n * f (n - 1) 
in
  f 5
```

**(A)** `120`

**(B)** Evaluation does not terminate

**(C)** Error: unbound variable `f`

<br>

(I) final

    *Answer:* C

<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
let f = \n -> n * f (n - 1) 
in -- [f := <[], n, n * f (n - 1)>]
  f 5 -- eval [n := 5] (n * f (n - 1))
      --  ==> unbound variable f!!!
```


**Lesson learned:** to support recursion, 
you need to figure out a way to put the function itself *back* into its closure environment
before the body gets evaluated!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The Nano Language

Features of Nano:

1. Arithmetic [done]
2. Variables [done]
3. Let bindings [done]
3. Functions [done]
4. Recursion **[you figure it out in HW4]**


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

That's all folks!


