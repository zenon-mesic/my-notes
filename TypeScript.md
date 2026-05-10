# TypeScript

## Basic Types

```ts
const bootupMessage: string = "starting server...";
const port: number = 3000;
const isOnline: boolean = true;
const noValue: null = null;
const notDefined: undefined = undefined;
```

## Function Type Syntax

### Standard Functions

```ts
function createMessage(name: string, a: number, b: number): string {
  return `${name} scored ${a + b}`;
}
```

### Arrow Functions

```ts
const createMessage = (name: string, a: number, b: number): string => {
  return `${name} scored ${a + b}`;
};
```

### Explicit Type Annotation

```ts
type CommandHandler = (cmdName: string, ...args: string[]) => void;

const handlerLogin: CommandHandler = (cmdName, ...args) => { ... };
```

### Type Alias

Instead of:

```ts
function setLoggerTimeout(
  loggerCallback: (s1: string, s2: string) => string,
  delay: number,
) {
  // do something
}
```

Use a type alias:

```ts
type LoggerCallback = (s1: string, s2: string) => string;

function setLoggerTimeout(loggerCallback: LoggerCallback, delay: number) {
  // do something
}
```

## Importing Types

You can import directly:

```ts
import { User, Post } from "./models";
```

But it is safer and more efficient to use `import type` syntax:

```ts
import type { User, Post } from "./models";
```

This also works:

```ts
import { type User, type Post } from "./models";
```

## Unions

Basic example:

```ts
// userId is a string OR a number
let userId: string | number;
userId = "user_42";
userId = 42;
```

Type narrowing example:

```ts
function safeSquare(val: string | number): number {
  if (typeof val === "string") {
    val = parseInt(val, 10);
  }
  // now val is only a number
  return val * val;
}

let result = safeSquare("5");
console.log(result);
// 25

result = safeSquare(5);
console.log(result);
// 25
```

## Optional Parameters

```ts
function greet(name: string, title?: string): string {
  if (title) {
    return `Hello, ${title} ${name}!`;
  }
  return `Hello, ${name}!`;
}

greet("Gandalf"); // "Hello, Gandalf!"
greet("Gandalf", "Wizard"); // "Hello, Wizard Gandalf!"
```

Notes:
1. Optional parameters must come after all required parameters.

```ts
// Error: Required parameter cannot follow optional parameter
function greet(title?: string, name: string): string {
  // ...
}
```

2. Optional params have an `undefined` automatically unioned on the specified type. If the value is omitted, it's `undefined` instead of the specified type.

```ts
function greet(name: string, title?: string): string {
  // inside the function, title
  // is a string | undefined
}
```

## Default Parameters

When you use default parameters, you do not need to mark the parameter as optional by using `?`. When using a default value, the parameter type can be automatically inferred, so don't specify it, unless you need to widen the type.

```ts
function countdown(start = 10): void {
  // start is a number
  console.log(`Counting down from ${start}...`);
}
```

## Literal Types

A string can have an infinite number of values.

```ts
function move(direction: string) {
  // Implementation...
}
```

Now `direction` can only be "north":

```ts
function move(direction: "north") {
  // Implementation...
}
```

To make it a bit more useful, let's combine that idea with a union type:

```ts
type Direction = "north" | "south" | "east" | "west";

function move(direction: Direction) {
  // Implementation...
}
```

### Template Literal Types

You can also create literal types using string templates:

```ts
type Class = "wizard" | "warrior" | "rogue";

type Hero = `elf ${Class}`;
// The type of Class expands automatically to the possible values, so the above is the same as:
// type Hero = "elf wizard" | "elf warrior" | "elf rogue";
```

Another example:

```ts
type LogRecord = `${string}: ${number}`;

// this is valid because it's a string followed by a colon and a number
const criticalErr: LogRecord = "CRITICAL: 69";

// these are all invalid
const criticalErr: LogRecord = "CRITICAL 92";
const criticalErr: LogRecord = "CRITICAL: 92a";
const criticalErr: LogRecord = "92: CRITICAL";
```

## Giant Unions

Best practice: Avoid them.
Otherwise, you might get

> ❌ Error
>
> Union type too complex to represent.

## Arrays

The most common way to declare an array is using the bracket notation, `string[]`, `number[]`, etc.:

```ts
// Using bracket notation
function assignLightsaberColors(name: string, colors: string[]): void {
  return
}

// Using generic type parameter syntax
function assignLightsaberColors(name: string, colors: Array<string>): void {
  return

// TypeScript infers the type as (string | number)[]
let lightsaberStyles = [1, 2, "double", "shoto"];
}
```

### Rest Parameters

Rest parameters allow an indefinite number of final arguments, and brings them into the function body as an array. They're denoted by three dots (`...`) before the parameter name.

```ts
function gatherParty(partyName: string, ...adventurers: string[]): string {
  return `${partyName} consists of: ${adventurers.join(", ")}`;
}
```

### Evolving Any

When you create a new empty array, TypeScript infers it as `any[]`.

```ts
let inventory = [];
// inventory: any[]
```

If you then push a type into it, TypeScript will infer the array as that type.

```ts
inventory.push(42);
// inventory: number[]
```

You're actually still allowed to push other types into the array, it just keeps updating the underlying type:

```ts
inventory.push("robe");
// inventory: (number | string)[]
```

#### Within A Function

Evolving any stops when it gets passed around:

```ts
function getConfig() {
  let config = [];
  // config: any[]
  config.push("api-key");
  // config: string[]
  config.push(8080);
  // config: (string | number)[]
  return config;
}

let config = getConfig();
// config: (string | number)[]

config.push(false);
// Error: Argument of type 'boolean' is not assignable to parameter of type 'string | number'
```

## Object Literal Types

```ts
type Saiyan = {
  name: string;
  power: number;
};

function logSaiyan(saiyan: Saiyan) {
  console.log(`${saiyan.name} has power level: ${saiyan.power}!`);
  // ...
}
```

### Extra Properties

```ts
type Spaceship = {
  name: string;
  speed: number;
};

const falcon = {
  name: "Millennium Falcon",
  speed: 75,
  weapons: 4,
};

function pilot(ship: Spaceship) {
  console.log(`Piloting ${ship.name} at ${ship.speed} light-years per hour`);
}

// this is fine
pilot(falcon);

// Error: Object literal may only specify known properties, and 'weapons' does not exist in type 'Spaceship'.
pilot({ name: "Millennium Falcon", speed: 75, weapons: 4 });
```

### Optional Object Properties

```ts
type Superhero = {
  name: string;
  strength: number;
  cape?: boolean; // cape is optional
};

function fight(superhero: Superhero) {
  if (!superhero.cape) {
    // contact edna mode
  }
  // do the happy path thing
}
```

### Empty Object Type

TypeScript doesn't allow this:

```ts
let newUser = {};

// Property 'name' does not exist on type '{}'
newUser.name = "Lane";
```

But it allows this:

```ts
let newUser = {};
newUser = "Lane";
```

You can reassign a variable which initially held an empty object to anything except `null` or `undefined`, because everything else is technically an object.


### Discriminated Unions

Discriminant properties/tags example:

```ts
type MultipleChoiceLesson = {
  kind: "multiple-choice"; // Discriminant property
  question: string;
  studentAnswer: string;
  correctAnswer: string;
};

type CodingLesson = {
  kind: "coding"; // Discriminant property
  studentCode: string;
  solutionCode: string;
};

type Lesson = MultipleChoiceLesson | CodingLesson;

function isCorrect(lesson: Lesson): boolean {
  switch (lesson.kind) {
    case "multiple-choice":
      return lesson.studentAnswer === lesson.correctAnswer;
    case "coding":
      return lesson.studentCode === lesson.solutionCode;
  }
}
```

### Sets

```ts
// A Set that contains only strings
const justiceLeague = new Set<string>();

// A Set automatically removes duplicate values from an array
const names = ["plasticman", "firestorm", "plasticman"];
const justiceLeague = new Set<string>(names);
```

### Maps

```ts
// A Map with string keys and number values
const podracerSpeeds = new Map<string, number>();

podracerSpeeds.set("Anakin Skywalker", 947);
podracerSpeeds.set("Sebulba", 941);

podracerSpeeds.set("R2-D2", true);
// Error: Argument of type 'true' is not assignable to parameter of type 'number'

podracerSpeeds.set(420, 69);
// Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

How to easily iterate over a map:

```ts
for (const [racer, speed] of podracerSpeeds) {
  console.log(`${racer} raced at ${speed} speed`);
}
// Anakin raced at 947 speed
// Sebulba raced at 941 speed
```

The most important Map functions are `get`, `set`, `has`, and `delete`.

### Dynamic Keys

Sometimes, you won't know all of an object's property names in advance.

In that case, you can define dynamic keys using an index signature:

```ts
type UserMetrics = {
  [key: string]: number;
};

const metrics: UserMetrics = {
  wordsPerMinute: 50,
  errors: 2,
  timeOnPage: 120,
};

metrics["refreshRate"] = 60; // OK
metrics["theme"] = "dark"; // Error: Type 'string' is not assignable to type 'number'
```

This type says "this object can have any number of properties if the keys are strings and the values are numbers."

#### Dynamic Default Properties

```ts
type FormData = {
  [field: string]: string;
  email: string;
  password: string;
};
```

You use this syntax to require certain properties, in this case, `email` and `password`. The type above says:

> The object must have an `email` and `password` property, and it can have any number of additional string properties.

Another example:

```ts
type FormData = {
  [field: string]: string | number | boolean;
  email: string;
  password: string;
  age: number;
};
```

This type says:

> The object must have an `email` (string), `password` (string), and `age` (number) property, but it can have any number of additional string, number, or boolean properties.

Only use dynamic keys when you truly need unknown keys. If you have optional keys, just use the `?` operator.

### PropertyKey

TypeScript has a built-in type called `PropertyKey` that represents all possible property key types:

```ts
// this is a built-in type
type PropertyKey = string | number | symbol;
```

So, instead of:

```ts
type InfrastructureTags = {
  [key: string]: any;
};
```

We can allow number and symbol keys (the JS default) like this:

```ts
type InfrastructureTags = {
  [key: PropertyKey]: any;
};

