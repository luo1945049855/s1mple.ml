---
title:  "TypeScript"
date:   2020-11-20
categories: Frontend
---

## Basic Types

```ts
let isDone: boolean = false;
let decimal: number = 10;
let hex: number = 0xff;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: bigint = 100n;

// ####################### Template String #######################

`big plus one is ${big + 1}`.

// ####################### Array #######################

let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];

// ####################### Tuple #######################
// Tuple types allow you to express an array with a fixed
// number of elements whole types are known, but need not be the same.

let x: [string, number];
x = ["hello", 10];
x = [10, "hello"]; // Error
console.log(x[0]);

// ####################### Enum #######################

enum Color {
    Red = 1,
    Green = 2,
    Blue = 4,
}
let c: Color = Color.Green;

let colorName: string = Color[2];
console.log(colorName);

// ####################### Unknown #######################
// 'maybe' could be a string, object, boolean, undefined, or other types.

declare const maybe: unknown;
const aNumber: number = maybe; // Error
if (maybe === true) {
    const aBoolean: boolean = maybe;
}
if (typeof maybe === "string") {
    const aString: string = maybe;
}

// ####################### Any #######################
// *opt-out* of type checking.
// Any will propagate through your objects.

declare function getValue(key: string): any;
// OK, return value of 'getValue' is not checked
const str: string = getValue("myString");

let looselyTyped: any = 4;
// OK, ifItExists might exist at runtime.
looselyTyped.ifItExists();
// OK, toFixed exists (but the compiler doesn't check)
looselyTyped.toFixed();

let strictlyTyped: unknown = 4;
strictlyType.toFixed(); // Error, Object is of type 'unknown'.

let looselyType: any = {};
let d = looselyTyped.a.b.c.d; // ^ = let d: any

// ####################### Void #######################
// you can only assign null(only if --strictNummChecks isn't specified) or undefined to them.

function warnUser(): void {
    console.log("This is my warning message");
}

let unusable: void = undefined;
unusable = null;

// ####################### Null and Undefined #######################
// By default null and undefined are subtypes of all other types. That means you can assign null and undefined to something like number.
// However, when using the --strictNullChecks flag, null and undefined are only assignable to unknown, any and their respective types (the one exception being that undefined is also assignable to void). This helps avoid many common errors. In cases where you want to pass in either a string or null or undefined, you can use the union type string | null | undefined.

let u: undefined = undefined;
let n: null = null;

// ####################### Never #######################
// never occur.
// function that always throws an exception or one never returns.
// variables acquire the type never when narrowed by any type guards that can never be true.

// function returning never must not have a reachable end point.
function error(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
    while (true) {}
}

// Inferred return type is never.
function fail() {
    return error("something failed");
}

// ############################# Object #######################
declare function create(o: object | null):  void;

// ############################# Type Assertion #######################
let someValue: unknown = "this is a string";
let strLength0: number = (someValue as string).length;
let strLenght1: number = (<string>someValue).length;
```

## Interfaces

* One of TypeScript's `core principles` is that type checking focuses on the `shape` that values have.

```ts
// ############################# Optional Properties #######################
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    let newSquare = { color: "white", area: 100 };
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

// ############################# Readonly Properties #######################
// Variable use const whereas properties use readonly.

interface Point {
    readonly x: number;
    readonly y: number;
}
let pt: Point = { x: 10, y: 20 };
pt.x = 5; // Error

let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 0; // Error
a = ro;    // Error. 'readonly number[]' cannot be assigned to the mutable type 'number[]'

// ############################# Excess Property Checks #######################
// Object literals get special treatment and undergo `excess property` checking
// when assigning them to other variables, or passing them as arguments.
// If an object literal has any properties that the “target type” doesn’t have, you’ll get an error.
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    return {
        color: config.color || "red",
        area: config.width ? config.width * config.width : 20;
    };
}

let mySquare = createSquare({ colour: "red", width: 100 });
// Argument of type '{ colour: string; width: number; }'
// is not assignable to parameter of type 'SquareConfig'.
// Object literal may only specify known properties,
// but 'colour' does not exist in type 'SquareConfig'. Did you mean to write 'color'?

// ############################# Function Type Interfaces #######################
interface SearchFunc {
    (source: string, substring: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, substring: string) {
    let result = source.search(substring);
    return result > -1;
}

// ############################# Indexable Type Interfaces #######################
// Supported index signatures: string and number.
// obj[numeric] must be a `subtype` of obj[string],
// numeric indexer is convert to a string before indexing into an object.
//
// string index signatures enforce that all properties match their return type.
// because it's a powerful way to describe the "dictionary" pattern.
//
interface StringArray {
    [index: number]: string;
    [index: string]: string;
    length: number; // Error. The type of 'length' isn't subtype of string.
}
interface StringArray {
    [index: number]: string | number;
    [index: string]: string;
    length: number; // OK.
}
interface StringArray {
    readonly [index: number]: string; // readonly index signatures.
}

// ############################# Class Types #######################
// * class has two sides: static side and instance side.
// * constructor sits in the static side.
//

interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick(): void;
}
const Clock: ClockConstructor = class Clock implements ClockInterface {
    constructor(h: number, m: number) {}
    tick() {
        console.log("beep beep");
    }
};

// ############################# Extending Interfaces #######################
// * An interface can extend multiple interfaces, creating a combination of all of the interfaces.
//

interface Shape {
    color: string;
}
interface PenStroke {
    penWidth: number;
}
interface Square extends Shape, PenStroke {
    sideLength: number;
}

// ############################# Hybrid Types #######################
// * object can act as both a function and an object, with additional properties.
//

interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function getCounter(): Counter {
    let counter = function(start: number) {} as Counter;
    counter.interval = 123;
    counter.reset = function() {};
    return counter;
}

// ############################# Interfaces Extending Classes #######################
// Interface extends a class type's members even protected and privated, but not their implementations.
```

