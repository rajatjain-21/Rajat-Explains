---
title: "Typescript: Tips and Tricks for Aces"
seoTitle: "Mastering TypeScript: Advanced Tips and Tricks for Developers"
seoDescription: "Level up your TypeScript skills with advanced tips and tricks. Master type inference, union types, type guards, enums, and more. Boost your development!"
datePublished: Tue Jul 25 2023 19:43:09 GMT+0000 (Coordinated Universal Time)
cuid: clkipfbtl000a0aju29ovgdju
slug: typescript-tips-and-tricks-for-aces
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1690313381931/65fcc4db-ebfa-4134-8ca0-d4239fe9415b.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1690313433377/c51d3176-a182-406a-a058-37969e26000b.jpeg
tags: javascript, frontend, typescript, frontend-development

---

[Typescript](https://www.typescriptlang.org/) has rapidly gained popularity among developers due to its ability to bring **type safety** and **static analysis** to Javascript code. It helps **catch errors** during development and **improves code quality**, making it a powerful tool for building robust applications. As you dive into Typescript, there are several tips and tricks you can use to become an ace at leveraging its full potential.

In this blog post, let us explore some advanced Typescript tips and tricks with code examples to level up your development skills.

## **1\. Mastering Type Inference**

Typescript has a powerful type inference system that automatically deduces the types of variables when initialized. This feature can save you from explicitly defining types in many cases. Let's take a look at a simple example:

```typescript
// Without type inference
const name: string = "John Doe";
const age: number = 30;

// With type inference
const name = "John Doe";
const age = 30;
```

In the above example, Typescript can automatically infer the types of `name` and `age` based on their initial values.

## **2\. Utilize Union and Intersection Types**

Typescript supports **Union** and **Intersection** types, which allow you to combine multiple types into one. Union types are denoted using the pipe `|` symbol, while intersection types use the ampersand `&` symbol.

```typescript
// Union type example
function printId(id: string | number) {
  console.log(`ID: ${id}`);
}

// Intersection type example
interface Name {
  first: string;
  last: string;
}

interface Age {
  age: number;
}

type Person = Name & Age;

function printPerson(person: Person) {
  console.log(`Name: ${person.first} ${person.last}, Age: ${person.age}`);
}
```

## **3\. Leverage Type Guards**

Type guards help you narrow down the type of a variable within a conditional block. This is particularly useful when dealing with union types.

```typescript
// Type guard example
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(`ID: ${id.toUpperCase()}`);
  } else {
    console.log(`ID: ${id.toFixed(2)}`);
  }
}
```

## **4\. Use Type Aliases and Interfaces Wisely**

TypeScript allows you to create type aliases and interfaces to define custom types. Knowing when to use each can improve code readability and maintainability.

Use type aliases when you need to create a new name for a type, especially for complex types or union types:

```typescript
type Point = { x: number; y: number };
```

Use interfaces to define object shapes or when working with classes:

```typescript
interface IUser {
  id: number;
  name: string;
  email: string;
}
```

## **5\. Strict Mode is Your Friend**

Enabling TypeScript's strict mode (`strict: true` in `tsconfig.json`) may feel restrictive at first, but it greatly improves code quality and catches potential bugs early on. It enforces better coding practices and enables a higher level of type safety.

## **6\. Take Advantage of Enums**

Enums provide a way to define a set of named constants, which can make your code more readable and self-explanatory. Using enums can make it easier to document intent, or create a set of distinct cases.:

```typescript
enum Color {
  Red,
  Green,
  Blue,
}

const selectedColor = Color.Red;

if (selectedColor === Color.Red) {
  console.log("You chose Red!");
}
```

## **7\. Explore Mapped Types**

Mapped types allow you to create new types based on existing ones, with modifications applied:

```typescript
interface IUser {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<IUser>; // All properties become optional
type ReadonlyUser = Readonly<IUser>; // All properties become readonly
```

## **8\. Embrace Non-Null Assertion Operator**

The non-null assertion operator (`!`) tells Typescript that a variable is not null or undefined, even though the compiler might think it's possible. Be cautious when using this operator, and only do so when you are confident the value is not null/undefined. Similar to type assertions of the forms `<T>x` and `x as T` , the `!` non-null assertion operator is simply removed in the emitted Javascript code.

```typescript
function myFunc(maybeString: string | undefined | null) {
   const onlyString: string = maybeString; //compilation error: string | undefined | null is not assignable to string
   const ignoreUndefinedAndNull: string = maybeString!; //no problem
}
```

> [Cleaner TypeScript With the Non-Null Assertion Operator](https://betterprogramming.pub/cleaner-typescript-with-the-non-null-assertion-operator-300789388376)

## **Conclusion**

Typescript is a powerful language that empowers developers to write safer and more maintainable JavaScript code. By mastering the tips and tricks outlined in this blog post, you can become an ace at using TypeScript effectively in your projects. From type inference and union types to strict mode and non-null assertions, these features will enhance your development experience and boost productivity. Happy coding!

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.

And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [**@rajatexplains**](https://twitter.com/rajatexplains)

Instagram - [**@javascript\_to\_the\_rescue**](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [**Rajat Jain**](https://www.linkedin.com/in/rajatjain-21/)