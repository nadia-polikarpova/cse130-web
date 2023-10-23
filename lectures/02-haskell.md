---
title: A crash course in Haskell
date: 2016-09-26
headerImg: books.jpg
---

## What is Haskell?

<br>

A **typed**, **lazy**, **purely functional** programming language

<br>

Haskell = $\lambda$-calculus + 

  + better syntax
  + types
  + built-in features
    - booleans, numbers, characters
    - records (tuples)
    - lists
    - recursion
    - ...
    
<br>
<br>
<br>
<br>
<br>
<br>    
    
## Why Haskell?

Haskell programs tend to be *concise* and *correct*   

### QuickSort in Haskell

```haskell
sort []     = []
sort (x:xs) = sort ls ++ [x] ++ sort rs
  where
    ls      = [ l | l <- xs, l <= x ]
    rs      = [ r | r <- xs, x <  r ]
```

### Goals for this week

1. Understand the code above
2. Understand what **typed**, **lazy**, and **purely functional** means (and why it's cool)

<br>
<br>
<br>
<br>
<br>
<br>

## Haskell vs $\lambda$-calculus: similarities

### (1) Programs

A program is an **expression** (*not* a sequence of statements)

It **evaluates** to a value (it *does not* perform actions)

  * **$\lambda$**:

    ```
    (\x -> x) apple     -- =~> apple
    ```

  * **Haskell**:
  
    ```
    (\x -> x) "apple"   -- =~> "apple"
    ```

### (2) Functions    
      
Functions are *first-class values*:

* can be *passed as arguments* to other functions
* can be *returned as results* from other functions
* can be *partially applied* (arguments passed *one at a time*)

(I) lecture
 
    ```haskell
    (\f x -> f (f x)) (\z -> z + 1) 0   -- =~> ???
    ```
    
(I) final    

    ```haskell
    (\f x -> f (f x)) (\z -> z + 1) 0   -- =~> 2
    ```


*But:* unlike $\lambda$-calculus, not everything is a function!

     
### (3) Top-level bindings

Like in Elsa, we can *name* terms to use them later
 
**Elsa**:

```haskell
let T    = \x y -> x
let F    = \x y -> y

let PAIR = \x y -> \b -> ITE b x y
let FST  = \p -> p T
let SND  = \p -> p F

eval fst:
 FST (PAIR apple orange)
 =~> apple
```

**Haskell**:

```haskell
haskellIsAwesome = True

pair = \x y -> \b -> if b then x else y
fst = \p -> p haskellIsAwesome
snd = \p -> p False

-- In GHCi:
> fst (pair "apple" "orange")   -- "apple"
```   
    
The names are called **top-level variables**

Their definitions are called **top-level bindings**

<br>
<br>
<br>
<br>
<br>
<br>
    
## Better Syntax: Equations and Patterns

You can define function bindings using **equations**:

```haskell
pair x y b = if b then x else y -- same as: pair = \x y b -> ...
fst p      = p True             -- same as: fst = \p -> ...
snd p      = p False            -- same as: snd = \p -> ...
```
<br>
<br>
<br>
<br>
<br>
<br>

A *single* function binding can have *multiple* equations with different **patterns** of parameters:

```haskell
pair x y True  = x  -- If 3rd arg evals to True,
                    -- use this equation;
pair x y False = y  -- Otherwise, if 3rd evals to False,
                    -- use this equation.
```

At run time, the first equation whose pattern matches the actual arguments is chosen

For now, a **pattern** is:
 
  * a *variable* (matches any value)
  
  * or a *value* (matches only that value)

<br>
<br>

Same as:

```haskell
pair x y True  = x  -- If 3rd arg evals to True,
                    -- use this equation;
pair x y b     = y  -- Otherwise, use this equation.
```

<br>
<br>

Same as:

```haskell
pair x y True  = x
pair x y _     = y  -- Wildcard pattern `_` is like a variable 
                    -- but cannot be used on the right
```

<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

Which of the following definitions of `pair` is **not the same** as the original?

```haskell
pair = \x y -> \b -> if b then x else y
```

**A.** `pair x y = \b -> if b then x else y`

**B.**
```haskell
pair x _ True  = x
pair _ y _     = y
```

**C.** `pair x _ True = x`

**D.**
```haskell
pair x y b     = x
pair x y False = y
```

**E.**  C and D

<br>

(I) final

    _Answer:_ **E**

<br>
<br>
<br>
<br>
<br>

## Equations with guards

An equation can have multiple guards (Boolean expressions):

```haskell
cmpSquare x y  |  x > y*y   =  "bigger :)"
               |  x == y*y  =  "same :|"
               |  x < y*y   =  "smaller :("
```

Same as:

```haskell
cmpSquare x y  |  x > y*y   =  "bigger :)"
               |  x == y*y  =  "same :|"
               |  otherwise =  "smaller :("
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

## Recursion

Recursion is built-in, so you can write:

```haskell
sum n = if n == 0 
          then 0 
          else n + sum (n - 1)
```

or you can write:

```haskell
sum 0 = 0
sum n = n + sum (n - 1)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The scope of variables

Top-level variable have **global** scope,
so you can write:

```haskell
message = if haskellIsAwesome          -- this var defined below
            then "I love CSE 130"
            else "I'm dropping CSE 130"
            
haskellIsAwesome = True
```

<br>
<br>

Or you can write:

```haskell
-- What does f compute?
f 0 = True
f n = g (n - 1) -- mutual recursion!

g 0 = False
g n = f (n - 1) -- mutual recursion!
```

(I) final

    Answer: `f` is `isEven`, `g` is `isOdd` 

<br>
<br>
<br>

Is this allowed?

```haskell
haskellIsAwesome = True

haskellIsAwesome = False -- changed my mind
```

(I) final

    Answer: no, a variable can be defined once per scope; no mutation!

<br>
<br>
<br>
<br>

### Local variables

You can introduce a *new* (local) scope using a `let`-expression:

```haskell
sum 0 = 0
sum n = let n' = n - 1          
        in n + sum n'  -- the scope of n' is the term after in
```

<br>
<br>
<br>

Syntactic sugar for nested `let`-expressions:

```haskell
sum 0 = 0
sum n = let 
          n'   = n - 1
          sum' = sum n'
        in n + sum'
```

<br>
<br>
<br>

If you need a variable whose scope is an equation, use the `where` clause instead:

```haskell
cmpSquare x y  |  x > z   =  "bigger :)"
               |  x == z  =  "same :|"
               |  x < z   =  "smaller :("
  where z = y*y
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Types

<br>
<br>
<br>
<br>

What would *Elsa* say?

```haskell
let WEIRDO = ONE ZERO
```

<br>

(I) final

    Answer: Nothing. When evaluated will crunch to something nonsensical.
    $lambda$-calculus is **untyped**.

<br>
<br>
<br>

What would *Python* say?

```python
def weirdo():
  return 0(1)
```

<br>

(I) final

    Answer: Nothing. When evaluated will cause a run-time error.
    Python is **dynamically typed**.

<br>
<br>
<br>

What would *Java* say?

```java
void weirdo() {
  int zero = 0;
  zero(1);
}
```

<br>

(I) final

    Answer: Java compiler will reject this.
    Java is **statically typed**.

<br>
<br>
<br>
<br>
<br>
<br>

In *Haskell* every expression either **has a type**
or is **ill-typed** and rejected statically
(at compile-time, before execution starts)

  * like in Java
  * unlike $\lambda$-calculus or Python

```haskell
weirdo = 1 0     -- rejected by GHC
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type annotations

You can annotate your bindings with their types using `::`, like so:

```haskell
-- | This is a Boolean:
haskellIsAwesome :: Bool            
haskellIsAwesome = True

-- | This is a string
message :: String
message = if haskellIsAwesome
            then "I love CSE 130"
            else "I'm dropping CSE 130"
            
-- | This is a word-size integer
rating :: Int
rating = if haskellIsAwesome then 10 else 0

-- | This is an arbitrary precision integer
bigNumber :: Integer
bigNumber = factorial 100
```

If you omit annotations, GHC will infer them for you

  * Inspect types in GHCi using `:t`
  * You should annotate all top-level bindings anyway! (Why?)

<br>
<br>
<br>
<br>
<br>
<br>

## Function Types

Functions have **arrow types**:

* `\x -> e` has type `A -> B`
* if `e` has type `B` assuming `x` has type `A`

For example:

(I) lecture
 
    ```haskell
    > :t (\x -> if x then `a` else `b`)  -- ???
    ```    
    
(I) final    

    ```haskell
    > :t (\x -> if x then `a` else `b`)
    (\x -> if x then `a` else `b`) :: Bool -> Char
    ```

<br>
<br>
<br>
<br>

You should annotate your function bindings:

```haskell
sum :: Int -> Int
sum 0 = 0
sum n = n + sum (n - 1)
```

With multiple arguments:

```haskell
pair :: String -> (String -> (Bool -> String))
pair x y b = if b then x else y
```

Same as:

```haskell
pair :: String -> String -> Bool -> String
pair x y b = if b then x else y
```

<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

With `pair :: String -> String -> Bool -> String`,
what would GHCi say to 

```haskell
>:t pair "apple" "orange"
```

**A.** Syntax error

**B.** The term is ill-typed

**C.** `String`

**D.** `Bool -> String`

**E.** `String -> String -> Bool -> String`

<br>

(I) final

    _Answer:_ **D**

<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Lists

A list is

  * either an *empty list*
    
    `[]       -- pronounced "nil"`
    
  * or a *head element* attached to a *tail list* 
  
    `x:xs     -- pronounced "x cons xs"`
    
<br>
<br>    
  
Examples:

```haskell
[]                -- A list with zero elements

1:[]              -- A list with one element: 1

(:) 1 []          -- Same thing: for any infix op, 
                  -- (op) is a regular function!

1:(2:(3:(4:[])))  -- A list with four elements: 1, 2, 3, 4

1:2:3:4:[]        -- Same thing (: is right associative)

[1,2,3,4]         -- Same thing (syntactic sugar)
```  

<br>
<br>

### Terminology: constructors and values

`[]` and `(:)` are called the list **constructors**

We've seen constructors before:

  * `True` and `False` are `Bool` constructors
  * `0`, `1`, `2` are... well, it's complicated, but you can think of them as `Int` constructors
  * these constructions didn't take any parameters, so we just called them *values*

In general, a **value** is a constructor applied to *other values*

  * examples above are list values

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## The Type of a List

A list has type `[A]` if each one of its elements has type `A`

Examples:

```haskell
myList :: [Int]
myList = [1,2,3,4]
```

<br>

(I) lecture
 
    ```haskell
    -- myList' :: ??
    myList' = ['h', 'e', 'l', 'l', 'o']
    ```
    
(I) final    

    ```haskell
    myList' :: [Char]                   -- or :: String
    myList' = ['h', 'e', 'l', 'l', 'o'] -- or = "hello"
    ```

<br>

(I) lecture
 
    ```haskell
    -- myList'' :: ???
    myList'' = [1, 'h']    
    ```
    
(I) final    

    ```haskell
    -- myList'' :: Type error: elements have different types!
    myList'' = [1, 'h']
    ```
    
<br>

(I) lecture
 
    ```haskell
    -- myList''' :: ???
    myList''' = []
    ```      
    
(I) final    

    ```haskell
    myList''' :: [t] -- Generic: works for any type t!
    myList''' = []
    ```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
  
## Functions on lists: range

(I) lecture
 
    ```haskell
    -- | List of integers from n upto m
    upto :: Int -> Int -> [Int]
    upto l u = ???
    ```
    
(I) final    

    ```haskell
    -- | List of integers from n upto m
    upto :: Int -> Int -> [Int]
    upto l hi
      | l > u     = []
      | otherwise = l : (upto (l + 1) u)
    ```

<br>
<br>
<br>
<br>
<br>

There's also syntactic sugar for this!

```haskell
[1..7]    -- [1,2,3,4,5,6,7]
[1,3..7]  -- [1,3,5,7]
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Functions on lists: length

```haskell
-- | Length of the list
length :: ???
length xs = ???
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

## Pattern matching on lists

```haskell
-- | Length of the list
length :: [Int] -> Int
length []     = 0
length (_:xs) = 1 + length xs
```

<br>
<br>

~~A pattern is either a *variable* (incl. `_`) or a *value*~~

A pattern is 

  * either a *variable* (incl. `_`)
  * or a *constructor* applied to other *patterns*
  
<br>
<br>  

**Pattern matching** attempts to match *values* against *patterns* and, 
if desired, *bind* variables to successful matches.

<br>
<br>
<br>
<br>
<br>  


## QUIZ

What happens when we match the pattern `(x:xs)` against the value `[1]`?

**A.** Does not match

**B.** `x` is bound to `1`, and `xs` is unbound

**C.** `xs` is bound to `[1]`, and `x` is unbound

**D.** `x` is bound to `1`, `xs` is bound to `[]`

**E.** `x` is bound to `1`, `xs` is bound to `[1]`

<br>

(I) final

    _Answer:_ **D**

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## EXERCISE: counting zeros

Write a function `count0` that counts the number of zeros in a list

  - do not use *conditionals* or *guards*!

```haskell
count0 :: [Int] -> Int
count0 = ???
```

<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ

Which of the following is **not** a pattern?

**A.** `(1:xs)`

**B.** `(_:_:_)`

**C.** `[x]`

**D.** `[x+y,1,2]`

**E.**  all of the above

<br>

(I) final

    _Answer:_ **D** (`x+y` is a function application, not a constructor application)
  

<br>
<br>
<br>
<br>
<br>  


## Some useful library functions

```haskell
-- | Is the list empty?
null :: [t] -> Bool

-- | Head of the list
head :: [t] -> t   -- careful: partial function!

-- | Tail of the list
tail :: [t] -> [t] -- careful: partial function!

-- | Length of the list
length :: [t] -> Int

-- | Append two lists
(++) :: [t] -> [t] -> [t]

-- | Are two lists equal?
(==) :: [t] -> [t] -> Bool
```

<br>

You can search for library functions on [Hoogle](https://www.haskell.org/hoogle/)!

<br>
<br>
<br>
<br>
<br>
<br>

## Pairs

```haskell
myPair :: (String, Int)  -- pair of String and Int
myPair = ("apple", 3)
```

<br>

`(,)` is the *pair constructor*

<br>
<br>


Field access:

```haskell
-- Option 1: using library functions:
whichFruit = fst myPair  -- "apple"
howMany    = snd myPair  -- 3
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

## EXERCISE: Destructing pairs

Define the following function:

```haskell
isEmpty :: (String, Int) -> Bool
isEmpty p = (fst p == "") || (snd p == 0)
```

but **without** using `fst` or `snd`!

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
-- Using pattern matching:
isEmpty (x, y)   =  x == "" || y == 0

-- With multiple equations (more idiomatic):
isEmpty ("", _)  = True
isEmpty (_, 0)   = True
isEmpty _        = False
```

<br>
<br>

You can use pattern matching not only in equations,
but also in $\lambda$-bindings and `let`-bindings!

```haskell
-- pattern matching in lambda:
isEmpty          = \(x, y) -> x == "" || y == 0

-- pattern matching in let:
isEmpty p        = let (x, y) = p in x == "" || y == 0

-- Now p is the whole pair and x, y are first and second:
isEmpty p@(x, y) =  x == "" || y == 0
```

<br>
<br>
<br>
<br>
<br>


## Tuples

Can we implement triples like in $\lambda$-calculus?

<br>
<br>
<br>
<br>

Sure! But Haskell has native support for $n$-tuples:

```haskell
myPair   :: (String, Int)
myPair   = ("apple", 3)

myTriple :: (Bool, Int, [Int])
myTriple = (True, 1, [1,2,3])

my4tuple :: (Float, Float, Float, Float)
my4tuple = (pi, sin pi, cos pi, sqrt 2)

...

-- And also:
myUnit   :: ()
myUnit   = ()
```

<br>
<br>
<br>
<br>
<br>
<br>

## List comprehensions

A convenient way to construct lists from other lists:


```haskell
[toUpper c | c <- s]  -- Convert string s to upper case


[(i,j) | i <- [1..3],
         j <- [1..i] ] -- Multiple generators
         
[(i,j) | i <- [0..5],
         j <- [0..5],
         i + j == 5] -- Guards         
```

<br>
<br>
<br>
<br>
<br>
<br>  

## QuickSort in Haskell

```haskell
sort :: [Int] -> [Int]
sort []     = []
sort (x:xs) = sort ls ++ [x] ++ sort rs
  where
    ls      = [ l | l <- xs, l <= x ]
    rs      = [ r | r <- xs, x <  r ]
```

<br>
<br>
<br>
<br>
<br>
<br>

## What is Haskell?

<br>

A **typed**, **lazy**, **purely functional** programming language

<br>
<br>
<br>

### Haskell is statically typed

Every expression either has a type,
or is *ill-typed* and rejected at compile time

<br>

**Why is this good?**

  * catches errors early
  * types are contracts (you don't have to handle ill-typed inputs!)
  * enables compiler optimizations
  
<br>
<br>
<br>
<br>
<br>
<br>

### Haskell is purely functional

**Functional** = functions are *first-class values*

**Pure** = a program is an expression that evaluates to a value

  * no side effects!
  
  * unlike in Python, Java, etc:
  
    ```java
    public int f(int x) {
      calls++;                         // side effect!
      System.out.println("calling f"); // side effect!
      launchMissile();                 // side effect!
      return calls;
    }
    ```
    
  * in Haskell, a function of type `Int -> Int`
    computes a *single integer output* from a *single integer input*
    and does **nothing else**
  
**Referential transparency:** The same expression always evaluates to the same value

  * More precisely: In a scope where `x1, ..., xn` are defined,
    all occurrences of `e` with `FV(e) = {x1, ..., xn}` have the same value

<br>
  
**Why is this good?**

  * easier to reason about (`e1 + e2`: no need to worry about order of evaluation!)
  * enables compiler optimizations (`e1 + e2`: only need to compute `e1` once!)
  * great for parallelization (`e1 + e2`: we can always compute `e1` and `e2` in parallel!)
  
<br>
<br>
<br>
<br>
<br>
<br>

### Haskell is lazy

An expression is evaluated only when its result is needed
  
Example: `take 2 [1 .. (factorial 100)]`

<br>

```haskell
        take 2 (   upto 1 (factorial 100))
=>      take 2 (   upto 1 933262154439...)
=>      take 2 (1:(upto 2 933262154439...)) -- def upto
=> 1:  (take 1 (   upto 2 933262154439...)) -- def take 3
=> 1:  (take 1 (2:(upto 3 933262154439...)) -- def upto
=> 1:2:(take 0 (   upto 3 933262154439...)) -- def take 3
=> 1:2:[]                                   -- def take 1
```

<br>

**Why is this good?**

  * can implement cool stuff like infinite lists: `[1..]`
  
    ```haskell
    -- first n pairs of co-primes: 
    take n [(i,j) | i <- [1..],
                    j <- [1..i],
                    gcd i j == 1]
    ```

  
  * encourages simple, general solutions
  * but has its problems too :(
           
<br>
<br>
<br>
<br>
<br>

That's all folks!  
  
  

   


