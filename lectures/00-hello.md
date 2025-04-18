---
title: Hello, world!
headerImg: sea.jpg
date: 2025-03-31
---

## Who am I?

![Nadia Polikarpova](https://cseweb.ucsd.edu/~npolikarpova/images/nadia_polikarpova.jpg){#fig:nadia .align-center width=25%}

- Associate professor
- UCSD CSE since 2017
- PhD at ETH Zurich
- Postdoc at MIT

### My Research

- *In the past:* Program Verification: how to *prove* the program is doing the right thing?
- Program Synthesis: how to *generate* a program that does the right thing?
- Usability of AI assistants: how do programmers use AI assistants and how can we make them better?

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## The Crew

### Teaching Assistants

* [Emmanuel Anaya González](https://eanayag.com/)
* [Ilana Shapiro](https://ilanashapiro.github.io/)

### Tutors

* Alexander Zhang
* Shaurya Raswan
* Hisham Baobaid

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Lecture Format

- In person
- Recordings available on [podcast](https://podcast.ucsd.edu/)

- I do not take attendance, you are adults
- but: you are expected to have attended or watched all previous lectures before asking questions on Piazza or OH


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



## A Programming Language

### Two Variables

- `x`, `y`

### Three Operations

- `x++`
- `x--`
- `x = 0 ? L1 : L2`


<br>
<br>
<br>
<br>
<br>
<br>


## Example Program

(What _does_ it do?)

```
L1: x++
    y--
    y = 0 ? L2 : L1
L2: ...
```


<br>
<br>
<br>
<br>
<br>
<br>

## The above language is "equivalent to" every PL!

(See [wikipedia](https://en.wikipedia.org/wiki/Counter_machine))

But good luck writing

- QuickSort
- Fortnite
- Spotify

<br>

**Two lessons:**

- All Programming Languages share something fundamental
- But also: your Programming Language shapes your Programming Thought

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

## What is CSE 130 **not** about?

Learning...

- JavaScript in March
- Haskell in April
- C++ in May
- etc.

### New languages come (and go ...)

There was no

- Python      before 1991
- Java        before 1995
- C#          before 2000
- Rust        before 2006
- WebAssembly before 2015


<br>
<br>
<br>
<br>
<br>
<br>

## What is CSE 130 about?

- Concepts in programming languages
- Language design and implementation

<br>
<br>
<br>
<br>
<br>
<br>


## Course Goals

![130 Brain](https://ucsd-cse130.github.io/wi20/static/img/galaxy-brain-130.jpg){#fig:morpheus .align-center width=40%}

> Free Your Mind.



<br>
<br>
<br>
<br>
<br>
<br>

## Goal: Learn the Anatomy of PL

![Anatomy](/static/img/anatomy.png){#fig:anatomy .align-center width=20%}


- What makes a programming language?
- Which features are **fundamental** and which are **syntactic sugar**?





<br>
<br>
<br>
<br>
<br>
<br>


## Goal: Learn New Languages / Constructs

![Musical Score](/static/img/music-score.png){#fig:music .align-center width=30%}

New ways to **describe** and **organize** computation,
to create programs that are:

- **Correct**
- **Readable**
- **Extendable**
- **Reusable**



<br>
<br>
<br>
<br>
<br>
<br>






## Goal: How to Design new Languages

New hot languages being designed in industry as we speak:

- Hack, Flow, React @ Facebook
- Rust @ Mozilla (now The Rust Foundation)
- TypeScript @ Microsoft
- Swift @ Apple
- WebAsssembly @ Google + Mozilla + Microsoft

Buried in every large system is a (domain-specific) language

- DB: SQL
- Word, Excel: Formulas, Macros, VBScript
- Emacs: LISP
- Latex, shell scripts, build scripts (makefiles), ...

If you work on a large system, you **will** design a new PL!



<br>
<br>
<br>
<br>
<br>
<br>


## Course Syllabus

- Lambda calculus (2 weeks)
    - The simplest language on Earth
- Haskell (3 weeks)
    - A practical functional language
- Build your own language (5 weeks)
    - How do we implement a new language (in Haksell)?
    - Fun project: how do we make LLMs generate code in our language?


<br>
<br>
<br>
<br>
<br>
<br>


## **QuickSort** in C

```c
void sort(int arr[], int beg, int end){
  if (end > beg + 1){
    int piv = arr[beg];
    int l = beg + 1;
    int r = end;
    while (l != r-1)
       if(arr[l] <= piv) l++;
       else swap(&arr[l], &arr[r--]);
    if(arr[l]<=piv && arr[r]<=piv)
       l=r+1;
    else if(arr[l]<=piv && arr[r]>piv)
       {l++; r--;}
    else if (arr[l]>piv && arr[r]<=piv)
       swap(&arr[l++], &arr[r--]);
    else r=l-1;
    swap(&arr[r--], &arr[beg]);
    sort(arr, beg, r);
    sort(arr, l, end);
  }
}
```



<br>
<br>
<br>
<br>
<br>
<br>

## **QuickSort** in Haskell

```Haskell
sort []     = []
sort (x:xs) = sort ls ++ [x] ++ sort rs
  where
    ls      = [ l | l <- xs, l <= x ]
    rs      = [ r | r <- xs, x <  r ]
```

(not a wholly [fair comparison...](http://stackoverflow.com/questions/7717691/why-is-the-minimalist-example-haskell-quicksort-not-a-true-quicksort))



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


## Course Logistics

- [webpage](https://nadia-polikarpova.github.io/cse130-web)
    - calendar, lecture notes, programming assignments, ...
- [piazza](https://www.piazza.com/ucsd/spring2025/cse130/home)
    - announcements and discussions
- [gradescope](https://www.gradescope.com/courses/1012632) (code: X2GEG5)
    - homework/exam submissions, grades
- [classquestion](https://classquestion.com/students) (code: GXJVJ)
    - anonymous questions during lecture


<br>
<br>
<br>
<br>
<br>
<br>

## Grading

- 50% Homework assignments
- 20% Project (aka "the last assignment")
- 15% Midterm quiz
- 15% Final quiz
- 05% Extra credit for Piazza discussion
    - To **top 20** best participants

<br>
<br>
<br>
<br>
<br>
<br>

## Assignments

- 5 programming assignments
- Released [online](https://nadia-polikarpova.github.io/cse130-web/assignments.html), at least a week before due date
- Due at **11:59pm**
    - Usually on a **Wednesday**, but not always, check the calendar
    - No assignment Weeks 1 and 2
- 8 late days, no more than 4 late days per assignment
    - used atomically (5 mins late = 1 late day)
- Submitted via Gradescope
    - submission instructions in the assignment
- Solve **alone or in groups of two**
    - user this [piazza thread](https://piazza.com/class/m8usoopybn1t8/post/5) to find a partner

<br>
<br>
<br>
<br>
<br>
<br>

## Project

- Bigger and more open-ended than the other assignments
- You will work in groups of 2-3
- You are allowed (and even encouraged) to use AI assistants (e.g., Copilot, ChatGPT) in your project,
    but you must understand (and will be asked to explain) the code you submit.
- More details coming soon

<br>
<br>
<br>
<br>
<br>
<br>

## Exams

- Midterm on *May 5*
- Final: *June 6*
- Gradescope multiple choice
- Individual
- Done during class


<br>
<br>
<br>
<br>
<br>
<br>

## In-class Quizzes

We will do quizzes in class via ClassQuestion

  - Make class interactive
  - Help *you* and *me* understand what's tricky

**Protocol**

1. *Discuss*
    - I show the quiz on screen
    - Discuss with your neighbor
    - Try to reach consensus

3. *Vote*
    - On the ClassQuestion website

4. *Class Discuss*
    - What was easy or tricky?

<br>
<br>
<br>
<br>
<br>
<br>


## TEST QUIZ

How can you earn 5% extra credit for this class?

- **A**  do all my homework alone

- **B**  answers other students' questions on Piazza

- **C**  submit all my homework on due date (without using late days)

- **D**  post snarky comments on zoom chat during lecture


<br>
<br>
<br>
<br>
<br>
<br>


## Your Resources

- Discussion section: Mon 1pm
    - PCYNH 106
- Office hours
    - every day, check calendar
- Piazza
    - we answer during work hours
- **No text**
    - online lecture notes and links



<br>
<br>
<br>
<br>
<br>
<br>






## Academic Integrity

**General policy:** everything you submit for a grade should be your own work, or else you should cite the source

For details, please see academic integrity statement on the website

For programming assignments **do not**:

  - search or post on Chegg, StackOverflow etc
  - try to find solutions from previous years

For exams **additionally do not**:

  - use AI assistants
  - communicate with your classmates during the exam
  - try to find past exams that are not publicly posted

Policy on using AI assistants (e.g., Copilot, ChatGPT):
  - using AI assistants is allowed for coding (not quizzes), as long as you acknowledge them in your submission
  - you are responsible for understanding *all the code* you submit
  - in the final project, you *are encouraged* to use AI assistants
  - in the assignments, you *are discouraged* to use AI assistants because the purpose
    of the assignments is to practice new coding skills on simple problems




<br>
<br>
<br>
<br>
<br>
<br>


## Students with Disabilites

Students requesting accommodations for this course due to a disability or current functional limitation must provide
a current **Authorization for Accommodation (AFA)** letter issued by the Office for Students with Disabilities (OSD).

Students are required to present their AFA letters to Faculty (please make arrangements to contact me privately)
and to the CSE OSD Liaison in advance so that accommodations may be arranged.

<br>
<br>
<br>
<br>
<br>
<br>


## Diversity and Inclusion

**Goal**

- Create a diverse and inclusive learning environment
- Where all students feel comfortable and can thrive
- If there is a way we can make you feel more included, please let one of the course staff know

**Expectations**

- We expect that you will honor and respect your classmates
- Abide by the UCSD [Principles of Community](https://ucsd.edu/about/principles.html)
- Understand that others’ backgrounds, perspectives and experiences may be different than your own
- Help us to build an environment where everyone is respected and feels comfortable.

If you experience any sort of **harassment or discrimination**, please contact the [Office of Prevention of Harassment and Discrimination](https://ophd.ucsd.edu/).
Students may receive confidential assistance at the [Sexual Assault Resource Center](http://care.ucsd.edu) at (858) 534-5793
or [Counseling and Psychological Services](http://caps.ucsd.edu.) (CAPS) at (858) 534-3755.


<br>
<br>
<br>
<br>
<br>
<br>




