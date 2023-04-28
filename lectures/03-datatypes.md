---
title: Algebraic Data Types
date: 2018-04-22
headerImg: books.jpg
---    
    
    
## Representing complex data

**We've seen:**

  * *base* types: `Bool`, `Int`, `Integer`, `Float`
  * some ways to *build up* types: given types `T1, T2`
  
    - functions: `T1 -> T2`
    - tuples: `(T1, T2)`
    - lists: `[T1]`
    
<br>
<br>    
  
**Algebraic Data Types:** a single, powerful technique 
for building up types to represent complex data

  * lets you define your own data types
  * subsumes tuples and lists!
    
    
<br>
<br>
<br>
<br>
<br>
<br>

## Algebraic Data Types

1. ***Type Synonyms:* naming existing types**

2. *Product Types:* bundling things together

3. *Sum Types:* types with multiple variants

4. *Recursive Types:* types that contain themselves
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Type Synonyms

Synonyms are just names ("aliases") for existing types

- think `typedef` in `C`

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A type to represent circles

A tuple `(x, y, r)` is a *circle* with center at `(x, y)` and radius `r`

```haskell
type CircleT = (Double, Double, Double)
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

## A type to represent cuboids

A tuple `(length, depth, height)` is a *cuboid*

```haskell
type CuboidT = (Double, Double, Double)
```

![](https://upload.wikimedia.org/wikipedia/commons/d/dc/Cuboid.png){#fig:cuboid .align-center width=60%}


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Using Type Synonyms

We can now use synonyms by creating values of the given types

```haskell
circ0 :: CircleT 
circ0 = (5, 1, 100)  -- circle at (5,1) with radius 100

cub0 :: CuboidT
cub0 = (10, 20, 30)  -- cuboid with l=10, d=20, h=30 
```

And we can write functions over synonyms too

```haskell
area :: CircleT -> Double
area (x, y, r) = pi * r * r  

volume :: CuboidT -> Double
volume (l, d, h) = l * d * h 
```

We should get this behavior

```haskell
>>> area circ0 
31415.926535897932

>>> volume cub0
6000
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

Suppose we have the definitions

```haskell
type CircleT = (Double, Double, Double)
type CuboidT = (Double, Double, Double)

circ0 :: CircleT
circ0 = (5, 1, 100)  -- circle at (5,1) with radius 100

cub0 :: CuboidT
cub0 = (10, 20, 30)  -- cuboid with length=10, depth=20, height=30 

area :: CircleT -> Double
area (x, y, r) = pi * r * r  

volume :: CuboidT -> Double
volume (l, d, h) = l * d * h
```

What is the result of

```haskell
>>> volume circ0
```

**A.** `500`

**B.** Type error

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
<br>

## Beware!

Type Synonyms 

* Do not _create_ new types

* Just _name_ existing types

And hence, synonyms 

* Do not prevent _confusing_ different values 

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Algebraic Data Types

1. *Type Synonyms:* naming existing types \[done\]

2. ***Product Types:* bundling things together**

3. *Sum Types:* types with multiple variants

4. *Recursive Types:* types that contain themselves

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Creating *New* Data Types 

We can avoid mixing up values by creating _new_ `data` types

```haskell
-- | A new type `Circle` with constructor `MkCircle`,
--   which takes three arguments of type `Double`
data Circle = MkCircle Double Double Double

-- | A new type `Cuboid` with constructor `MkCuboid`
--   which takes three arguments of type `Double`
data Cuboid = MkCuboid Double Double Double
```

We use constructors to *build* values of the new type: 