const janesServer: InfrastructureTags = {
  name: "Jane's Server",
  1: 420,
  [Symbol("role")]: "Admin",
};
```

### Readonly Modifier

```ts
type Point = {
  readonly x: number;
  y: number;
};

const point: Point = {
  x: 10,
  y: 20,
};

point.y = 30; // OK

// Error: Cannot assign to 'x' because it is a read-only property
point.x = 15;
```

### As Const

```ts
const colorsConst = ["red", "green", "blue"] as const;

// Error: Property 'push' does not exist on type 'readonly ["red", "green", "blue"]'
colorsConst.push("yellow");
```

Unlike most utility types and `Object.freeze()`, it automatically makes all nested structures `readonly` as well:

```ts
const configConst = {
  apiUrl: "https://api.cobrakai.com",
  admins: {
    johnny: "lawrence",
    daniel: "larusso",
  },
  features: ["no mercy", "not crying", "winning too much"],
} as const;

// Error: Cannot assign to 'apiUrl' because it is a read-only property
configConst.apiUrl = "https://api.karate.com";

// Error: Cannot assign to 'johnny' because it is a read-only property
configConst.admins.johnny = "larusso";

// Error: Property 'push' does not exist on type 'readonly ["no mercy", "not crying", "winning too much"]'
configConst.features.push("sweep the leg");
```

### Satisfies

Before `satisfies`, you often had to choose between:

1. Letting TypeScript infer types (good for flexibility, but might miss errors)
2. Using explicit type annotations (good for catching errors, but loses narrowed type information)

```ts
// Using type inference (flexible but might miss errors)
const colors = {
  red: "#ff0000",
  green: "#00ff00",
  blue: 255, // same as hex "#0000ff"

  // "classic Lane-style typo" - Allan
  yelow: "#ffff00",
};
```

To get around this, we can create an explicit type, and use that to catch typos:

```ts
type ColorMap = {
  red: string | number;
  green: string | number;
  blue: string | number;
  yellow: string | number;
};

const colorsTyped: ColorMap = {
  red: "#ff0000",
  green: "#00ff00",
  blue: 255,
  // Error: "yelow" is not in type ColorMap
  yelow: "#ffff00",
};
```

The trouble is that because our `ColorMap` type uses `string | number` for the values, we lose the more specific type information:

```ts
// redHex is now 'string | number'
// where it used to be 'string'
type redHex = typeof colorsTyped.red;

// Error: Property 'toUpperCase' does not exist on type 'string | number'
const redUpper = colorsTyped.red.toUpperCase();
```

The `satisfies` operator gives us the best of both worlds:

```ts
type ColorMap = {
  red: string | number;
  green: string | number;
  blue: string | number;
  yellow: string | number;
};

const colorsSatisfies = {
  red: "#ff0000",
  green: "#00ff00",
  blue: 255,
  yellow: "#ffff00",
  // Error: "yelow" is not in type ColorMap
  // yelow: "#ffff00"
} satisfies ColorMap;

// We keep the narrowed types!
type RedHexSatisfies = typeof colorsSatisfies.red;
const redUpper = colorsSatisfies.red.toUpperCase(); // "#FF0000"
```

### Function Overloads

First, we define a function that can be called in multiple ways:

```ts
function formatEmployeeMessage(
  employee: Employee,
  isNew?: boolean,
  onBoardedDate?: Date,
): string {
  if (!isNew) {
    return `Employee: ${employee.name}, Dept: ${employee.dept}`;
  }
  return `Employee: ${employee.name}, New: Yes, Onboarded: ${onBoardedDate}`;
}

type Employee = {
  name: string;
  dept: string;
};
```

Used as-is, this function can be called in 3 different ways:
- `formatEmployeeMessage(employee)`
- `formatEmployeeMessage(employee, boolean)`
- `formatEmployeeMessage(employee, boolean, Date)`

But we can constrain the function to only allow certain combinations of parameters by using function overloads.

```ts
// note: function overloads need to be declared above the implementation
function formatEmployeeMessage(employee: Employee): string;
function formatEmployeeMessage(
  employee: Employee,
  isNew: true,
  onBoardedDate: Date,
): string;

const employee: Employee = { name: "Joe Exotic", dept: "Zoo" };
const msg = formatEmployeeMessage(employee);
console.log(msg);
// Employee: Joe Exotic, Dept: Zoo

const employee: Employee = { name: "Carole Baskin", dept: "Big Cat Rescue" };
const msg = formatEmployeeMessage(employee, true, new Date());
console.log(msg);
// Employee: Carole Baskin, New: Yes, Onboarded: 2023-10-01T00:00:00.000Z

const employee: Employee = { name: "Dillon Passage", dept: "Zoo" };
// Error: No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
const msg = formatEmployeeMessage(employee, true);
```

## Tuples

A tuple is a special kind of array where each position has a specific, known type.

```ts
const nameAndAge: [string, number] = ["Rose Tyler", 24];
```

#### Be Explicit With Tuples

This is a tuple:

```ts
// [string, number]
const nameAndAge: [string, number] = ["John Jones", 104];
```

This is an array of `string | number`:

```ts
// (string | number)[]
const nameAndAge = ["Martha Jones", 24];
```

They're handled differently:

```ts
const nameAndAge = ["Martha Jones", 24];
// this works
nameAndAge[1] = "Donna Noble";

const nameAndAge: [string, number] = ["Martha Jones", 24];
// Error: Type 'string' is not assignable to type 'number'.
nameAndAge[1] = "Donna Noble";
```

### Readonly

Tuples in TypeScript are still arrays under the hood, so counterintuitively you can still push to them and pop from them.

```ts
const nameAndAge: [string, number] = ["Martha Jones", 24];
nameAndAge.push("Donna Noble");
```

To make tuples immutable, use `readonly`:

```ts
const nameAndAge: readonly [string, number] = ["Martha Jones", 24];
// Error: Property 'push' does not exist on type 'readonly [string, number]'.
nameAndAge.push("Donna Noble");
```

### Destructuring Tuples

Sometimes tuples are also useful when you want to return multiple values from a function (which is impossible in JS/TS), but you don't want to create a new object type just to do so.

```ts
function getName(fullName: string): [string, string] {
  const parts = fullName.split(" ");
  return [parts[0], parts[1]];
}

const [firstName, lastName] = getName("Frodo Baggins");
```

#### Nested Destructuring

```ts
type UserWithAddress = [string, { city: string; country: string }];

const userData: UserWithAddress = [
  "Aragorn",
  { city: "Minas Tirith", country: "Gondor" },
];

const [userName, { city, country }] = userData;
console.log(city);
// Minas Tirith
```

### Named Tuples

You can label tuple elements (sometimes called "named tuples").

Instead of this:

```ts
type UserData = [string, number, boolean];
```

We can do this:

```ts
type UserDataLabeled = [name: string, age: number, isAdmin: boolean];
```

When you hover over a variable in your editor, you'll see names instead of just positions:

```ts
// Your editor shows the full type:
// [name: string, age: number, isAdmin: boolean]
function getUser(): UserDataLabeled {
  return ["Frodo", 33, false];
}
```

#### Labels Are Just Documentation

The labels are quite literally just names for the TypeScript tooling, they don't change how the values are accessed. Say we have a named tuple like this:

```ts
const user: [name: string, age: number] = ["Bilbo", 111];
```

And then we try to destructure in reverse order:

```ts
const [age, name] = user;
console.log(age); // "Bilbo"
console.log(name); // 111
```

the variable names we choose when destructuring don't matter: only the positions do.

### Optional Elements in Tuples

Example:

```ts
type HttpResponse = [statusCode: number, data: string, error?: string];

// Both of these work!
const successResponse: HttpResponse = [200, "Success!"];
const errorResponse: HttpResponse = [404, "", "Resource not found"];
```

#### Optional Values Are Last

Similar to optional function parameters, all required elements must come before optional elements.

#### Optional Types Are Potentially Undefined

All optional elements are automatically unioned with undefined.

```ts
type UserInfo = [name: string, age: number, address?: string];

function handleUserInfo(user: UserInfo) {
  const [name, age, address] = user;
  // name: string
  // age: number
  // address: string | undefined
}
```

### Tuple Rest Elements

TypeScript allows tuples to have a variable number of elements of a specific type using rest elements. This is nice when you want a tuple to have a fixed-length beginning but a flexible-length ending:

```ts
// A tuple with a rest element
type NameAndScores = [string, ...number[]];

// All of these are valid
const nameAndScores: NameAndScores = ["Alphonse", 69, 420, 300];
const nameAndScores: NameAndScores = ["Winry", 42];
const nameAndScores: NameAndScores = ["Edward"];
```

One great use case for rest elements would be to model a command line argument pattern:

```ts
type Command = [name: string, ...args: string[]];

const gitCommit: Command = ["git", "commit", "-m", "Add new feature"];
const npmInstall: Command = ["npm", "install", "typescript"];

