---
layout: default
title: Unseemly
---

*Unseemly has all of its core features, but it's still a long way from being practical. There's a still a lot of stuff to implement and iron out.*

Unseemly is the first language able to safely typecheck all macros before expansion.

Typically, **macro-based languages** are untyped,  
 and programmers in **typed languages** are rightly reluctant to use macros,  
  because macros can make type errors incomprehensible.  
In Unseemly, the code that macros generate is automatically typesafe,  
 as long as the code the programmer writes passes typechecking,  
  so it can have the best of both worlds.  
(This has historically been difficult,  
  but recent research has cleared a path.)  

If you want to implement a typed language, and the types are pretty normal,  
 you can write the whole language as Unseemly macros.  
Not only is that faster than writing the language from scratch  
 (you get the typechecker for free!),  
 but Unseemly-based languages get to share libraries and even some tooling.  
(Tools like text editor support and a REPL should be shareable.)

Unseemly is utterly barebones right now,  
 but it has everything you need to grow a language.  

# Typed languages and macro-based languages

I like to divide the design of programming languages into two main families.  
It's not the only valid taxonomy,  
 but it appeals to me.  

One family, the **typed languages**,  
 includes the MLs and Haskell, as well as C++, Java, Rust, and so on.  
Programmers in those languages use type systems  
 both to describe data they are interested in and to express invariants.  

The other, smaller, family is **macro-based languages**.  
These are mostly direct descendants of Lisp, like Scheme and Racket.  
(If you squint, the dynamic metaprogramming systems of Ruby and JavaScript  
 make them cousins of this family)  
Programmers in those languages use metaprogramming to  
 abstract over surface syntax, control flow, and binding.  

But if you write in a typed language,  
 you almost certainly hear the advice to use the macro system sparingly.  
And Lisps (usually) lack a type system altogether.  

While type errors respect *function* boundaries,  
 as soon as a *macro* is involved,  
  you have to wade through the macro-generated code  
   to figure out why the macro went wrong,  
  even if the error had nothing to do with the macro!  
And type errors are the user interface of a typed language;  
 they have to be comprehensible!  

If the type system holds you responsible for generated code,  
 code generation is not an abstraction.  

# Unseemly is a typed macro language

Languages descended from ML often have names with "ML" in them.  
Languages descended from Scheme often have names suggesting improper behavior.  
Unseemly is both a Scheme and an ML.  

In Unseemly, macros have types!  
Type errors respect macros they same way they respect functions,  
 so macros feel like part of the language.  

Unseemly's macro system is procedural, hygienic,  
 and has access to syntax quotation,  
  just like Scheme's.  

Unseemly's type system is algebraic, generic,  
 and has access to pattern-matching,  
  just like ML's.  

Almost everything about Unseemly has stolen  
 from older, more respectable languages.  
The one new thing, which Unseemly needs to make macro types work,  
 is called **binding annotations**.  
When you define a macro that binds names (like a lambda or a let),  
 you have to specify in the syntax what the binders are and where they're bound.  

## Macro languages are extensible compilers

Macros are an ergonomic way to specify source-to-source translations.  
In other words, they're a great way to write a compiler.  

In macro-based languages,  
 the language is typically composed of a small set of "core" forms,  
 and almost every language feature the user interacts with  
  is a macro that expands to those forms.  
Language features can be built layer-by-layer, and, since they live in libraries,  
 they can be versioned separately from the core language.  

### Aside: Compilers aren't that hard

Compilers have a reputation for being hard to write. This is basically wrong.  

It's true that writing everything from the tokenizer to the assembly code generator  
 for a complex language without using any outside libraries  
 could take years.  

But that calculation puts assembly language on an unearned pedestal.  
If you're a programmer,  
 you can already write a [compiler] to some normal language (instead of assembly),  
  if you're willing to spend a month or two mucking around with strings.  

[compiler]: http://composition.al/blog/2017/07/31/my-first-fifteen-compilers/