```haskell
circ1 :: Circle 
circ1 = MkCircle 5 1 100  -- circle at (5,1) w/ radius 100

cub1 :: Cuboid
cub1 = MkCuboid 10 20 30  -- cuboid w/ len=10, dep=20, ht=30 
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

Suppose we create a new type with a `data` definition

```haskell
-- | A new type `Circle` with constructor `MkCircle`
data Circle = MkCircle Double Double Double
```

What is the **type of** the `MkCircle` _constructor_?

**A.** `MkCircle :: Circle`

**B.** `MkCircle :: Double -> Circle`

**C.** `MkCircle :: Double -> Double -> Circle`

**D.** `MkCircle :: Double -> Double -> Double -> Circle`

**E.** `MkCircle :: (Double, Double, Double) -> Circle`

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


### Aside: Record syntax

Haskell's **record syntax** allows you to *name* the constructor parameters:

  * Instead of

    ```haskell
    data Circle = MkCircle Double Double Double
    ```

  * you can write:

    ```
    data Circle = MkCircle { 
      center_x :: Double, 
      center_y :: Double,
      radius   :: Double  
    }
    ```
    
  * then you can do:
  
    ```
    circ1 = MkCircle { center_x = 5, center_y = 1, radius = 100 }
    -- same as: circ1 = MkCircle 5 1 100
    
    r = radius circ1 -- use field name as a function    
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

Suppose we have the definitions

```haskell
type CuboidT = (Double, Double, Double)
data Cuboid  = MkCuboid Double Double Double     

volume :: CuboidT -> Double
volume (l, d, h) = l * d * h
```

What is the result of

```haskell
>>> volume (MkCuboid 10 20 30)
```

**A.** `6000`

**B.** Type error

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

## Deconstructing Data

Constructors let us *build* values of new type ... but how to *use* those values?

How can we implement a function

```haskell
volume :: Cuboid -> Double
volume c = ???
```

such that

```haskell
>>> volume (MkCuboid 10 20 30)
6000
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


## Deconstructing Data by Pattern Matching

Haskell lets us *deconstruct* data via pattern-matching

```haskell
volume :: Cuboid -> Double
volume (MkCuboid l d h) = l * d * h

area :: Circle -> Double
area (MkCircle x y r) = pi * r * r
```

same as for tuples, just using different constructors!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
    
## Algebraic Data Types

1. *Type Synonyms:* naming existing types \[done\]

2. *Product Types:* bundling things together \[done\]

3. ***Sum Types:* types with multiple variants**

4. *Recursive Types:* types that contain themselves

5. *Polymorphic Datatypes:* datatypes with parameters

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Defining new types with `data` prevents us from mixing up values:

```haskell
area :: Circle -> Double
area (MkCircle x y r) = pi * r * r

>>> area (MkCuboid 10 20 30) -- TYPE ERROR!
```

But ... what if we _need_ to mix up values?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## A List of Shapes

Suppose I need to represent a *list of shapes*

- Some `Circle`s
- Some `Cuboid`s

What is the problem with `shapes` as defined below?

```haskell
shapes = [circ1, cub1]
```

Where we have defined

```haskell
circ1 :: Circle 
circ1 = MkCircle 5 1 100  -- circle at (5,1) with radius 100

cub1 :: Cuboid
cub1 = MkCuboid 10 20 30  -- cuboid with length=10, depth=20, height=30 
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

This does not type-check!

All list elements must have the _same_ type!

<br>
<br>
<br>
<br>

Solution?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Sum types

Lets create a _single_ type that can represent _both_ kinds of shapes!

```haskell
data Shape 
  = MkCircle Double Double Double   -- Circle at x, y with radius r 
  | MkCuboid Double Double Double   -- Cuboid with length, depth, height
```

A **sum type** is a data type with multiple constructors

  - aka **enum** in Rust
  - aka (tagged) **union** in C

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

With the definition:

```haskell
data Shape 
  = MkCircle Double Double Double   -- Circle at x, y with radius r 
  | MkCuboid Double Double Double   -- Cuboid with length, depth, height
```

What is the type of `MkCircle 5 1 100` ? 

**A.** `Shape`

**B.** `Circle`

**C.** `MkCircle`

**D.** `(Double, Double, Double)`

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
<br>
<br>
<br>
<br>


## List of Shapes: Take 2

Now we can define

```haskell
circ2 :: Shape
circ2 = MkCircle 5 1 100  -- circle at (5,1) with radius 100

cub2 :: Shape 
cub2 = MkCuboid 10 20 30  -- cuboid with length=10, depth=20, height=30 
```

and then define collections of `Shape`s

```haskell
shapes :: [Shape]
shapes = [circ2, cub2]
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

## 2D Shapes 

Lets define a type for 2D shapes

```haskell
data Shape2D 
  = MkRect Double Double -- rectangle with width and height
  | MkCirc Double        -- circle with radius
  | MkPoly [Vertex]      -- polygon with a list of vertices