// Function that handles commands
function executeCommand([cmd, ...args]: Command) {
  console.log(`Executing ${cmd} with arguments: ${args.join(", ")}`);
}
```

It says "I need a command string, but everything after that is optional".

## Intersections of Types

> ❗️ Important
>
> Interface Extensions should be used over Intersections of Types. Read "Extending Interfaces" chapter for more information.

An intersection type combines multiple types into one with the `&` operator. The resulting intersection type satisfies all the component types simultaneously.

Example 1:

```ts
type Point2D = {
  x: number;
  y: number;
};

type Point3D = Point2D & {
  z: number;
};

// Equivalent to:
// type Point3D = {
//   x: number;
//   y: number;
//   z: number;
// };
```

Example 2:

```ts
type IndividualContributor = {
  id: number;
  name: string;
  tasks: string[];
};

type Manager = {
  directReports: number[];
};

type GoodManager = IndividualContributor & Manager;

const hunter: GoodManager = {
  id: 1,
  name: "Hunter Backmann",
  tasks: ["Fixing Lane's B*llsh*t code", "Vibe Coding"],
  directReports: [2, 3, 4],
};
```

### The Never Type

In TypeScript, the `never` type represents values that can't occur.

Take a look at this function that should handle 3 cases:

```ts
function handleStatusCode(code: 200 | 404 | 500) {
  if (code === 200) {
    console.log("OK");
    return;
  }
  if (code === 404) {
    console.log("Not Found");
    return;
  }
  throw new Error(`Unknown status code: ${code}`);
}
```

It only handles `200` and `404`. TypeScript isn't throwing any compiler errors, but we can configure it to do so. See, after each conditional, the type of code is narrowed down:

```ts
function handleStatusCode(code: 200 | 404 | 500) {
  if (code === 200) {
    console.log("OK");
    return;
  }
  // code is now 404 | 500
  if (code === 404) {
    console.log("Not Found");
    return;
  }
  // code is now 500
  throw new Error(`Unknown status code: ${code}`);
}
```

If we assign `code` to `never`, TypeScript will complain unless `code` has actually been narrowed down to no possible values.

```ts
function handleStatusCode(code: 200 | 404 | 500) {
  if (code === 200) {
    console.log("OK");
    return;
  }
  if (code === 404) {
    console.log("Not Found");
    return;
  }
  // Type '500' is not assignable to type 'never'.
  const err: never = code;
  return err;
}
```

And now it's fixed by simply handling every case properly:

```ts
function handleStatusCode(code: 200 | 404 | 500) {
  if (code === 200) {
    console.log("OK");
    return;
  }
  if (code === 404) {
    console.log("Not Found");
    return;
  }
  if (code === 500) {
    console.log("Internal Server Error");
    return;
  }
  // no errors! code is never
  const err: never = code;
  return err;
}
```

### Intersecting Incompatible Types

```ts
type Saiyan = {
  name: "goku" | "vegeta";
  powerLevel: number;
};

type Human = {
  name: "krillin" | "yamcha";
  age: number;
};

type SaiyanHuman = Saiyan & Human;

// Type '{}' is not assignable to type 'never'
const theLaneagen: SaiyanHuman = {};
```

Now the `name` property can't possibly satisfy both! `Humans` must be `krillin` or `yamcha`, and `Saiyans` must be `goku` or `vegeta`. So, the `name` property in `SaiyanHuman` becomes `never`, which in turn makes the entire `SaiyanHuman` type `never`.

### Intersections vs. Unions

#### Unions

- Use the `|` operator (like logical OR)
- Widen the resulting type (more possible values)
- Useful for modeling mutually exclusive options or states

#### Intersections

- Use the `&` operator (like logical AND)
- Narrow the resulting type (fewer possible values)
- Useful for combining multiple constraints or adding more required properties to existing types

### Super Set Unions

Use case: Allow all values of a certain type, with explicitly defining a few default values for which our code editor will give us autocomplete.

Example:

```ts
// Without a Super Set Union:
type ErrorCodes = "OK" | "NOT_FOUND" | "INTERNAL_ERROR" | string;

// With a Super Set Union:
type ErrorCodes = "OK" | "NOT_FOUND" | "INTERNAL_ERROR" | (string & {});
```

When `string` is replaced with `(string & {})`, TypeScript won't change which values are allowed. Any string is allowed. But it will still give us autocomplete in our editor for the values "OK", "NOT_FOUND", and "INTERNAL_ERROR".

## Interfaces

There are two ways to define object types: the `type` keyword and `interface` keyword:

```ts
type Superhero = {
  name: string;
  powers: string[];
  isAvenger: boolean;
};

interface Superhero {
  name: string;
  powers: string[];
  isAvenger: boolean;
}
```

Notice how `interface` does not require `=` or `;` at the end.

### Extending Interfaces

```ts
interface Character {
  name: string;
  level: number;
}

interface Wizard extends Character {
  spellbook: string[];
  mana: number;
}
```

A `Wizard` now has all four properties: `name`, `level`, `spellbook`, and `mana`.

#### Why Is “Interface Extends” Usually Better?

Put simply, with interfaces the developer ergonomics are a bit better and compilation is a bit faster.

To quote [Microsoft's wiki](https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections):

> Interfaces create a single flat object type that detects property conflicts, which are usually important to resolve! Intersections on the other hand just recursively merge properties, and in some cases produce never. Interfaces also display consistently better, whereas type aliases to intersections can't be displayed in part of other intersections. Type relationships between interfaces are also cached, as opposed to intersection types as a whole. A final noteworthy difference is that when checking against a target intersection type, every constituent is checked before checking against the "effective"/"flattened" type.
>
> For this reason, extending types with interfaces/extends is suggested over creating intersection types.

#### Extending Multiple Interfaces

```ts
type Character = {
  name: string;
  level: number;
};

interface Magical {
  mana: number;
  castSpell(spell: string): void;
}

interface Physical {
  strength: number;
  attack(): void;
}

interface BattleMage extends Character, Magical, Physical {
  combineAttacks(): void;
}
```

BattleMage now has all 7 properties and methods:
- name
- level
- mana
- castSpell
- strength
- attack
- combineAttacks

### Overriding Interface Properties

Works:

```ts
interface Character {
  rank: string | number;
  name: string;
  level: number;
}

interface Wizard extends Character {
  // Wizards only have a number rank
  // This is allowed because
  // `number` is assignable to `string | number`
  rank: number;
  mana: number;
}
```

Doesn't work:

```ts
interface Character {
  rank: string;
  name: string;
  level: number;
}

interface Wizard extends Character {
  // This breaks because `number` is
  // not assignable to `string`
  rank: number;
  mana: number;
}
```

### Declaration Merging

Declaration merging is useful in certain niche cases (like modifying the global `Window` type in front-end code), but most of the time it leads to confusing bugs.

This:

```ts
interface Spaceship {
  name: string;
}

interface Spaceship {
  engines: number;
}

interface Spaceship {
  lightSpeed: boolean;
}
```

Is the same as this:

```ts
interface Spaceship {
  name: string;
  engines: number;
  lightSpeed: boolean;
}
```

If you use the `type` keyword instead, you'll get an error that you can't redeclare the type:

```ts
type Spaceship = {
  name: string;
};

// Duplicate identifier 'Spaceship'
type Spaceship = {
  engines: number;
};
```

## Enums

Example:

```ts
enum Direction {
  North, // 0
  East, // 1
  South, // 2
  West, // 3
}

let myDirection: Direction = Direction.North;
console.log(myDirection); // Outputs: 0
```

The killer feature of enums is that in your code you can have nicely named identifiers like `Direction.North`, and under the hood you can have simple unique values, like `0`. Typescript automatically increments the underlying values for us as we define new enums.

You can also explicitly set the values, and TypeScript will ensure they're unique:

```ts
enum StatusCode {
  OK = 200,
  Created = 201,
  BadRequest = 400,
  Unauthorized = 401,
  NotFound = 404,
}
```

#### Bidirectional Mapping

Numeric enums are bidirectional, which just means you can easily convert from the underlying value to the name and vice versa:

```ts
const directionValue: number = Direction.South;
// 2
const directionName: string = Direction[directionValue];
// "South"
```

### String Enums

```ts
enum LogLevel {
  ERROR = "ERROR",
  WARN = "WARN",
  INFO = "INFO",
  DEBUG = "DEBUG",
}

function structuredLog(message: string, level: LogLevel) {
  console.log(`[${level}] ${message}`);
}

structuredLog("User not found", LogLevel.ERROR);
// Outputs: [ERROR] User not found
```

When enums only exist within your code, numeric enums are totally fine. They start to get really hairy when you need to serialize them to JSON or store them in a database. There's nothing worse than debugging API responses and seeing this:

```ts
{
  "id": "94e83b65-ae9c-47f4-b788-d3f4fd085067",
  "name": "Lane",
  "user_type": 7 // what the h*ck is 7?!?!?
}
```

### Enum Compilation

Unlike most TypeScript features, enums generate additional JavaScript code at runtime.

When we compile this enum:

```ts
enum Class {
  Rogue,
  Mage,
  Warrior,
  Priest,
}
```

We get a JavaScript object that looks like this:

```js
var Class;
(function (Class) {
  Class[(Class["Rogue"] = 0)] = "Rogue";
  Class[(Class["Mage"] = 1)] = "Mage";
  Class[(Class["Warrior"] = 2)] = "Warrior";
  Class[(Class["Priest"] = 3)] = "Priest";
})(Class || (Class = {}));
```

#### String Enum Compilation

Strings compile in a similar way:

```ts
enum Class {
  Rogue = "Rogue",
  Mage = "Mage",
  Warrior = "Warrior",
  Priest = "Priest",
}
```

compiles to:

```js
var Class;
(function (Class) {
  Class["Rogue"] = "Rogue";
  Class["Mage"] = "Mage";
  Class["Warrior"] = "Warrior";
  Class["Priest"] = "Priest";
})(Class || (Class = {}));
```

String enums do not support reverse mapping - the compiled JavaScript only maps from name to string value, not the other way around.

### Const Enums

There's a special variant of enums, `const enums`, which are completely removed during compilation and replaced with their literal values. Unlike regular enums, they don't ship extra mapping code.

```ts
const enum Direction {
  North = "NORTH",
  East = "EAST",
  South = "SOUTH",
  West = "WEST",
}

