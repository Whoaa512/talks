theme: Next, 3

# Effective Flow Typing
### Bring safety back to your refactors

---

## :point_right: Why flow?
## :point_right: Where does flow fit?
## :point_right: How to best leverage it

---

## Where flow fits

![inline](./testing-pyramid.jpg)

^ Re: tests,  All that matters is (how much value, how long does it take to write, & how long does it take to run?) Categories not helpful

---
[.build-lists: true]

## Why flow?
- True 100% coverage
- Built in (and validated) docs
- Catches incorrect assumptions
- Refactor with confidence

^ I want to start w/ some short reasons why flow is important

^ really hard to get get a runtime exception w/ a properly flow-typed code base.
  Also a good rule of thumb: if it's hard to typecheck, it's probably hard to understand
  credit on the first 3 slides go to Forbes Lindesay's talk from React Amsterdam => https://youtu.be/lk8o7ym29WM

---
# Enabling flow

## `// @flow`

^ Flow can be enabled in any file by simply adding `// @flow` to the top of the file

---

## Escape hatches
[.build-lists: true]

- `any`/`Function`/`Object`
- `// $FlowFixMe`

^ Flow gives us 2 escape hatches to enable us to retain the speed & simplicity of JS when prototyping ideas
  - `any`/`Function`/`Object`
    + Has little to no validation, but can allow you to work quickly before refining your data flow
    + **Can lead to runtime exceptions**
    + Should be avoided as much as possible, can largely be solved with aliases, unions & intersections, and interfaces
  - `// $FlowFixMe`
    + Disables flow typing on the next line
    + Useful when there might be an error with flow itself, or you just can't make the type system understand what you're trying to do
    + Should be used sparingly, if at all

---

## Primitives - Literals

Types for literal values are lowercase

```javascript, [.highlight: 2-4, 8]
function method(
  x: number,
  y: string,
  z: boolean,
) {
  // ...
}
method(3.14, "hello", true)
```

---

## Primitives - Literals

Can also use literal values as types

```javascript
type Suit = 'Spades' | 'Hearts' | 'Clubs' | 'Diamonds'
const card: Suit = 'Spades'
```

---

## Primitives - Constructed objects


Types for wrapper objects are capitalized, the same as their constructor

```javascript, [.highlight: 2-4, 8]
function method(
  x: Number,
  y: String,
  z: Boolean,
) {
  // ...
}
method(new Number(42), new String("world"), new Boolean(false))
```

^ Try to avoid using the constructor objects, whenever possible

---

## The `mixed` type
> "Hey flow, I don't know what this will be, but I promise I won't depend on it's type"
-- Forbes Lindesay

```javascript
type ClickHandler = (e: Event) => mixed
const MyButton = (onClick: ClickHandler) => /*...*/
```