Unseemly (like other macro-based languages)  
 doesn't exist to make writing compilers *easier*; it's already not that hard.  
Unseemly makes it less tedious (with type-safe syntax quotation),  
 and gives you more of the goodies (type checking, pattern-matching, parsing)  
  that you shouldn't have to reimplement.  
For example, in order to implement Unseemly,  
 I needed to write a fairly complicated typechecker.  
I'm not an expert in types,  
 so I just copied the rules out of [the brick wall book].  
Now I'm a non-expert with a typechecker, and with Unseemly, you can be, too!  

[the brick wall book]: https://www.cis.upenn.edu/~bcpierce/tapl/

## Most libraries can be shared between Unseemly languages

If you write a library in one Unseemly-backed language,  
 in most cases, programmers other Unseemly-backed language  
  will be able to use your library without a foreign function interface.  
(This is like the relationship between Clojure and Java.)  

This is why Unseemly's type system is more normal-looking  
 than the rest of the language;  
  library users should be able to read type signatures.  
With a shared type systems, libraries can be language-agnostic.  

## Inline language-switching

Because Unseemly macros (and their associated changes to syntax) are scoped,  
 it's possible for multiple languages to coexist on equal footing in the same file.  

Using syntax quotation rather than strings to embed code  
 prevents problems like SQL injection  
 and means that the Unseemly auto-formatter (uh, once someone writes one)  
  will format the quoted code.  

# What does Unseemly look like?

Okay, I've been postponing showing you a code sample  
 because Unseemly's syntax is *bats*.  
It looks like I was trying to come up with  
 a grand unified theory of syntax from first principles,  
  which, embarrassingly, I was.  
I also didn't worry too much about how it looked, because  
 the syntax (even the tokenizer!) can be completely rewritten with macros.  
And if there was anything I thought a macro could do,  
 I've omitted it from the [core language].  

[core language]: https://github.com/paulstansifer/unseemly/blob/master/core_language_basics.md

Here's a program to take the factorial of 5:  
```
((fix .[again: [ -> [ Int -> Int ]] .
    .[n: Int .
        match (zero? n) {
            +[True]+ => one
            +[False]+ => (times n ((again) (minus n one)))
        }
    ].
].) five)
```
It's really hard to read, because Unseemly doesn't have `if` statements  
 or recursive function definitions.  
But we can fix that!  

Here's what that `if` macro looks like,  
 though the density of weird new syntax may make it hard to read:  

```
extend_syntax
  Expr ::=also forall T . '{
      [
          lit ,{ DefaultToken }, = 'if'
          cond := ( ,{ Expr<Bool> }, )
          lit ,{ DefaultToken }, = 'then'
          then_e := ( ,{ Expr<T> }, )
          lit ,{ DefaultToken }, = 'else'
          else_e := ( ,{ Expr<T> }, )
      ]
  }' conditional -> .{
      '[Expr | match ,[cond], {
                +[True]+ => ,[then_e],
                +[False]+ => ,[else_e], } ]' }. ;
in
⋮
```
That's not a lot of code, considering that it handles  
 the syntax, the typing, and the actual behavior of `if`!

Then we don't need to use `match` to implement a factorial function:  

```
((fix .[again: [ -> [ Int -> Int ]] .
    .[n: Int .
        if (zero? n) then one else (times n ((again) (minus n one)))
    ].
].) five)
```

Here's what it could look like if we added function definitions:
```
letfn (fact n: Int) -> Int =
  if (zero? n) then one else (times n (fact (minus n one))) ;
in (fact five)
````

...and numeric literals:
```
letfn (fact n: Int) -> Int =
  if (equals? n 0) then one else (times n (fact (minus n one))) ;
in (fact 5)
```

...and binary math operators:
```
letfn (fact n: Int) -> Int =
  if n == 0 then 1 else n * (fact n - 1)
in (fact 5)
```

Another layer of macros can impose a C-like or Scheme-like or ML-like syntax,  
 add comments, more literals, and convenience features.  
It shouldn't take much code to get a basic language off the ground.