const whereWinterComesFrom = Direction.North;
```

Const enums are more performant, but do come with some limitations:
1. No computed values: They can reference other enum members, but can't use arbitrary expressions.

```ts
const enum FavoriteActor {
  BradPitt = "Brad Pitt",
  AngelinaJolie = "Angelina Jolie",
  // this is okay, it references enum members
  BestCouple = FavoriteActor.BradPitt + " and " + FavoriteActor.AngelinaJolie,
}

const enum FavoriteActor {
  BradPitt = "Brad Pitt",
  AngelinaJolie = "Angelina Jolie",
  // this is not okay
  // const enum member initializers must be constant expressions
  BestCouple = getBestCouple(),
}
```

2. Mapping issues: Const enums don't have runtime representation, so getting the name from the number isn't possible.

```ts
const enum Direction {
  North, // 0
  East, // 1
  South, // 2
  West, // 3
}

const directionValue = Direction.West;

// This errors:
// A const enum member can only be accessed using a string literal.(2476)
const directionName = Direction[directionValue];

// and if you do use a string literal, it just returns the value again
const directionValueAgain = Direction["West"];
// 3
```

### Enums vs. Union Types

```ts
enum CardSuit {
  Hearts = "Hearts",
  Diamonds = "Diamonds",
  Clubs = "Clubs",
  Spades = "Spades",
}

// VS

type CardSuit = "Hearts" | "Diamonds" | "Clubs" | "Spades";
```

#### Pros of Unions
- Unions are what you use for complex types, so it feels consistent to use them for primitives as well
- Unions don't add any additional runtime code
- It's less verbose to write a union

#### Pros of Enums
- Enums are slightly easier to refactor because if you change the value of a label (e.g. "Hearts" to "hearts"), you don't have to change the string literal in every place you use it.
- If you're using numerical enums, then the reverse mapping can be useful.
- `CardSuit.Hearts` provides more context than just `"Hearts"`. That said, any good editor is going to say type `CardSuit` on hover, so it's not a huge win.

## Narrowing Types

Type narrowing is the simple process of making a type more and more specific as you write your code. As a general rule the more specific your types, the better. With narrower types:
- Your editor tooling will be more helpful
- Your code will self-document much better
- You'll catch more errors at compile time.

#### Conditional Narrowing

```ts
type WitcherCharacter = {
  type: "witcher";
  name: string;
  magicPower: boolean;
};

type StarWarsCharacter = {
  type: "star-wars";
  name: string;
  forceSensitive: boolean;
};

type Character = WitcherCharacter | StarWarsCharacter;

function fight(player1: Character, player2: Character) {
  if (player1.type === "witcher" && player2.type === "witcher") {
    // I don't need to type cast (convert)
    // player1 and player2 to WitcherCharacter - TypeScript
    // does that automatically because this branch of the
    // conditional narrows the type
    fightWitcher(player1, player2);
  } else if (player1.type === "star-wars" && player2.type === "star-wars") {
    // same thing here
    fightStarWars(player1, player2);
  } else {
    throw new Error("Can't fight characters from different universes");
  }
}

function fightWitcher(player1: WitcherCharacter, player2: WitcherCharacter) {
  // witcher specific logic
}

function fightStarWars(player1: StarWarsCharacter, player2: StarWarsCharacter) {
  // star wars specific logic
}
```

### Unknown Type

The `any` type in TypeScript can represent anything.
The `unknown` type is a much safer alternative because it forces you to explicitly assert the type before using it in a specific way.

#### The “any” Problem

The `any` type basically turns off TypeScript's type checking:

```ts
function processData(data: any) {
  // TypeScript allows this even though it might crash
  console.log(data.toLowerCase());

  // TypeScript allows this too - it's like we're using plain JavaScript
  return data.nonExistentMethod();
}

// No errors when calling the function
processData(42); // Will crash at runtime
```

#### The “unknown” Solution

```ts
function processData(data: unknown) {
  // Error: Object is of type 'unknown'
  console.log(data.toLowerCase());

  // Error: Object is of type 'unknown'
  return data.nonExistentMethod();
}
```

With `unknown`, you can still assign any value to it (e.g. call this function with any value), but you can't use that value in a meaningful way without first checking its type:

```ts
function processData(data: unknown) {
  // We do a type assertion
  if (typeof data === "string") {
    // Now TypeScript knows data is a string
    console.log(data.toLowerCase());
    return data;
  }
  if (typeof data === "number") {
    // Now TypeScript knows data is a number
    return data * 2;
  }

  // Throw an error for other types
  // that we can't handle
  throw new Error("Expected string data");
}
```

#### When to Use `unknown`

`unknown` is a fantastic alternative to `any` when it comes to dealing with values that are coming into your program from the outside world (e.g. user input, API responses, etc.). It forces you to add type checks at that I/O boundary so that you can then be confident working with the data inside your program.

### Narrowing Using In

The `in` operator checks if a property exists in an object, which is fantastic for type narrowing in object literals.

```ts
type TextMessage = {
  content: string;
  sentAt: Date;
};

type ImageMessage = {
  caption: string;
  sentAt: Date;
};

type VideoMessage = {
  duration: number;
  sentAt: Date;
};

type Message = TextMessage | ImageMessage | VideoMessage;

function displayMessage(message: Message) {
  if ("content" in message) {
    // TypeScript knows this is a TextMessage
    // because it's the only one with a 'content' property
    console.log(`Text content is: ${message.content}`);
  } else if ("caption" in message) {
    // TypeScript knows this is an ImageMessage
    // because it's the only one with an 'caption' property
    console.log(`Image caption is ${message.caption}`);
  } else {
    // TypeScript knows this is a VideoMessage because
    // it's the only other option
    console.log(`Video length is ${message.duration}`);
  }
}
```

#### Discriminated Unions vs. 'in' Checks

You might have noticed that this kind of logic feels very similar to using discriminated unions, and you're correct. Here's the same types with an explicit discriminant property:

```ts
type TextMessage = {
  kind: "text";
  content: string;
  sentAt: Date;
};

type ImageMessage = {
  kind: "image";
  caption: string;
  sentAt: Date;
};

type VideoMessage = {
  kind: "video";
  duration: number;
  sentAt: Date;
};
```

From boot.dev:

> My recommendation is to prefer a discriminated union when you have full control of the types, but if you're using types from a library or package, or have another reason you don't want extra properties, the `in` operator is a great alternative.

### Type Predicates

Sometimes the built-in type guards (`typeof`, `instanceof`, etc.) aren't enough.

TypeScript allows you to create your own type guards using type predicates. We do that by creating a function that:

- Accepts a wide type that we want to narrow
- Returns a boolean indicating if the value is of the desired type
- Uses the type predicate syntax `value is Type` in the return type

For example, here's a function that reports if a value is a string:

```ts

function isString(value: unknown): value is string {
  return typeof value === "string";
}

function processValue(value: unknown) {
  if (isString(value)) {
    // TypeScript knows value is a string here
    console.log(value.toUpperCase());
  }
}
```

Type predicates become really useful when the logic to check the type is a bit more complex. So we have a situation where one type, in this case, the `ManagerAdmin` type, shares properties with both other types:

```ts
interface ManagerAdmin {
  accessLevel: number;
  numEmployees: number;
}

interface Admin {
  accessLevel: number;
  payrollDate: Date;
}

interface Manager {
  numEmployees: number;
}
```

We can encapsulate the slightly more complex logic in a type predicate function:

```ts
function isManagerAdmin(
  boss: ManagerAdmin | Admin | Manager,
): boss is ManagerAdmin {
  return "numEmployees" in boss && "accessLevel" in boss;
}

// boss is a `ManagerAdmin | Admin | Manager`
if (isManagerAdmin(boss)) {
  // TypeScript knows boss is a ManagerAdmin here
  console.log(`Managing ${boss.numEmployees} employees`);
}
```

### Exhaustive Checks

```ts
type Notif = "email" | "sms" | "push";

function sendNotification(notif: Notif) {
  switch (notif) {
    case "email":
      return "Sending email";
    case "sms":
      return "Sending SMS";
    case "push":
      return "Sending push notification";
  }
  return "Unknown notification type";
}
```

This might be a very reasonable way to write JavaScript code, but that final `return "Unknown notification type";` is actually redundant in good TypeScript code. The switch statement is exhaustive, and TypeScript is smart enough to know that `return "Unknown notification type";` is actually unreachable code, and will give us a compiler error (assuming we have configured tsc to do so)!

Design your types so that you get these kinds of useful errors.

### Guard Clauses

Peak production TypeScript code is often riddled with `undefined` and `null` types due to the nature of I/O and external APIs, so this is a classic pattern:

```ts
function processName(name: string | null | undefined) {
  if (name === null || name === undefined) {
    return "";
  }
  // TypeScript knows name is a string here
  return name.toUpperCase();
}
```

Now, an empty string keeps processName's behavior straightforward, (always returning a string), but depending on your use case, it might make more sense to throw an error instead:

```ts
function processName(name: string | null | undefined) {
  if (name === null || name === undefined) {
    throw new Error("Name is required");
  }
  // TypeScript knows name is a string here
  return name.toUpperCase();
}
```

Interestingly, throwing an error still narrows the type, but it doesn't change the function signature - this function still just returns a string. That's because errors in JavaScript and TypeScript are a control flow mechanism, not a type mechanism, so you do just kind of need to be aware: "Hey this function can throw, I need to handle that."

In cases where my program won't break on an empty string, I might just coalesce to an empty string instead of throwing an error. This happens all the time with optional fields in web apps.

### Type Assertion

The `as` keyword is the "trust me, bro" of TypeScript and should be avoided unless a really good use case demands it.

```ts
// Property 'toLowerCase' does not exist on type 'string | string[]'
const userId = route.query?.userId.toLowerCase();

