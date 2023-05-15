---
title: Tail Recursion
date: 2018-04-22
headerImg: books.jpg
---

## Recursion is...

Building solutions for *big problems*
from solutions for *sub-problems*

  - **Base case:** what is the *simplest version* of this problem and how do I solve it?
  - **Inductive strategy:** how do I *break down* this problem into sub-problems?
  - **Inductive case:** how do I solve the problem *given* the solutions for subproblems?
  
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Why use Recursion?

1. Often far simpler and cleaner than loops 

    - But not always...
    
2. Structure often forced by recursive data

2. Forces you to factor code into reusable units (recursive functions)
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>    
    
## Why **not** use Recursion?

1. Slow

2. Can cause stack overflow

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>    

## Example: factorial

```haskell
fac :: Int -> Int
fac n
  | n <= 1    = 1
  | otherwise = n * fac (n - 1)
```

<br>
<br>

Lets see how `fac 4` is evaluated:

```haskell
<fac 4>
  ==> <4 * <fac 3>>              -- recursively call `fact 3`
  ==> <4 * <3 * <fac 2>>>        --   recursively call `fact 2`
  ==> <4 * <3 * <2 * <fac 1>>>>  --     recursively call `fact 1`
  ==> <4 * <3 * <2 * 1>>>        --     multiply 2 to result 
  ==> <4 * <3 * 2>>              --   multiply 3 to result
  ==> <4 * 6>                    -- multiply 4 to result
  ==> 24
```
<br>
<br>

Each *function call* `<>` allocates a frame on the *call stack*

  - expensive
  - the stack has a finite size
  
Can we do recursion without allocating stack frames?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Tail Recursion

No computations allowed on recursively returned value 

  - i.e. value returned by the recursive call == value returned by function
  
<br>
<br>

### QUIZ: Is this function tail recursive? 

```haskell
fac :: Int -> Int
fac n
  | n <= 1    = 1
  | otherwise = n * fac (n - 1)
```

**A.** Yes

**B.** No

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

## Tail recursive factorial

Let's write a tail-recursive factorial!

(I) lecture

    ```haskell
    facTR :: Int -> Int
    facTR n = ... 
    ```
    
(I) final

    ```haskell
    facTR :: Int -> Int
    facTR n = loop 1 n
      where
        loop :: Int -> Int -> Int
        loop acc n
          | n <= 1    = acc
          | otherwise = loop (acc * n) (n - 1)
    ```      
      
<br>
<br>
<br>
<br>    

Lets see how `facTR` is evaluated:


```haskell
<facTR 4>
  ==>    <<loop 1  4>> -- call loop 1 4
  ==>   <<<loop 4  3>>> -- rec call loop 4 3 
  ==>  <<<<loop 12 2>>>> -- rec call loop 12 2
  ==> <<<<<loop 24 1>>>>> -- rec call loop 24 1
  ==> 24                  -- return result 24! 
```

Each recursive call **directly** returns the result 

  - without further computation

  - no need to remember what to do next!
  
  - no need to store the "empty" stack frames!
    
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>  

## Why care about Tail Recursion?

Because the _compiler_ can transform it into a _fast loop_

```haskell
facTR n = loop 1 n
  where
    loop acc n
      | n <= 1    = acc
      | otherwise = loop (acc * n) (n - 1)
```

<br>

```javascript 
function facTR(n){ 
  var acc = 1;
  while (true) {
    if (n <= 1) { return acc ; }
    else        { acc = acc * n; n = n - 1; }
  }
}
```

- Tail recursive calls can be optimized as a **loop**

    - no stack frames needed! 

- Part of the language specification of most functional languages

    - compiler **guarantees** to optimize tail calls

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


That's all folks!