^ [`mixed`](https://flow.org/en/docs/types/mixed/) is similar to `any`, but is not an escape hatch. `mixed` says to flow, "Hey I don't know what this will be, but I promise I won't depend on it's type"

---

# Maybe types

```javascript, [.highlight: 3]
// @flow
function acceptsMaybeNumber(
  value: ?number,
) {
  // ...
}
```


^ It’s common for JavaScript code to introduce “optional” values so that you have the option of leaving out the value or passing null instead.
 Flow allows you to add `?` to help denote these "Maybe" types

---

# Maybe types

```javascript, [.highlight: 5]
// @flow
function acceptsMaybeNumber(
  value: ?number,
) {
  if (value != null) {
    return value * 2
  }
}
```

^ However when you add the `maybe` type you'll need to add a refinement, or a validation check to let flow know that you're handling the `maybe` properly

---

## Variable types

```javascript
// @flow
const fooConst /* : string */ = '1'
const barConst: string = '2'
let fooLet /* : number */ = 1
let barLet: number = 2
```

^ For const variables Flow can either infer the type from the value you are assigning to it or you can provide it with a type
^ Since let can be re-assigned, there’s a few more rules you’ll need to know about. Similar to const, Flow can either infer the type from the value you are assigning to it or you can provide it with a type
^ When you provide a type, you will be able to re-assign the value, but it must always be of a compatible type.
^ When you do not provide a type, the inferred type will do one of two things if you re-assign it.

---

## Function types

```javascript, [.highlight: 2]
// @flow
function concat(a: string, b: string): string {
  return a + b
}
```

^ Functions have two places where types are applied: Parameters (input) and the return value (output).

---

## Function types - Optional Parameters

```javascript, [.highlight: 2]
// @flow
function method(baz?: string): string {
  // ...
}
```

^ You can also have optional parameters by adding a question mark ? after the name of the parameter and before the colon :.

---

## Object types

```javascript
// @flow
var obj1: { foo: boolean } = { foo: true }
var obj2: {
  foo: number,
  bar: boolean,
  baz?: string, // Optional property
} = {
  foo: 1,
  bar: true,
  baz: 'three',
}
```

---

## Array types

```javascript
let arr1: Array<boolean> = [true, false, true]
let arr2: Array<string> = ["A", "B", "C"]
let arr3: Array<mixed> = [1, true, "three"]
// OR
let arr4: boolean[] = [true, false, true]
let arr5: string[] = ["A", "B", "C"]
let arr6: mixed[] = [1, true, "three"]
```

---

## Tuple types

```javascript
let tuple1: [number] = [1]
let tuple2: [number, boolean] = [1, true]
let tuple3: [number, boolean, string] = [1, true, "three"]
```

---

## Class types

```javascript
class MyClass {
  // ...
}

let myInstance: MyClass = new MyClass()
```

^ JavaScript classes in Flow operate both as a value and a type.

---

## Type aliases

```javascript, [.highlight: 2-4]
// @flow
type MyObject = {
  bar: string,
  baz: number,
}

var val: MyObject = { /* ... */ }
function method(val: MyObject) { /* ... */ }
class Foo { constructor(val: MyObject) { /* ... */ } }
```

^ Type aliases allow you to give meaningful names to shapes of data which can be reused.

---

## [Nominal vs. Structural](https://flow.org/en/docs/lang/nominal-structural/)

^ A static type checker uses either the names or the structure of the types in order to compare them against other types.

^ Languages with a more OO bent have primarily nominal type systems, while other languages with a more functional swing have primarily structural type systems.

^  B/c JS combines ideas from both paradigms, Flow uses structural typing for objects and functions, and nominal typing for classes.


---

## Opaque Type Aliases

```javascript
// @flow
opaque type ID = string

function identity(x: ID): ID {
  return x
}
export type { ID }
```

^ Opaque type aliases are type aliases that do not allow access to their underlying type outside of the file in which they are defined. Opaque type aliases may be used anywhere a type can be used.
  The main difference is that within the file they are defined they act like normal type aliases using structural typing, but when imported into another file they use nominal typing

---
## `typeof` types

```javascript
// @flow
let obj1 = { foo: 1, bar: true, baz: 'three' }
let obj2: typeof obj1 = { foo: 42, bar: false, baz: 'hello' }

let arr1 = [1, 2, 3]
let arr2: typeof arr1 = [3, 2, 1]
```

^ JavaScript has a typeof operator which returns a string describing a value.
^ Flow's `typeof` operator allows you to leverage existing types of a variable without having to redefine the type

---

## Read-only arrays

```javascript, [.highlight: 2]
function logNullable(
  arr: $ReadOnlyArray<?string>,
) {
  arr.forEach(val => { console.log(val) })
}

const myArr: Array<string> = ['Hello']

logNullable(myArr)
```

^ Using read-only arrays in places where you're not mutating
  will make the errors you get from flow match your expectations more closely

---

## Read-only properties

```javascript, [.highlight: 2]
function logNullable(
  container: { +value: ?string },
) {
  console.log(container.value)
}
const obj: { value: string } = { value: 'Ahoy' }
logNullable()
```

^ Using read-only properties in places where you're not mutating
  will make the errors you get from flow match your expectations more closely

---
## Further reading
- [Interfaces](https://flow.org/en/docs/types/interfaces/)
- [Generics](https://flow.org/en/docs/types/generics/)
- [Unions](https://flow.org/en/docs/types/unions/)
- [Intersections](https://flow.org/en/docs/types/intersections/)
- [Utility types](https://flow.org/en/docs/types/utilities/)
- [Flow + React](https://flow.org/en/docs/react/components/)

---

## Credits

- [Flow Typing a React Codebase](https://youtu.be/lk8o7ym29WM) - Forbes Lindesay
- [Flow type annotations docs](https://flow.org/en/docs/types/)
- [Flow type system docs](https://flow.org/en/docs/lang/)
- [Type Systems Will Make You a Better JavaScript Developer](https://youtu.be/V1po0BT7kac) - Jared Forsyth


---

# It’s **that** easy!

![left](http://deckset-assets.s3-website-us-east-1.amazonaws.com/colnago1.jpg)