## Functions

```ts

// ################Writing the function type:
// ################(baseValue: number, increment: number) => number###############################

let myAdd: (baseValue: number, increment: number) => number = function(
    x: number,
    y: number
): number {
    return x + y;
};

let myAdd2: (baseValue: number, increment: number) => number = function(x, y) {
    return x + y;
};

// ###############################Optional and Default Parameters###############################
// * Any optional parameters must follow required parameters.
// * Default initialized parameters don't need to occur after required parameters.
//   `Trailing`default parameters will share commonality with optional parameters.
//   (firstName: string, lastName?: string) => string;

function buildName(firstName: string, lastName?: string) {
    if (lastName) return firstName + " " + lastName;
    else return firstName;
}

function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

function buildName(firstName = "Will", lastName: string) {
    return firstName + " " + lastName;
}
let result = buildName(undefined, "Adams"); // return "Will Adams"

// ###############################Rest Parameters###############################

function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}
let employeeName = buildName("Joseph", "Samuel", "Lucas", "Mackinzie");

// ###############################this###############################
// this and arrow functions

let deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function () {
    return function () {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 }; // error, this isn't deck object.
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();
alert("card: " + pickedCard.card + " of " + pickedCard.suit);

//##############################################################

interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}

let deck: Deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),

  // this parameters are `fake` parameters that come first in the parameter list of a function.
  // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
  // * The downside is arrow function is created per object.
  createCardPicker: function (this: Deck) {
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);
      return { suit: this.suits[pickedSuit], card: pickedCard % 13 }; // ok, this is deck object.
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();
alert("card: " + pickedCard.card + " of " + pickedCard.suit);

// ###############################Overloads###############################

function pickCard(x: { suit: string; card: number }[]): number;
function pickCard(x: number): { suit: string; card: number };

```

## Literal Types

```ts

// ###############################String Literal Types###############################
// String literal combine nicely with `union types`, `type guards`, and `type aliases`.
// Numeric literal types which act the same as the string literals.
// Boolean literal types which might be used to constrain object values.

type Easing = "ease-in" | "ease-out" | "ease-in-out";

class UIElement {
    animate(dx: number, dy: number, easing: Easing) {
        if (easing === "easing-in") {

        } else if (easing === "easing-out") {

        } else if (easing === "easing-in-out") {

        } else {

        }
    }
}
let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy");
// Error. Argument of type '"uneasy"' is not assignable to parameter of type 'Easing'.

function createElement(tagName: "img"): HTMLImageElement;
function createElement(tagName: "input"): HTMLInputElement;
function createElement(tagName: string): Element;

// ###############################Numeric###############################

function rollDice(): 1 | 2 {
    return (Math.floor(Math.random() * 6) + 1) as 1 | 2;
}

// ###############################Boolean###############################

interface ValidationSuccess {
    isValid: true;
    reason: null;
}
interface ValidationFailure {
    isValid: false;
    reason: null;
}

```

## Unions and Intersection Types

