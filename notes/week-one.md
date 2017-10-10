# Week One

## Compiler vs. Interpreter

### Interpreter

Interpreters are "on-line," meaning that they take in a program and some data and then run the resulting, interpreted code.

### Compiler

Compilers are "off-line." Instead of instantly running an interpretation of a given program, it generates an executable translation of the program.
This executable, in turn, can then be run in the appropriate manner at a later time.

## History

The first compiler was written for Fortran I.
Memory usage was a problem at the time.
John Backus imagined a system where formulas would be translated into efficient code, hence the name of the language.
He wrote the compiler for Fortran 1953-1957.
By 1958, over 50% of all code was Fortran code.

## Steps of Compilation

### Lexical Analysis

The initial goal is to tokenize the code.

### Parsing

Identify the role of each of the tokens.
Take an example of `if x == y then z = 1; else z = 2;`.

This is parsed as:

* Predicate
  * Relation
    * x == y
* Then Statement
  * Assignment
    * z => 1
* Else Statement
  * Assignment
    * z => 2

### Semantic Analysis

Determining the meaning of the parsed input is too hard to do.
Obviously, we still don't understand how humans determine meaning.

Compilers do relatively little semantic analysis.
Typically, this includes type-checking and variable scoping.

### Optimization

We then want to modify the program to increase either run-time or memory efficiency.
For instance, we can ensure that certain assignments (i.e. a number times zero) is just assigned to zero.
This reduces the total number of computations required.

### Code Generation

Lastly, we need to take the optimized code an translate it into a new language.
Typically, this is assembly code, but sometimes it can be byte code or higher level languages.

## Compilers Today vs. Past

If we were to look at Fortran's compiler, we would expect to see a sizable lexical analysis and parsing phases, a meager semantic analysis phase, and involved optimization and code generation phases.

Today's compilers have very little lexical analysis and parsing, thanks to helpful tools, involved semantic phase, extremely involved optimization phase, and very little at the code generation phase.
