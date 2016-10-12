---
layout: default
title: Unseemly
---

_Unseemly is still missing some vital pieces. The text below is not yet true._

Unseemly is (to my knowledge) the first typed macro-based language.

Types are great! Macros are great! But they have historically not played well together.

### Typed languages and macro-based languages

I like to divide the design of programming languages into two main threads. 
There are other, no-less-valid, ways of looking at them,
 but this one appeals to me.

One thread, the family of typed languages 
 includes the MLs and Haskell, as well as C++, Java, Rust, and so on.
Programmers in those languages use type systems
 to both describe data they are interested in and to express invariants.

The other, smaller, thread is macro-based languages.
These are mostly direct descendants of Lisp: Scheme, and Racket, etc.
(If you squint, the dynamic metaprogramming systems of Ruby and JavaScript
 make them part of the family, too.)
Programmers in those languages use metaprogramming to 
 abstract over surface syntax, control flow, and binding.

But if you write in a typed language, 
 you almost certainly hear the advice to use the macro system sparingly,
  if at all.
And Lisps (usually) lack a type system altogether.
Why?
Type errors in macro-generated code 
 are incredibly difficult to understand.

This is no small issue.
Type errors are the user interface of a typed language;
 the primary purpose of types is to produce useful error messages.

Rule that I just made up:
 If the programmer is responsible for generated code,
  code generation is not an abstraction.

### Unseemly

(Here's where I start making bold claims,
  which could all be undone 
   if there's a theoretical error in Unseemly's design.
 But the vast majority of Unseemly
  is stolen from existing languages or at least existing research.)

In Unseemly, macros always generate typesafe code,
 so macros feel solid, like Scheme macros,
  and the typechecker can only complain about the code you wrote.

And Unseemly doesn't cheap out on the macro or type systems!
The macro system is procedural, hygienic, and has access to syntax quotation.
The type system is algebraic, sound (I hope!), and has access to pattern-matching.

If I'm making this sound magic, it's not; 
 it relies on the macro definitions being well-typed,
  just like code normally relies on libraries being well-typed.
Well, there's a lot of technical stuff behind-the-scenes,
 but, like I said, it's mostly stolen.
 
