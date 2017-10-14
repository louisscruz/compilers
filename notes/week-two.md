# Lexical Analysis and Finite Automata

## Lexical Analysis

The goal of lexical analysis is to classify substrings according to their roles and then communicate those roles to the parser.

### Intro

```
if (i == j)
  z = 0;
else
  z = 1;
```

The above code would likely be read as follows:

`\tif (i == j)\n\tz = 0;\n\telse\n\t\t\z = 1;`

The lexical analyzer will go through and determine where dividers should be placed.

#### Token Classes

Examples of these are:

* Identifiers
* Keywords
* `(`
* `)`
* Numbers

Token classes correspond to sets of strings.

##### Identifiers

These are string of letters or digits, starting with a letter.

##### Integers

These are non-empty strings of digits.
That includes things like `01` and `00`.

##### Keywords

These include `else`, `if`, `begin`, etc.

##### Whitespace

These include non-empty sequences of blanks, newlines, and tabs.

### Tokens

Tokens are pairs (tuples) of this structure:

`<Class, string>`

The lexical analyzer sends these tokens along to the parser.

`\tif (i == j)\n\t\tz = 0;\n\telse\n\t\t\z = 1;`

This would be read as:

* `\t`: whitespace
* `if`: keyword
* ` `: whitespace
* `(`: (
* `i`: identifier
* ` `: whitespace
* `==`: operator
* ` `: whitespace
* `j`: identifier
* `)`: )
* `\n\t\t`: whitespace
* `z`: identifier
* ` `: whitespace
* `=`: =
* ` `: whitespace
* `0`: number
* `;`: ;
* `\n\t`: whitespace
* `else`: keyword
* `\n\t\t`: whitespace
* `z`: identifier
* ` `: whitespace
* `=`: =
* ` `: whitespace
* `1`: number
* `;`: ;

### Examples

#### FORTRAN

**FORTRAN rule**: whitespace is irrelevant.

`DO 5 I = 1,25`

In this example, we have a `DO` loop, where it is given the ID of 5 (to know what is inside of the loop, there is a closing keyword with the same ID).
I is to lie within the range of one and twenty-five.

`DO 5 I = 1.25`

In this example, we have a variable assignment.
`DO5I=1.25` is how it will be analyzed.
The reason for this is that the period negates the possibility of iterating over a range.
The analyzer would have to use `lookahead` to see if we do in fact have a `DO` keyword.

Lookahead is always needed, but the goal is to minimize its use.

#### PL/1

Programming Language 1 designed by IBM.

In this language, keywords are not reserved.

`IF ELSE THEN THEN = ELSE; ELSE ELSE = THEN`

`DECLARE (ARG1,...,ARGN)`

In this case, it's possible that `DECLARE` is either a keyword or a reference to an array.

Because there are any number of arguments in this, we require unbounded lookahead.

#### C++

`Foo<Bar>`

vs.

`cin >> var`

How should the lexical analyzer handle the case of: `Foo<Bar<Bazz>>`
At one point in time, an error would arise, and the only fix was to insert a whitespace as so: `Foo<Bar<Bazz> >`

### Regular Languages

#### Intro

Regular expressions are used to define the regular languages.
Each regular expression denotes a set.

For example, the single character `'c'` denotes a set where the language includes a single string of `c`.
Epsilon is a special language that just contains an empty string.
Epsilon is not an empty set.

##### Operations

###### Union

A + B => { a | a is a member of A } U { b | b is a member of B }

###### Concatenation

AB => { ab | a is a member of A ^ b is a member of B }

###### Iteration

A* => A concatenated with itself i times

A^0 = epsilon

#### Examples

##### First Example

Sigma = { "0", "1" }

What is the language 1*?

This is the union of 1 concatenated with itself an infinite number of times.

1* = { "", "1", "11", "111", ... }

##### Second Example

("1" + "0")"1" = { ab | a is a member of "1" + "0" ^ b is a member of { "1" } } = { "11", "01" }

##### Third Example

What is the language of 0* + 1*?

0* + 1* = { 0^i | i >= 0 } U { 1^i | i >= 0 }

##### Fourth Example

What is the language of (0 + 1)* ?

(0 + 1)* = union of (0 + 1)^i up to i

This means that we can have { "", "0", "00", "01", ...., "10",...}.
This is essentially sigma star.

## Formal Languages

Both alphabets and languages are important in lexical analysis.

The previous section seemingly conflates expressions with sets.
In actuality, it is the case that:

```
L: Expression => Set
```

The above is called a "meaning function."
This means that the proper sets are defined as:

```
L(epsilon) = { "" }
L('c') = { "c" }
L(A + B) = L(A) U L(B)
L(AB) = { ab | a is a member of L(A) ^ b is a member of L(B) }
L(A*) = Union of L(A^i) for all i >= 0
```

Generally speaking, there are many more expressions than there are meanings.
There are many ways to illustrate the same meaning.

It's a good idea to separate syntax from semantics.
If you hold that there is an underlying idea behind syntax, you know that there are many ways to show the same concept.
Also, some syntactic systems are more apt at allowing for different modes of thought.

Meaning is many to one, not one to many.

## Lexical Specifications

### Keywords

To create "if" and "else," we could show it as follows:

```
'i''f' + 'e''l''s''e'
```

Shorthand for this is:

```
'if' + 'else'
```

### Integers

```
'0' + '1' + '2' + '3' + '4' + '5' + '6' + '7' + '8' + '9'
```

There is typically some kind of helper to denote this set.

```
digit
```

Note that `digit*` includes the empty string, so to select any number of repeating digits, we must use `digitdigit*`.
The shorthand for this is `digit+`.

### Letters or Digits, Starting with a Letter

Character ranges are shown as:

```
[a-zA-Z]
```

This defines the union of all lower and uppercase letters.

To get to a letter followed by letters or digits:

```
letters(letters + digits)*
```

### Whitespace

` ` is a single space.
`\n` is a new line.
`\t` is a tab.

```
(' ' + '\n' + '\t')
```

### Pascal Example

```
num = digits opt_fraction opt_exponent
opt_fraction = ('.'digits) + epsilon
opt_exponent = ('E'('+'+'-'+epsilon)digits)+epsilon
```

Using standard optional syntax, these could also be shown as:

```
opt_fraction = ('.'digits)?
opt_exponent = ('E'('+'+'-')?digits)?
```

### Review

```
A+ = AA*
A | B = A + B
'a' + 'b' + ... + 'z' = [a-z]
complement of [a-z] = [^a-z]
```

### Maximal Munch

If parsing `==`, should we consider it to be `=` and `=` or `==`?
The rule that specifies that we should always take the longest possibility is called "maximal munch."

### Prioritized Tokenization

`if` is technically a possible keyword and a possible identifier.
One way of handling this is to choose whichever it is first.
Keywords have higher priority over identifiers.

### Error Handling

Use an expression that is equal to all strings not in the lexical specification.
Put this error handling last.
This pattern prevents the compiler from failing.
