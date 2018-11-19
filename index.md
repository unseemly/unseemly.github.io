---
layout: default
title: Unseemly
---

_Unseemly is still missing some vital pieces; this is based on what it should become._

Unseemly is (to my knowledge) the first typed macro-based language.

I want to use types and macros, but they have historically not played well together.

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

Languages descended from ML often have names with "ML" in them.
Languages descended from Scheme often have names suggesting nefarious behavior.
Unseemly is both a Scheme and an ML.

*(Here's where I start making bold claims,
  which could all be undone 
   if there's a theoretical error in Unseemly's design.
 But the vast majority of Unseemly
  is stolen from existing languages or at least existing research.)*

In almost any language with a type system,
 type errors don't expose the details of the functions you're trying to invoke.
In Unseemly, the same is true of macros!
Like in Scheme, user-defined macros feel solid, as if they were built in to the language.

Unseemly's macro system is procedural, hygienic, 
 and has access to syntax quotation,
  just like Scheme's.

Unseemly's type system is algebraic, sound (I hope!), 
 and has access to pattern-matching,
  just like the ML's.

## Don't program in Unseemly!

Unseemly might have sophisticated type and macro systems,
 but it has _almost nothing_ else.
It's missing a lot of convenience features,
 and you don't want to live without them.

Instead, try adding those convenience features to Unseemly;
 it's easier than living without them.
Come up with a better syntax, too; it's easy.
In other words, implement the language you've always been meaning to make
 as a set of Unseemly macros.

Not just if you want a typed macro language
 (though the world needs a good one, and Unseemly isn't it).
But use Unseemly because it's a great way to write a compiler.

There are two reasons that you should use Unseemly as your core language:

### Macros make easy compilers easy, and hard compilers possible

Writing a simple macro in C is easy,
 as long as you ignore all of the jankiness of the C macro system.
 
You can get something almost as simple, and about 0% as janky,
 if your language has syntax quotation.
 
Unseemly's macro system is procedural.
However, syntax quotation means that writing pattern-match macros
 (like `syntax-rules` in Scheme or `macro_rules!` in Rust)
 is a piece of cake, 
  and adding a "little bit of proceduralness" is straightforward.
(This is like how, in Scheme, `syntax-rules` is just a
 thin wrapper around the `syntax-case` procedural macros.)

### Most libraries can be shared between Unseemly languages

If you write a library in one Unseemly-backed language,
 in most cases, anyone in any other Unseemly-backed language
  will be able to use your library
   as if it were written in their language (i.e., without an FFI).
(This is like the relationship between Clojure and Java.)
   
This is why Unseemly's type system looks like
 the type system of a "real" language;
  many libraries are just a bunch of functions with types.
If the type systems are shared, libraries can be language-agnostic.

Dynamically-typed languages get to use statically-typed libraries,
 and vice-versa.
 
## Compilers aren't that hard

Compilers have a reputation for being hard to write. This is basically wrong.

It's true that writing everything from the tokenizer to the assembly code generator
 for a complex language without using any outside libraries
 is a huge undertaking.
 
But that's the wrong comparison; it puts assembly language on an unearned pedestal.
If you're a programmer in some language, 
 you can already write a [transpiler] to that language (instead of assembly),
  if you were willing to spend a month or two mucking around with strings.
(A transpiler is a compiler written by someone who wanted to use it for something, 
  rather than to keep working on it forever.)

[transpiler]: http://composition.al/blog/2017/07/31/my-first-fifteen-compilers/

Unseemly doesn't exist to make writing compilers *easier*; it's already not that hard.
Unseemly makes it less tedious (with type-safe syntax quotation),
 and gives you more of the goodies (parse errors, type errors)
  that you shouldn't have to reimplement.
Like, in order to implement Unseemly,
 I needed to write a fairly complicated typechecker.
I'm not an expert in types, so I just copied the rules out of [the brick wall book].
No reason not to, really.

[the brick wall book]: https://www.cis.upenn.edu/~bcpierce/tapl/

Unseemly is meant for language implementation,
 but all that means is that you can get to the good stuff faster.