```ts

// ###############################Unions###############################
function padLeft(value: string, padding: string | number) {

}

// ###############################Unions with Common Fields###############################
interface Bird {
    fly(): void;
    layEggs(): void;
}
interface Fish {
    swim(): void;
    layEggs(): void;
}

declare function getSmallPet(): Fish | Bird;
let pet = getSmallPet();
pet.layEggs(); // OK.
pet.swim();
// Error.
// Property 'swim' does not exist on type 'Bird | Fish'.
// Property 'swim' does not exist on type 'Bird'.

// ###############################Discriminating Unions###############################
// Discriminating Unions by adding a single shared field.

// ###############################Intersection Types###############################
// An intersection type combines multiple types into one which have all members of all other types.

```

## Classes

```ts
// * Derived class that contains a constructor function must call `super()`
//   which will execute the constructor of the base class.
// * before we ever access a property on this in a constructor body,
//   we have to call `super()`.
// * members are public by default.
// * `#member`/`private member: type` is private fields.
// * Parameter properties let you create and initialize a member in one place.
//   `constructor(accessibility modifier or readonly member: type)`
// * Accessors require you to set the compiler output ECMAScript 5 or higher.
//   Accessors with a set and no set are automatically inferred to be readonly.

class Animal {
    public name: string;
    #color: string;

    // public by default.
    constructor(theName: string) {
        this.name = theName;
    }

    move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Horse extends Animal {
    constructor(name: string) {
        super(name);
    }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

// ############################Static Properties#############################
// Grid.x not Grid::x

class Grid {
    static origin = { x: 0, y: 0};

    calculateDistanceFromOrigin(point: { x: number, y: number }) {
        let xDist = point.x - Grid.origin.x;
    }
}

// ############################Abstract Classes#############################

abstract class Animal {
    abstract makeSound(): void;

    move(): void {
        console.log("roaming the earth...");
    }
}

// ############################Constructor Functions#############################

class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        } else {
            return Greeter.standardGreeting;
        }
    }
}

let greeter1: Greeter;
greeter1 = new Greeter();
console.log(greeter1.greet()); // "Hello, there"

// greeterMaker holds its constructor function.
// and contain all of the static members of Greeter along with
// the constructor that creates instances of the Greeter class.

let greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey, there";
let greeter2: Greeter = new greeterMaker();
console.log(greeter2.greet()); // "Hey, there"

let greeter3: Greeter;
greeter3 = new Greeter();
console.log(greeter3.greet()); // "Hey, there"

// JavaScript created by the above example

let Greeter = (function() {
    function Greeter(message) {
        this.greeting = message;
    }

    Greeter.prototype.greet = function() {
        return "Hello, " + this.greeting;
    };

    return Greeter;
})();

// ############################Using a class as an interface#############################

```

## Enums

* Enum members also become types as well.
* Enum themselves effectively become a union of each enum member.

### Numeric Enums

* Numeric enums can be mixed in computed and constant members.
* Enums without initializers either need to be first and come after numeric.
* Numeric enums members also get a `reverse mapping` from enum values to enum names.

```ts
enum E {
    A = getSomeValue(),
    B, // Error.
}
```

### Enums at runtime

* Enums are real objects that exists at runtime.

```ts
enum E {
    X,
    Y,
    Z,
}
function f(obj: { X: number }) {
    return obj.X;
}
f(E); // Works, since 'E' has a property named 'X' which is a number.
```

### Enums at compile time

* `keyof typeof` get a `Type` that represents all Enum keys as strings.

```ts
enum E {
    X,
    Y,
    Z,
}
/*
 * This is equivalent to:
 * type EStrings = 'X' | 'Y' | 'Z';
 */
type EStings = keyof typeof E;
```

### const Enums

* const enum are removed during compilation.
* const enum members are inlined at use sites.
* const enum cannot have computed members.

```ts
const enum Direction {
    Up,
    Down,
    Left,
    Right,
}
let directions = [
    Direction.Up,
    Direction.Down,
    Direction.Left,
    Direction.Right,
];
```

## Generic

```ts
// Generic functions
function identity<T>(arg: T): T {
    return arg;
}

function identify<T>(arg: T): T {
    console.log(arg.length); // Error. Not like C++
    return arg;
}

// Generic Interface
interface GenericIdentityFn<T> {
    (arg: T): T;
}
function identity<T>(arg: T): T {
    return arg;
}
let myIdentity: GenericIdentityFn<number> = identity;
```

### Generic Classes

* Generic classes are only generic over their instance side rather that their static side.

### Generic Constraints

* `<T extends Interface>` add constraint to generic.
* `<T, K extends keyof T>` add constraints by another type parameter.
* When using `class in generic`, it's necessary to refer to class types by their `constructor` function.
  the prototype property is related to constructor function because of generated JavaScript code.

```ts
interface Lengthwise {
    length: number;
}
function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length); // OK.
    return arg;
}
```

## Reference

[The TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