// OK
const userId = (route.query?.userId as string).toLowerCase();
```

We also capture values that come across the network as `unknown` and then use `as` to assert them into the shape we expect a given network response to be:


```ts
type User = {
  id: string;
  name: string;
};

async function getUserRaw(userId: string): Promise<unknown> {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
}

export async function getUser(userId: string) {
  const data = await getUserRaw(userId);
  // here data is still just "unknown"
  // so we assert it to a User type
  return data as User;
}
```

#### Angle Bracket Syntax

There is an alternative syntax for type assertions using angle brackets and the type before the value:

```ts
const userIdRaw = <string>route.query?.userId;
const userId = userIdRaw.toLowerCase();
```

### Double Assertion

As a general rule, don't use Double Assertion.

TypeScript won't allow you to assert absolute nonsense:

```ts
const num = 42;

// Error: Conversion of type 'number' to type
// 'string' may be a mistake because neither
// type sufficiently overlaps with the other.
const str = num as string;
```

The `number` and `string` types have no overlap, making this assertion likely to be a mistake, so TypeScript complains. We can get around this with a double assertion:

```ts
const id = 42;

// This works - but is very unsafe!
const userId = id as unknown as string;

// Now TypeScript treats this as a string
console.log(userId.toUpperCase());
// Compiles, but still CRASHES at runtime!
```

### Non-Null Assertion

It's common for TypeScript libraries to assume that a value can be `null` or `undefined` even when you know it can't be. You can assert that it's not with the non-null assertion (`!`) operator. It tells the compiler that a value cannot be `null` or `undefined`, even when the type system thinks it might be.

```ts
// assume getCleanedText returns a string | null
import { getCleanedText, sendText } from "./text-utils";

const cleanedText = getCleanedText("some text");
// cleanedText is string | null
// but we know that it's not null because we passed in a valid string

// sendText expects a string, so we use a non-null assertion
sendText(cleanedText!);
```

You'll also see this fairly often when working with optional properties that you know exist:

```ts
interface User {
  id: string;
  name?: {
    first: string;
    last: string;
  };
}

// we don't control the User type (it's imported from a library)
// but we know that we always use the `name` property
sentText(user.name!.first);
```

This is always safer, albeit more verbose:

```ts
function sendTextSafely(text: string | null) {
  if (text === null) {
    throw new Error("Text is required");
  }
  sendText(text);
}
```

## Classes

```ts
class Hero {
  name: string;
  health: number;

  constructor(name: string, health: number) {
    this.name = name;
    this.health = health;
  }

  attack(damage: number): void {
    console.log(`${this.name} attacks for ${damage} damage!`);
  }

  getHealth() {
    return this.health;
  }
}

// Create an instance
const geralt = new Hero("Geralt", 100);
geralt.attack(25);
// "Geralt attacks for 25 damage!"
console.log(geralt.getHealth());
// 100
```

### Private Class Members

JavaScript added support for private class members in ES2022 with the `#` syntax. In plain JavaScript, we'd only get the error at runtime, but with the same syntax in TypeScript, we get the error at compile time.

```ts
class SecretAgent {
  // a private field
  #id: string;

  constructor(id: string) {
    this.#id = id;
  }

  // a public method
  getCodeName(): string {
    const idToCodeNameMap: Record<string, string> = {
      "007": "James Bond",
      "006": "Alec Trevelyan",
      // Add more mappings as needed
    };
    return idToCodeNameMap[this.#id] || "Unknown Agent";
  }
}

const bond = new SecretAgent("007");
console.log(bond.getCodeName()); // "James Bond"

// Property '#id' is not accessible outside class 'SecretAgent' because it has a private identifier.
console.log(bond.#id);
```

### TypeScript Public and Private

> ❗ Note
> 
> Don't use this in newer projects.

JavaScript's `#` private fields didn't come until ES2022, but TypeScript developers had wanted public/private/protected access modifiers for a long time, so TypeScript added support for `private` and `protected` before then. So a lot of older TypeScript code uses the keyword syntax.

```ts
class SecretAgent {
  // private field using the private keyword
  private id: string;

  constructor(id: string) {
    this.id = id;
  }

  // a public method
  getCodeName(): string {
    const idToCodeNameMap: Record<string, string> = {
      "007": "James Bond",
      "006": "Alec Trevelyan",
      // Add more mappings as needed
    };
    return idToCodeNameMap[this.id] || "Unknown Agent";
  }
}

const bond = new SecretAgent("007");
console.log(bond.getCodeName()); // "James Bond"

// Property 'id' is private and only accessible within class 'SecretAgent'
console.log(bond.id); // This will cause a compilation error
```

### Protected Data Members

The `protected` keyword is unique to TypeScript in that it's not part of the EcmaScript standard. It allows you to define members that are accessible within the class and its subclasses, but not from outside the class. It's like "private but also accessible to subclasses".

```ts
class Character {
  protected health: number;

  constructor(health: number) {
    this.health = health;
  }

  protected takeDamage(amount: number): void {
    this.health -= amount;
    if (this.health < 0) {
      this.health = 0;
    }
  }
}

class Fighter extends Character {
  constructor(health: number) {
    super(health);
  }

  public fight(damage: number): void {
    // Can access protected members from the parent class
    this.takeDamage(damage);
    console.log(`Fighter took ${damage} damage. Health: ${this.health}`);
  }
}

const fighter = new Fighter(100);
fighter.fight(30);

// Error: Property 'health' is protected and only accessible within class 'Character' and its subclasses
console.log(fighter.health);

// Error: Property 'takeDamage' is protected and only accessible within class 'Character' and its subclasses
fighter.takeDamage(10);
```

Lane @ boot.dev:

> The protected keyword does not have a native JavaScript alternative. I personally don't use it very often. I tend to use # private fields whenever possible, or just leave them public if subclasses need access.

### Abstract Classes and Methods

An `abstract` class is a class that cannot be instantiated directly. It's a template for inheritance, forcing subclasses to implement specific methods or properties. Say we have this `Shape` class:

```ts
abstract class Shape {
  size: "small" | "medium" | "large";
  constructor(size: "small" | "medium" | "large") {
    this.size = size;
  }

  abstract calculateArea(): number;

  displayArea(): void {
    console.log(`The area of this shape is ${this.calculateArea()}`);
  }
}

// Error: Cannot create an instance of an abstract class
const shape = new Shape("small");
```

Within an `abstract` class, abstract methods (like `calculateArea` above) do not have an implementation because the implementation must be provided by the subclass. However, it can still have regular methods (like `displayArea` above) which are then shared by all subclasses.

So, we can create a `Circle` class that extends `Shape` and implements the `calculateArea` method:

```ts
class Circle extends Shape {
  radius: number;
  constructor(size: "small" | "medium" | "large") {
    super(size);
    if (this.size === "small") {
      this.radius = 5;
    } else if (this.size === "medium") {
      this.radius = 10;
    } else {
      this.radius = 15;
    }
  }
  calculateArea(): number {
    return Math.PI * this.radius * this.radius;
  }
}

const circle = new Circle("medium");
circle.displayArea();
// The area of this shape is 314.1592653589793
```

### Classes Implement Interfaces

Classes can implement interfaces using the `implements` clause. This enforces that the class adheres to the structure defined by the interface. Say we have two interfaces:

```ts
interface Vehicle {
  make: string;
  model: string;
}

interface Drivable {
  drive(distance: number): void;
}
```

And we have a class that we want to implement (have the properties and methods of) both interfaces:

```ts
class ElectricCar {
  make: string;
  model: string;
}
```

We can add a clause to the class definition to implement both interfaces. However, because at the moment, the class doesn't have a `drive` method, TypeScript will throw an error:

```ts
// Error: Class 'ElectricCar' incorrectly implements interface 'Drivable'.
class ElectricCar implements Vehicle, Drivable {
  make: string;
  model: string;
}
```

So, now we're reminded to add the `drive` method, and we do so:

```ts
class ElectricCar implements Vehicle, Drivable {
  make: string;
  model: string;

  // not required by the interfaces, but it's
  // okay to add extra properties
  private isRunning: boolean = false;

  constructor(make: string, model: string) {
    this.make = make;
    this.model = model;
    this.isRunning = false;
  }

  drive(distance: number): void {
    this.isRunning = true;
    console.log(`Driving ${distance} miles`);
  }
}
```

We can now use an instance of `ElectricCar` as a `Vehicle` or `Drivable`:

```ts
const myCar = new ElectricCar("Tesla", "Model S");

function testDrive(vehicle: Vehicle) {
  console.log(`Testing ${vehicle.make} ${vehicle.model}`);
}

testDrive(myCar); // "Testing Tesla Model S"

function takeForARide(drivable: Drivable) {
  drivable.drive(10);
}

takeForARide(myCar); // "Driving 10 miles"
```

### Classes vs. Interfaces and Types

You might be wondering when you should use a full-blown class to create reusable object types over interfaces and type aliases. There are 3 ways to model the same thing!

```ts
class Hero {
  name: string;
  health: number;
}

interface Hero {
  name: string;
  health: number;
}

type Hero = {
  name: string;
  health: number;
};
```