type Vertex = (Double, Double)
```

Different constructors can have different types!

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

## Tagged Boxes 

A values of type `Shape2D` is either two doubles *tagged* with `Rectangle` 

```haskell
>>> :type (Rectangle 4.5 1.2)
(Rectangle 4.5 1.2) :: Shape
```

or a single double *tagged* with `Circle`

```haskell
>>> :type (Circle 3.2)
(Circle 3.2) :: Shape
```

or a list of pairs of d *tagged* with `Polygon`

```haskell
ghci> :type (Polygon [(1, 1), (2, 2), (3, 3)])
(Polygon [(1, 1), (2, 2), (3, 3)]) :: Shape
```

<br>
<br>
<br>

![Data values are **Tagged Boxes**](/static/img/lec4_boxed.png)


<br>
<br>
<br>
<br>

How do we get the value out of the box?

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

Write a function to compute the `perimeter` of a `Shape2D` 

```haskell
data Shape2D = MkRect Double Double | MkCirc Double | MkPoly [Vertex]
type Vertex = (Double, Double)

perimeter :: Shape2D -> Double
perimeter s = ???
```

**HINT**

You may want to use this helper that computes the distance between two points

```haskell
distance :: Vertex -> Vertex -> Double
distance (x1, y1) (x2, y2) = sqrt ((x2 - x1) ** 2 + (y2 - y1) ** 2)
```

<br>
<br>
<br>
<br>
<br>
<br>

## Algebraic Data Types

1. *Type Synonyms:* naming existing types \[done\]

2. *Product Types:* bundling things together \[done\]

3. *Sum Types:* types with multiple variants  \[done\]

4. ***Recursive Types:* types that contain themselves**

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

## Recursive types

Let's define **natural numbers** from scratch:

```haskell
data Nat = ???
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

```haskell
data Nat = Zero | Succ Nat
```

A `Nat` value is:

  * either an *empty* box labeled `Zero`
  * or a box labeled `Succ` with another `Nat` in it!
  
Some `Nat` values:  
  
```haskell
Zero                     -- 0
Succ Zero                -- 1
Succ (Succ Zero)         -- 2
Succ (Succ (Succ Zero))  -- 3
...
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

## Functions on recursive types

**Recursive code mirrors recursive data**


### 1. Recursive type as a parameter

```haskell
data Nat = Zero     -- base constructor
         | Succ Nat -- inductive constructor
```

**Step 1:** add a pattern per constructor

```
toInt :: Nat -> Int
toInt Zero     = ... -- base case
toInt (Succ n) = ... -- inductive case
                     -- (recursive call goes here)
```

**Step 2:** fill in base case:

```
toInt :: Nat -> Int
toInt Zero     = 0   -- base case
toInt (Succ n) = ... -- inductive case
                     -- (recursive call goes here)
```

**Step 3:** fill in inductive case using a recursive call:

```
toInt :: Nat -> Int
toInt Zero     = 0           -- base case
toInt (Succ n) = 1 + toInt n -- inductive case
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
let foo i = if i <= 0 then Zero else Succ (foo (i - 1))
in foo 2 
```

**A.**  Syntax error

**B.**  Type error

**C.**  `2`

**D.**  `Succ Zero`

**E.**  `Succ (Succ Zero)`

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


### 2. Recursive type as a result

```haskell
data Nat = Zero     -- base constructor
         | Succ Nat -- inductive constructor

         
fromInt :: Int -> Nat
fromInt n
  | n <= 0    = Zero                   -- base case
  | otherwise = Succ (fromInt (n - 1)) -- inductive case
                                       -- (recursive call goes here)
```

<br>
<br>
<br>
<br>
<br>
<br>


### 3. Putting the two together

```haskell
data Nat = Zero     -- base constructor
         | Succ Nat -- inductive constructor

         
add :: Nat -> Nat -> Nat
add n m = ???

sub :: Nat -> Nat -> Nat
sub n m = ???
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
data Nat = Zero     -- base constructor
         | Succ Nat -- inductive constructor

         
