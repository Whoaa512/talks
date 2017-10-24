# Flow Typing a React Codebase - Forbes Lindesay
> https://youtu.be/lk8o7ym29WM

## why flow?

### pyramid
```
    ______^_______
   /     e2e      \
  /    js tests    \ <- All that matters is (how much value, how long does it take to write, & how long does it take to run?) Categories not helpful
 / static analysis  \
```

### Why static analysis?
  - 100% coverage
  - built in docs
  - catches incorrect assumptions
    * With comment docs, you can never really know if they are up to date
    * or if the the person that wrote them checked to make sure they were right, etc
  - makes you more confident in refactoring code by making it safer


### Escape hatches
  - flow supports the beauty JS prototyping by giving you escape hatches
    + **Can lead to runtime exceptions**
  - [`any`](@todo: add link to flow docs)
  - [`Function`](@todo: add link to flow docs)
  - [`Object`](@todo: add link to flow docs)
  - [`// $FlowFixMe`](@todo: add link to flow docs)

# Type Systems Will Make You a Better JavaScript Developer - Jared Forsyth
> https://www.youtube.com/watch?v=V1po0BT7kac

- if it's hard to typecheck, it's probably hard to understand
- if you're making a ton of this optional, you're probably trying to represent a state machine poorly
  + Use Union types instead ([link](https://flow.org/en/docs/types/unions/))






# more notes on integrating flow with react
  - It's useful to import React as a namespace here with `import * as React from 'react'` instead of as a default with `import React from 'react'`.
    - When importing React as an ES module you may use either style, but importing as a namespace gives you access to React’s [utility types](https://flow.org/en/docs/react/types).
  - Flow supports adding types for event handlers, including types for React's synthetic event system
  - Flow's `typeof` operator allows you to leverage existing types of a variable without having to redefine the type
    + https://flow.org/en/docs/types/typeof/
  - Flow uses structural typing for objects and functions, but nominal typing for classes.
    + If you wanted to use a class structurally you could do that by mixing them with objects as interfaces:
    + ```
    type Interface = {
      method(value: string): number;
    };

    class Foo { method(input: string): number {...} }
    class Bar { method(input: string): number {...} }

    let test: Interface = new Foo(); // Okay.
    let test: Interface = new Bar(); // Okay.
    ```
    + https://medium.com/@thejameskyle/type-systems-structural-vs-nominal-typing-explained-56511dd969f4
  - @todo: add explanation on `String` vs. `string` and `Number` vs. `number`
  - By default, object properties are invariant, which allow both reads and writes, but are more restrictive in the values they accept



## my notes
  - really hard to get get a runtime exception w/ a properly flow-typed code base
  - [`mixed`]() similar to `any`, but is not an escape hatch. `mixed` says to flow, "Hey I don't know what this will be, but I promise I won't depend on it's type"
  - `flow-typed` - useful cli to check existing flow-types for npm modules and add them to your codebase, and will help create stubs for packages that don't have any types yet. also gives instructions on how to contribute back
  - `flow-runtime`
    - optionally compile flow to runtime typechecks
    - too slow for production
  - Avoid `any`/`Object`/`Function` as much as possible
  - Exact Object types are awesome
    - ```
      type ObjType = {|
        value?: string,
      |}
      const v: ObjType = {
        val: 'hello',
      }
    ```
  - `$ReadOnlyArray` -- need links to this
  - read only property values `type ObjType = { +value: string }`
    - `+` is the read only property syntax marker, in the docs it's called "covariant"
  * By default try to use `$ReadOnlyArray` and read only properties
    - it will make the errors you get from flow match your expectations more closely
  - Generics allow more flexibility when adding flow to legacy code bases
    + Quick 3 minute example: https://youtu.be/lk8o7ym29WM?t=26m11s