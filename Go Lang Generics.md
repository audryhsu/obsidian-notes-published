---
tags: golang
aliases: generics
---
Tags: [Go Lang](./Go%20Lang.md)
References: [Go Generics Tutorial](https://go.dev/doc/tutorial/generics)
[Introducing Generics](https://www.youtube.com/watch?v=Pa_e9EeCdy8)

---
💻 [Playground code](https://goplay.tools/snippet/ZymLNGu92aP) from Go Tutorial on generics 

New to 1.18 release
- *Type constraints* are a set of types that describes a type parameter (the one we're defining)
- Type constraints can be declared as [interfaces](./Go%20Lang%20Structures%20&%20Interfaces.md) or directly in a generic function's type parameter list (e.g. square brackets that describe a type's type, aka meta-type.).

![Go Lang Generics 2023-03-04 15.01.15.excalidraw](./images/Go%20Lang%20Generics%202023-03-04%2015.01.15.svg)

A commonly used type parameter constraint is the `comparable` interface from the [standard library](https://pkg.go.dev/builtin#comparable)
- implemented by all comparable types (booleans, numbers, strings, pointers, channels, arrays of `comparable` types, structs whose fields are all `comparable`)

