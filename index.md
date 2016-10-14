---
layout: default
title: Unseemly
---

_Unseemly is still missing some vital pieces. This whole description is not yet true._

Unseemly is (to my knowledge) the first typed macro-based language.

Types are great! Macros are great! But they have historically not played well together.

# Typed languages and macro-based languages

I like to divide the design of programming languages into two main families.
It's not the only valid taxonomy, 
 but it appeals to me.

One family, the typed languages,
 includes the MLs and Haskell, as well as C++, Java, Rust, and so on.
Programmers in those languages use type systems
 to both describe data they are interested in and to express invariants.

The other, smaller, family is macro-based languages.
These are mostly direct descendants of Lisp: Scheme, and Racket.
(If you squint, the dynamic metaprogramming systems of Ruby and JavaScript
 make them part of the family, too.)
Programmers in those languages use metaprogramming to 
 abstract over surface syntax, control flow, and binding.

But if you write in a typed language, 
 you almost certainly hear the advice to use the macro system sparingly,
  if at all.
And Lisps (usually) lack a type system altogether.

This is because type errors in macro-generated code 
 are incredibly difficult to understand.

Type errors are the user interface of a typed language;
 the primary purpose of types is to produce useful error messages.

Rule that I just made up:
 If the programmer is responsible for generated code,
  code generation is not an abstraction.

# Unseemly

(Here's where I start making bold claims,
  which could all be undone 
   if there's a theoretical error in Unseemly's design.
 But the vast majority of Unseemly
  is stolen from existing languages or at least existing research.)

In Unseemly, macros always generate typesafe code,
 so macros feel solid, like Scheme macros,
  and the typechecker can only complain about the code you wrote.

Unseemly's macro system is procedural, hygienic, and has access to syntax quotation.

Unseemly's type system is algebraic, sound (I hope!), and has access to pattern-matching.

## Don't program in Unseemly!

Unseemly might have sophisticated type and macro systems,
 but it has _almost nothing_ else.
You don't want to program in it.

Instead, implement the language you've always been meaning to make
 in Unseemly's macro system.

Don't worry if you don't want a languague with macros;
 you can unbind the macro-definition construct.
And don't worry if you don't want types;
 untyped languages invisibly use the `Any` type everywhere.

There are two reasons that you should use Unseemly as your core language:

### Macros make easy compilers easy, and hard compilers possible

A lot of language forms are possible to write 
 as simple pattern-matching transformers,
  which are easy to write and easy to understand.

Unseemly lets you write pattern-matching macros
  when they're appropriate,
 and arbitrary code generation when it's necessary.

The two systems smoothly transition into each other,
 which is a trick picked up from Racket.

### Most libraries can be shared between Unseemly languages

If you write a library in one Unseemly-backed language,
 in most cases, anyone in any other Unseemly-backed language
  will be able to use your library
   as if it were written in their language.
   
This is why Unseemly's type system looks like the type system of a "real" language;
 many libraries are just a bunch of functions with types.
If the type systems are the same, libraries can be language-agnostic.

Dynamically-typed languages get to use statically-typed libraries,
 and vice-versa.
 