add :: Nat -> Nat -> Nat
add Zero     m = m              -- base case
add (Succ n) m = Succ (add n m) -- inductive case

sub :: Nat -> Nat -> Nat
sub n        Zero     = n       -- base case 1
sub Zero     _        = Zero    -- base case 2
sub (Succ n) (Succ m) = sub n m -- inductive case
```

Lessons learned:

* **Recursive code mirrors recursive data**
* With **multiple** arguments of a recursive type,
  which one should I recurse on?
* The name of the game is to pick the right **inductive strategy**!

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Lists

Lists aren't built-in! They are an *algebraic data type* like any other:

```haskell
data List = Nil           -- base constructor
          | Cons Int List -- inductive constructor
```

* List `[1, 2, 3]` is *represented* as `Cons 1 (Cons 2 (Cons 3 Nil))`

* Built-in list constructors `[]` and `(:)`
  are just fancy syntax for `Nil` and `Cons`

<br>
<br>

Functions on lists follow the same general strategy:

```haskell
length :: List -> Int
length Nil         = 0              -- base case
length (Cons _ xs) = 1 + length xs  -- inductive case
```

<br>
<br>
<br>
<br>
<br>

What is the right *inductive strategy* for appending two lists?


(I) lecture
    
    ```haskell
    append :: List -> List -> List
    append xs ys = ??
    ```
    
(I) final
    
    ```haskell
    append :: List -> List -> List
    append Nil ys = ys
    append (Cons x xs) ys = Cons x (append xs ys)    
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

## Trees

Lists are *unary trees*:

![Unary tree (aka list)](/static/img/list-tree.png){#fig:boxes .align-center width=50%}  


```haskell
data List = Nil | Cons Int List
```

<br>
<br>
<br>

How do we represent *binary trees*?

![Binary tree](/static/img/tree-data-node.png){#fig:boxes .align-center width=80%}  

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## QUIZ: Binary trees I

What is a Haskell datatype that can represent this *binary tree*?

![Binary tree](/static/img/tree-data-node.png){#fig:boxes .align-center width=50%}
  
**(A)** `data Tree = Leaf | Node Int Tree`

**(B)** `data Tree = Leaf | Node Tree Tree`

**(C)** `data Tree = Leaf | Node Int Tree Tree`

**(D)** `data Tree = Leaf Int | Node Tree Tree`

**(E)** `data Tree = Leaf Int | Node Int Tree Tree`

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

![Binary tree](/static/img/tree-data-node.png){#fig:boxes .align-center width=50%}

```haskell
data Tree = Leaf | Node Int Tree Tree

t1234 = Node 1 
          (Node 2 (Node 3 Leaf Leaf) Leaf) 
          (Node 4 Leaf Leaf)
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

<!--

BTW, answer D is also a binary tree, but with values stored in leaves!

![Leaf tree](/static/img/tree-data-leaf.png){#fig:boxes .align-center width=50%}

```haskell
data Tree = Leaf Int | Node Tree Tree

t12345 = Node
          (Node (Node (Leaf 1) (Leaf 2)) (Leaf 3))
          (Node (Leaf 4) (Leaf 5))
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>

-->


## Functions on trees

(I) lecture
    
    ```haskell
    height :: Tree -> Int
    height t = ??
    ```
    
(I) final
    
    ```haskell
    height :: Tree -> Int
    height Leaf = 0
    height (Node _ l r) = 1 + max (height l) (height r)
    ```

<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Example: Calculator

I want to implement an arithmetic calculator 
to evaluate expressions like:

* `4.0 + 2.9`
* `3.78 – 5.92`
* `(4.0 + 2.9) * (3.78 - 5.92)`

What is a Haskell datatype to *represent* these expressions?

```haskell
data Expr = ???
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
data Expr = Num Float
          | Add Expr Expr
          | Sub Expr Expr
          | Mul Expr Expr
```

<br>
<br>

How do we write a function to *evaluate* an expression?

(I) lecture

    ```haskell
    eval :: Expr -> Float
    eval e = ???
    ```
    
(I) final

    ```haskell
    eval :: Expr -> Float
    eval (Num f)     = f
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


That's all folks!
  