If you're an object-oriented programmer, you might be more comfortable with classes and the extra features they provide. Classes can basically do everything that interfaces can do, and more. Some of the most notable things you can't do with interfaces and type aliases are:
- Have private, protected, static, and abstract members
- Have dedicated constructors
- Have method implementations predefined on all instances

And on the other hand:
- Type aliases and interfaces have no runtime overhead
- Type aliases and interfaces have fewer features, and as a result, are simpler to work with when you don't need the extra features
- Type aliases and interfaces are more flexible, especially when working with plain objects because they're not tied to the class implementation (signature only)
- Interfaces can be extended and merged in ways that types and classes can't

### The “this” Type

Luckily, TypeScript is smart enough to handle the funky `this` keyword for us, because as JavaScript developers, we know that the only question more difficult than "what is the meaning of life?" is "what is the value of `this`?".

```ts
class Counter {
  private count: number = 0;

  increment(): void {
    // 'this' is implicitly typed as Counter
    this.count++;
  }

  getCount(): number {
    // 'this' is implicitly typed as Counter
    return this.count;
  }
}
```

#### Explicit `this` Parameters

TypeScript is pretty smart (especially newer versions) and usually infers the type of `this` correctly. However, if you want to explicitly control the type of `this`, you can use the [special `this` parameter](https://www.typescriptlang.org/docs/handbook/2/functions.html#declaring-this-in-a-function):

```ts
class Counter {
  private count: number = 0;

  increment(this: Counter, n: number): void {
    // 'this' is explicitly typed as Counter
    // the `this` parameter is not available at runtime
    // it is only used for type checking
    this.count += n;
  }

  getCount(this: Counter): number {
    // 'this' is explicitly typed as Counter
    return this.count;
  }
}

const counter = new Counter();
counter.increment(5);
console.log(counter.getCount());
// 5
```

### Parameter Properties

TypeScript has a neat shorthand feature called parameter properties that allows you to declare and initialize class properties directly in the constructor parameters. This eliminates the need to separately declare properties and then assign them in the constructor body.

```ts
class Hero {
  constructor(
    public name: string,
    public health: number,
    private level: number,
  ) {}
}
```

By adding an access modifier (`public`, `private`, `protected`, or `readonly`) to a constructor parameter, TypeScript automatically:
- Declares a property with the same name and type
- Assigns the parameter value to that property


#### Limitation

Parameter properties work with TypeScript's `private` keyword, but not with JavaScript's `#` private field syntax. If you need truly private fields using the `#` syntax, you must declare them separately:

```ts
class Hero {
  #secretPower: string;

  constructor(
    public name: string,
    secretPower: string,
  ) {
    this.#secretPower = secretPower;
  }
}
```

## Utility Types

### Single Source of Truth

It's incredibly common for a TypeScript codebase to amass a truly absurd number of custom type definitions - hundreds of interfaces and types, all with slightly different numbers of fields for a lot of the same "entities". You might run into crazy stuff like:

```ts
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
}

interface UserWithoutId {
  name: string;
  email: string;
  age: number;
}
```

This is bad. We generally try to avoid redefining the same types over and over, and instead try to follow a "single source of truth" approach. For example, here we could refactor a bit:

```ts
interface UserWithoutId {
  name: string;
  email: string;
  age: number;
}

interface User extends UserWithoutId {
  id: string;
}
```

In other words, we try to define our types once, and build type systems that rely on inference and type transformations to derive the types we need automatically. That way, when we make changes, we only have to do it in one place. In our second example, updating `UserWithoutId` will now automatically update `User` as well.

### Partial Utility Type

Partial<T> built-in utility type makes all properties of a type optional.

```ts
type User = {
  id: string;
  name: string;
  email: string;
};

// Without Partial
function updateUser(
  userId: string,
  userInfo: {
    id?: string;
    name?: string;
    email?: string;
  },
) {
  // ...
}

// With Partial
function updateUser(userId: string, userInfo: Partial<User>) {
  // ...
}
```

If the original type is ever updated, the new type created with `Partial<T>` type will automatically have those changes.

#### Nested Objects

`Partial<T>` only makes the top-level properties optional. For example, if we use `Partial<User>`, the resulting type would look like this:

```ts
type User = {
  id: string;
  name: string;
  preferences: {
    theme: string;
    notifications: boolean;
  };
};

// same as 'type LooseyGooseyUser = Partial<User>'
type LooseyGooseyUser = {
  id?: string;
  name?: string;
  preferences?: {
    theme: string;
    notifications: boolean;
  };
};
```

### Required Utility Type

The `Required<T>` utility type does the opposite of `Partial<T>` - it forces all properties of a type to be required, even those that were originally optional. As before, the `Required<T>` utility type is not recursive, it only affects the top-level properties.

```ts
interface BlogPost {
  title: string;
  content: string;
  tags?: string[];
  publishDate?: Date;
  author?: {
    id: string;
    name?: string;
  };
}

// All properties are now required
type MyRequiredBlogPost = Required<BlogPost>;

// MyRequiredBlogPost is equivalent to:
// {
//   title: string;
//   content: string;
//   tags: string[];
//   publishDate: Date;
//   author: {
//     id: string;
//     name?: string;
//   };
// }
```

### Readonly Utility Type

The `Readonly<T>` utility creates a new type where all the top-level properties are `readonly`, preventing them from being reassigned after initialization.

```ts
interface UserProfile {
  id: string;
  name: string;
  preferences: {
    readonly theme: "light" | "dark";
    notifications: boolean;
  };
}

type ConstantUserProfile = Readonly<UserProfile>;

// this is the same as
// type ConstantUserProfile = {
//   readonly id: string;
//   readonly name: string;
//   readonly preferences: {
//     readonly theme: "light" | "dark";
//     notifications: boolean;
//   };
// }
```

### Record Utility Type

The `Record<K, T>` utility type creates a type with a set of properties `K` of type `T`.

```ts
// Using string as the key type
type StringKeyDictionary = Record<string, number>;

const karateScores: StringKeyDictionary = {
  "Ralph Macchio": 60,
  "William Zabka": 100,
  "Jackie Chan": 82,
};

// We can add any string key
karateScores["Pat Morita"] = 85;

// But values must be numbers
// Error: Type 'string' is not assignable to type 'number'
karateScores["Eve"] = "A+";
```

You might be wondering, "wait, there's another way to define a key/value pair in TypeScript?"... yeah. I know we're up to like 5 different ways at this point. However, one of the more practical use cases for `Record` is to ensure that all specified keys in a union are present in the object:

```ts
// Using a union of literal types as keys
type PlayerRole = "tank" | "healer" | "dps";
type RoleCapacity = Record<PlayerRole, number>;

const partyRequirements: RoleCapacity = {
  tank: 1,
  healer: 2,
  dps: 3,
};

// TypeScript error if any role is missing
const invalidRequirements: RoleCapacity = {
  tank: 1,
  dps: 3,
  // Error: Property 'healer' is missing in type '{ tank: number; dps: number; }'
};

// We can't add additional keys not in the union
// Error: Property 'support' does not exist on type 'RoleCapacity'
partyRequirements["support"] = 1;
```

It's fantastic for exhaustive lookup tables and configuration objects:

```ts
type HttpStatusCode = 200 | 201 | 400 | 401 | 403 | 404 | 500;

const statusMessages: Record<HttpStatusCode, string> = {
  200: "OK",
  201: "Created",
  400: "Bad Request",
  401: "Unauthorized",
  403: "Forbidden",
  404: "Not Found",
  500: "Internal Server Error",
};

function getStatusMessage(code: HttpStatusCode): string {
  return statusMessages[code];
}

console.log(getStatusMessage(404));
// "Not Found"
```

### Pick Utility Type

The `Pick<T, K>` utility type creates a new type by selecting a subset of properties from an existing type. `Pick` is very useful for creating quick types for functions that don't need everything from the original type.

```ts
interface Product {
  id: string;
  name: string;
  price: number;
  description: string;
  category: string;
  inStock: boolean;
  images: string[];
  reviews: { user: string; rating: number; text: string }[];
}

type ProductSummary = Pick<Product, "id" | "name" | "price">;

const productList: ProductSummary[] = [
  { id: "p1", name: "Keyboard", price: 79.99 },
  { id: "p2", name: "Mouse", price: 59.99 },
];

const invalidProduct: ProductSummary = {
  id: "p3",
  name: "Headphones",
  price: 99.99,
  // TSC error:
  // Object literal may only specify known properties, and 'description' does not exist in type 'ProductSummary'.
  description: "Noise cancelling headphones",
};
```

### Omit Utility Type

The `Omit<T, K>` utility type is the opposite of `Pick<T, K>`. It creates a new type by excluding a set of properties from an existing type. I find this one to be very useful when removing sensitive or unnecessary properties from a type. For example, maybe you need to remove a password field from a user object before responding to an API request.

```ts
interface DatabaseUser {
  id: string;
  username: string;
  email: string;
  passwordHash: string;
  createdAt: Date;
  updatedAt: Date;
}

// Create a safe user representation without sensitive data
type PublicUser = Omit<DatabaseUser, "passwordHash" | "updatedAt">;

function getUserProfile(userId: string): PublicUser {
  // Fetch user from database...
  const dbUser: DatabaseUser = {
    id: userId,
    username: "johndoe",
    email: "john@example.com",
    passwordHash: "$2a$12$...",
    createdAt: new Date("2023-01-15"),
    updatedAt: new Date()
  };

  // Convert to PublicUser (explicit conversion for clarity)
  const publicUser: PublicUser = {
    id: dbUser.id,
    username: dbUser.username,
    email: dbUser.email,
    createdAt: dbUser.createdAt

    // TSC error:
    // Object literal may only specify known properties, and 'passwordHash' does not exist in type 'PublicUser'.
    passwordHash: dbUser.passwordHash,
  };

  return publicUser;
}
```

## Generics

Generics allow you to create reusable logic that works with many types rather than a single one. Think of a data structure like a Queue or a Stack. They can hold any type of data, so it would be really annoying to reimplement them for every type:
- NumberQueue
- StringQueue
- UserQueue
- etc.

Generics let us create a single `Queue<T>` type that can work with any type `T`.

The simplest example:

```ts
function identity<Type>(arg: Type): Type {
  return arg;
}
```

Notice:

```ts
fetchFromApi<T>(url: string): Promise<T | undefined>
```

in the following code:

```ts
async function fetchFromApi<T>(url: string): Promise<T | undefined> {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    return await response.json();
  } catch (error) {
    console.error("Error fetching data:", error);
    return undefined;
  }
}
```

Whenever we call `fetchFromApi`, we just specify the type we expect to get back:

```ts
const comments = await fetchFromAPI<Comment[]>(
  "https://api.example.com/posts/1/comments",
);

const user = await fetchFromApi<User>("https://api.example.com/user/1");

const posts = await fetchFromApi<Post[]>("https://api.example.com/posts");
```


### Multiple Type Parameters

```ts
function transform<InputType, OutputType>(
  inputs: InputType[],
  update: (item: InputType) => OutputType,
): OutputType[] {
  const outputs: OutputType[] = [];
  for (const input of inputs) {
    const output = update(input);
    outputs.push(output);
  }
  return outputs;
}
```

[Type parameters](https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-functions) in the above code are `InputType` and `OutputType`. They're usually called `T`, `U`, `V`, etc.

We can use our own custom transformers with our custom transform function.

```ts
type Human = {
  name: string;
  age: number;
};

const humans: Human[] = [
  { name: "Eren", age: 15 },
  { name: "Mikasa", age: 16 },
  { name: "Armin", age: 15 },
];

const titanTransformer = (human: Human): string => `${human.name} is a titan!`;

const titanNames = transform<Human, string>(humans, titanTransformer);
console.log(titanNames);
// ['Eren is a titan!', 'Mikasa is a titan!', 'Armin is a titan!']
```

Without changing our transform function, we can use it to transform entirely different types of data:

```ts
const numbers = [1, 2, 3, 4, 5];
const double = (num: number): number => num * 2;

const doubledNumbers = transform<number, number>(numbers, double);
console.log(doubledNumbers);
// [2, 4, 6, 8, 10]
```

### Generic Constraints

Sometimes you need your generic function to know something about the types it operates on. The examples we've used so far don't know anything about the types they're using:

```ts
async function fetchFromApi<T>(url: string): Promise<T | undefined>;
```

In `fetchFromApi`, `T` could be anything.

Constraints are just interfaces that allow us to write generics that only operate within the constraints of a given interface type. In the example above, the `any` constraint is the same as the empty interface because it means the type in question can be anything.

We can use the `extends` keyword to constrain the type parameter to have certain properties, for example:

```ts
interface HasCost {
  cost: number;
}

function applyDiscount<T extends HasCost>(vals: T[], discount: number): T[] {
  const arr: T[] = [];
  for (const val of vals) {
    val.cost *= discount;
    arr.push(val);
  }
  return arr;
}
```

### Type Parameters for Types

```ts
interface Store<T> {
  get(id: string): T;
  save(id: string, item: T): void;
  list(): T[];
}
// also works with type aliases using
// type Store<T> = { ... }
```

Now a `Store` can be anything that implements the methods above, but what is stored doesn't matter. Next we can create a function that uses the store, again, not caring about what is stored inside of it:

```ts
function addAndGetItems<T>(store: Store<T>, id: string, newItem: T): T[] {
  store.save(id, newItem);
  return store.list();
}
```

Finally, we can create a `Store` that specifically deals with `Product` types:

```ts
type Product = {
  name: string;
  price: number;
};

const productStore = {
  products: {} as Record<string, Product>,
  get(id: string): Product {
    return this.products[id];
  },
  save(id: string, item: Product): void {
    this.products[id] = item;
  },
  list(): Product[] {
    return Object.values(this.products);
  },
};
```

And we can use it like this:

```ts
const newStore = addAndGetItems(productStore, "laneslaptop", {
  name: "Laptop",
  price: 999,
});
console.log(newStore);
// [{ "name": "Laptop", "price": 999 }]
const finalStore = addAndGetItems(productStore, "allanstoaster", {
  name: "Toaster",
  price: 50,
});
console.log(finalStore);
// [{ "name": "Laptop", "price": 999 }, { name: 'Toaster', price: 50 }]
```

### Generic Type Inference

Previous code:

```ts
function transform<InputType, OutputType>(
  inputs: InputType[],
  update: (item: InputType) => OutputType,
): OutputType[] {
  const outputs: OutputType[] = [];
  for (const input of inputs) {
    const output = update(input);
    outputs.push(output);
  }
  return outputs;
}

type Human = {
  name: string;
  age: number;
};

const humans: Human[] = [
  { name: "Eren", age: 15 },
  { name: "Mikasa", age: 16 },
  { name: "Armin", age: 15 },
];

const titanTransformer = (human: Human): string => `${human.name} is a titan!`;
```

and, specifically:

```ts
const titanNames = transform<Human, string>(humans, titanTransformer);
console.log(titanNames);
```

We explicitly passed in `<Human, string>` as the type parameters. But in this case, there's no need because TypeScript knows that our `humans` variable is an array of `Human` objects, and the `titanTransformer` function takes a `Human` and returns a `string`. So we can just call:

```ts
const titanNames = transform(humans, titanTransformer);
```

### Generic Classes

Classes can be generic too. To keep it somewhat interesting, let's combine a few concepts:
- `InMemoryRepository` is a generic class
- It implements a generic interface (`Repository<T>`)
- `T` is constrained to have an `id` property

```ts
interface Repository<T> {
  getAll(): T[];
  getById(id: string): T | undefined;
  save(item: T): void;
}

class InMemoryRepository<T extends { id: string }> implements Repository<T> {
  private items: T[] = [];

  getAll(): T[] {
    return [...this.items];
  }

  getById(id: string): T | undefined {
    return this.items.find((item) => item.id === id);
  }

  save(item: T): void {
    const index = this.items.findIndex((i) => i.id === item.id);
    if (index >= 0) {
      this.items[index] = item;
    } else {
      this.items.push(item);
    }
  }
}
```

There are a few things to note about the example above:

- The purpose of using the `implements` keyword is to ensure that the class adheres to the `Repository<T>` interface - TypeScript will yell at us if our `InMemoryRepository` class can't be used as a `Repository<T>`.
- While any old `Repository<T>` doesn't need an `id` property, our `InMemoryRepository` does.
- An `InMemoryRepository` can be used to hold any type of object, as long as it has an `id` property. And all the implementation logic is shared between all the different possible types.

Let's create an `InMemoryRepository` for `Shinigami`:

```ts
interface Shinigami {
  id: string;
  name: string;
}

const deathNoteRepo = new InMemoryRepository<Shinigami>();
deathNoteRepo.save({ id: "1", name: "Ryuk" });
deathNoteRepo.save({ id: "2", name: "Rem" });
console.log(deathNoteRepo.getAll());
```

Of course, if we try to create an `InMemoryRepository` for something that doesn't have an `id` property, TypeScript will yell at us:

```ts
interface Psychopaths {
  name: "Light Yagami" | "L";
}

// Error: Type 'Psychopaths' does not satisfy the constraint '{ id: string; }'
const psychopathRepo = new InMemoryRepository<Psychopaths>();
```

## Conditional Types

```ts
type IsString<T> = T extends string ? true : false;

// Usage
type Result1 = IsString<"hello">; // true
type Result2 = IsString<42>;      // false
type Result3 = IsString<string>;  // true
```

TypeScript actually ships with some built-in conditional types:

```ts
type Extract<T, U> = T extends U ? T : never;
type Exclude<T, U> = T extends U ? never : T;
type NonNullable<T> = T extends null | undefined ? never : T;
```

Let's say we have some events that can fire in our front end application:

```ts
type ClickEvent = { type: "click"; x: number; y: number };
type KeyEvent = { type: "key"; key: string };
type MouseMoveEvent = { type: "mousemove"; x: number; y: number };
type FormEvent = { type: "submit"; formId: string };

type Event = ClickEvent | KeyEvent | MouseMoveEvent | FormEvent;
```

It may be useful to dynamically create a type that only includes "mouse-related" events: the ones that have an `x` and `y` property. We can use the `Extract` conditional type to do so:

```ts
// Extract is a TS built-in type, this is the implementation:
type Extract<T, U> = T extends U ? T : never;

// This is an example of how it can be used:
type MouseRelatedEvents = Extract<Event, { x: number; y: number }>;
```

Now `MouseRelatedEvents` is the same as:

```ts
type MouseRelatedEvents = ClickEvent | MouseMoveEvent;
```

### Infer

The `infer` keyword, when used inside a conditional type, lets us use the type of a value from the true branch. For example, `GetReturnType` is a conditional utility type that extracts the return type of a function type `T`:

```ts
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function greet() { return "Hello, world!"; }
function sum(a: number, b: number) { return a + b; }

type GreetReturnType = GetReturnType<typeof greet>; // string
type SumReturnType = GetReturnType<typeof sum>;     // number
```

Another example: Generic input type `InputTypeOf` extracts the type of the first (and only) parameter from a function type `T`. The function can return any type. Falls back to `unknown` if `T` isn't a function or doesn't take exactly one parameter.

```ts
export type InputTypeOf<T> = T extends (arg: infer A) => any ? A : unknown;
```

### Mapped Types

Remember dynamic properties?

```ts
type UserMetrics = {
  [key: string]: number;
};
```

Well, mapped types are a way to create new types with dynamic properties based on existing types. For example, say we have a `Soldier` type:

```ts
type Soldier = {
  name: string;
  age: number;
  branch: "garrison" | "military police" | "survey corps";
};
```

And we want to create a new type that has the same properties, but all of them are optional. We can do that with a mapped type:

```ts
type OptionalSoldier = {
  [K in keyof Soldier]?: Soldier[K];
};
```

- The `keyof` operator gets the keys of the `Soldier` type
- The `in` keyword iterates over them
- The `?` makes each property optional
- The `Soldier[K]` gets the value type each property maps to

It results in a type that's the same as:

```ts
type OptionalSoldier = {
  name?: string;
  age?: number;
  branch?: "garrison" | "military police" | "survey corps";
};
```

The obvious benefit, of course, is that if we update `Soldier`, `OptionalSoldier` automatically updates too.

#### Changing the Values

Mapped types are really useful for making properties `optional` or `readonly`, but it's an incredibly powerful (and potentially dangerously confusing) tool. You can also use them to change the value type of properties:

```ts
type StringifiedSoldier = {
  [K in keyof Soldier]: string;
};
```

Which is the same as:

```ts
type StringifiedSoldier = {
  name: string;
  age: string;
  branch: string;
};
```

### Mapped Types With Conditionals

Let's take our `OptionalSoldier` example from before:

```ts
type Soldier = {
  name: string;
  age: number;
  branch: "garrison" | "military police" | "survey corps";
};

type OptionalSoldier = {
  [K in keyof Soldier]?: Soldier[K];
};
```

What if instead of making all the properties optional, we instead wanted to filter any non-string properties? We can do that with a conditional mapped type:

```ts
type FilteredSoldier = {
  [K in keyof Soldier]: Soldier[K] extends string ? Soldier[K] : never;
};
```

The conditional: `Soldier[K] extends string` only evaluates to `true` (and thus the property is included as `Soldier[K]`) if the property is assignable to `string`. Otherwise, it evaluates to `never`, and the property is excluded. One really cool thing to note, is that because we used `Soldier[K]` in the conditional, the more specific type of the branch property is preserved, resulting in a type of:

```ts
type FilteredSoldier = {
  name: string;
  // age: never;
  branch: "garrison" | "military police" | "survey corps";
};
```

Another example:

```ts
export type EditableFields<T> = {
  [K in keyof T]: T[K] extends Function | object ? never : T[K];
};
```

### Extracting Keys from Types

Mapped types don't just let you build new object types – they can also be used to extract keys. Say we have this object type:

```ts
type Soldier = {
  name: string;
  age: number;
  branch: "garrison" | "military police" | "survey corps";
};
```

Now imagine you want to get just the keys of the fields that are `string`-based – maybe for a filter, a dropdown, or feeding to an LLM that summarizes records. First, we create an object where each key either returns the key name, or `never`:

```ts
type StringKeys<T> = {
  [K in keyof T]: T[K] extends string ? K : never;
};
```

That gives you something like:

```ts
type Result = {
  name: "name";
  age: never;
  branch: "branch";
};
```

Now we index into that type using all of its keys:

```ts
type StringKeyUnion<T> = StringKeys<T>[keyof T];
```

We've made the object into a union of its values:

```ts
type Keys = StringKeyUnion<Soldier>;
// "name" | "branch"
```

## Install TypeScript

Install TypeScript globally:

```bash
npm install -g typescript
```

Check its version:
```bash
tsc -v
```

### tsconfig.json

- `lib`: Add dom and dom.iterable (note: lowercase) to the list of libraries to allow all the browser APIs if you're writing front-end code.
- `strict`: If true, enables all strict type checking options. I strongly recommend it for new projects. You might need to turn it off if you're migrating an existing JS project.
- `skipLibCheck`: If true, skips type checking of all declaration files (which means it won't try to type check your infinitely large node_modules folder). Drastically speeds up compilation time.
- `verbatimModuleSyntax`: If true, simplifies some weirdness with importing and exporting types, basically it forces you to import and export types using the import type syntax. I recommend it.
- `esModuleInterop`: If true, allows you to use import syntax with CommonJS modules. Very useful if you need to work with CommonJS (Node) code.
- `moduleDetection`: If set to force, will consider everything to be a module, which is what you want in any new project.
- `noUncheckedIndexedAccess`: If true, adds undefined to the type of any indexed access, which can prevent some runtime errors. I recommend it.

### Declaration Files

If you've ever seen funky looking `.d.ts` files and wondered what they are, they're declaration files. They only contain type information - no runtime code is allowed. They're very useful for defining the types for JavaScript code that exists in your app, but that doesn't have any type information.

For example, in Boot.dev we support login with Google. We use TypeScript in our codebase, but we just include Google's JavaScript library in our HTML as per their instructions. Because we want the static type hints in our editors, we have this `globals.d.ts` file in our project:

```ts
declare global {
  interface Window {
    google: Google;
  }
}

interface Google {
  accounts: {
    id: {
      renderButton: (
        a: HTMLElement,
        b: {
          type?: string;
          theme?: string;
          size?: string;
          text?: string;
          shape?: string;
          width?: number;
        },
      ) => void;
      prompt: () => void;
      cancel: () => void;
      initialize: ({ client_id: string, callback }) => void;
      disableAutoSelect: () => void;
      revoke: (client_id: string, callback) => void;
    };
  };
}

export {};
```

It just says, "Hey, there's a global variable called `google` on the `window` object, and it has this shape." Now we can use `window.google` in our code and get type hints in our editor. It doesn't do anything for us at runtime, but it makes our lives much easier when writing the code.

### Using JS Libraries

When you're starting a new TypeScript project, you're all bright-eyed and bushy tailed, thinking to yourself, "Gee, I'm gonna have amazing type safety all throughout my project".

And then your project manager walks in and says, hey you're gonna need to `npm install pregnantgoku` and use that for this feature. Much to your dismay, `pregnantgoku` doesn't have any type definitions!

You have a couple of options:
- Allow the any types to flow through your code
- Create your own type definitions
- Check [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) and see if they have definitions for the library

DefinitelyTyped is a community-driven repository of type definitions for popular JavaScript libraries, and it's a great place to start.

That said, this is a course about learning how stuff works, so let's focus on creating your own type definitions for an existing JS library. It's not hard!

You just create a new file in your project. For example, `pregnantgoku.d.ts` and add the following:

```ts
declare module "pregnantgoku" {
  export function kamehameha(target: [number, number]): void;
  export type Saiyan = {
    name: string;
    monthsAlong: number;
    powerLevel: number;
  };
}
```

Now TypeScript will use this type information when you import `pregnantgoku` in your code.

For internal modules, you could just export the types. For example, if you have a `pregnantgoku.js` file, you could write a `pregnantgoku.d.ts` file like this:

```ts
export function kamehameha(target: [number, number]): void;
export type Saiyan = {
  name: string;
  monthsAlong: number;
  powerLevel: number;
};
```

### TypeScript Language Server

We've used `tsc` from the command line to compile our TypeScript code, but that's often not how it's used in practice... at least not directly. In many projects, TypeScript is primarily used as a [language server](https://code.visualstudio.com/docs/languages/typescript) to provide type checking and other features in your IDE. It powers stuff like:
- Auto-completion
- Type checking (showing and underlining errors in your editor)
- Jumping to definitions

That's why we sometimes joke about TypeScript being a "glorified linter". There are a few reasons I point out this distinction, but the first is to understand that your editor tooling and your build tooling are separate. If your editor is using TypeScript 4 and one `tsconfig.json` file, but your build tooling is using TypeScript 5 and another `tsconfig.json` file, you can run into scenarios where your editor and what's being compiled in production are out of sync.

All this to say... _keep them in sync_! Most editors are smart at this and do it automatically, but it's worth knowing how to check what your editor is using under the hood.
1. Most editors will default to using the version of TypeScript installed in your project. If you have a `node_modules` folder, it will use that version.
2. If you don't, or you don't have your editor opened to the right project directory, it will likely use:
     - The version of TypeScript installed globally on your machine
     - The version of TypeScript bundled in the editor itself
     - The version of TypeScript specified in your editor settings (e.g. `.vscode/settings.json`, `.zed/settings.json`, etc.)

_Pin the version of TypeScript in your project to avoid this confusion_, and make sure your editor is using _that_ version.

#### Restarting the TS Server

Language servers are notorious for kinda just... getting stuck. If you notice that your editor is not picking up on type errors, or is not providing auto-completion, step #1 should probably be to restart the TypeScript language server.
- In VS Code: `Cmd/Ctrl+Shift+P` > `TypeScript: Restart TS Server`
- In Zed: `Cmd/Ctrl+Shift+P` > `Restart Language Server`
- In Neovim: `:LspRestart`

### TypeScript Ignore

I try really hard to avoid using these, but sometimes they are the best choice among a host of bad choices.

`// @ts-ignore`: Ignores the next line's errors.

```ts
// @ts-ignore
const x: number = "not a number"; // Error suppressed
```

`// @ts-nocheck`: Disables type checking for the entire file.

```ts
// @ts-nocheck

const x: number = "not a number"; // No error

const sum(x: number, y: number): string {
  return x + y; // No error
}
```

These comments do what they say on the box: dangerously suppress type errors. Use them very sparingly, or ideally, not at all.
