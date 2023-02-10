---
bookCollapseSection: true
title: "Type Classes"
weight: 20
---

A type class groups related functions and operators and allows for instances across the various datatypes which are members of the typeclass. Hence the function names within a type class are overloaded across the various type class members.

A typeclass declaration creates a type class. An instance declaration defines a datatype as belonging to a type class. A datatype may belong to zero or many type classes.

The Prelude package declares the following type classes:

| Bits            | Types that can be converted to bit vectors and back. |
|-----------------|------------------------------------------------------|
| Eq              | Types on which equality is defined.                  |
| Literal         | Types  which can be created from integer literals.   |
| RealLiteral     | Types  which can be created from real literals.      |
| Arith           | Types on which arithmetic operations are defined.    |
| Ord             | Types on which comparison operations are defined.    |
| Bounded         | Types with a finite range.                           |
| Bitwise         | Types on which bitwise operations are defined.       |
| BitReduction    | Types on which bitwise operations on a single        |
| BitExtend       | Types on which  extend operations are defined.       |
| SaturatingArith | Types with functions to  describe how overflow       |
| Alias           | Types which can be used interchangeably.             |
| NumAlias        | Types which give a new name to a numeric type.       |
| FShow           | Types which can convert a value to a Fmt             |
| StringLiteral   | Types which can be created around strings.           |