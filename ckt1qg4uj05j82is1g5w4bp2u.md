# Next-Gen JavaScript - Level Up

In this article, I'll provide a brief introduction to some core next-gen JavaScript concepts, of course focusing on the ones you'll use the most in your coding joruney. And I'll also include handy code snippets for better understanding just like forever!

---

## Contents
- [let & const](#let-and-const)
- [ES6 Arrow Functions](#es6-arrow-functions)  
- [Exports & Imports](#exports-and-imports)
- [Classes](#classes)
- [Spread & Rest Operator](#spread-and-rest-operator)
- [Destructuring](#destructuring)

---

## let & const
`let` and `const` basically replace `var`. `let` is very similar to `var` with the difference that the `let` variable has a block-level scope (i.e. the enclosing `{ }` just like variables in other languages). On the other hand, the scope of a `var` variable is the entire enclosing function.

Let's have a look at an example:

```
function varDemo() {
  var x = 1;
  {
    var x = 2;  // same variable!
    console.log(x);  // 2
  }
  console.log(x);  // 2
}

function letDemo() {
  let x = 1;
  {
    let x = 2;  // different variable
    console.log(x);  // 2
  }
  console.log(x);  // 1
}

```

Use `const` instead of `var` if you plan on never re-assigning this "variable". It's a good practice to assign arrays and objects as a `const`.

```
const number = 2;

try {
  number = 99;
} catch (err) {
  console.log(err);
  // expected output: TypeError: invalid assignment to const `number'
}

console.log(number);
// expected output: 2
```

Read more about `let` [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let).  
Read more about `const` [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const).

---

## ES6 Arrow Functions
Arrow functions are an alternate way of creating functions in JavaScript. Besides a shorter syntax, they offer advantages when it comes to keeping the scope of the `this` keyword.

``` JavaScript
// Traditional Function
function callMe(a) {
  console.log(a);
}

// Arrow Function Break Down

// 1. The above function can be written as:
const callMe = function(a) {
  console.log(a);
}

// 2. Remove the word "function" and place arrow between the argument 
// and opening body bracket
const callMe = (a) => {
  console.log(a);
}
```

### Important:
- When having no arguments, you have to use empty parentheses in the function declaration:
``` javascript
const callMe = () => { 
    console.log('Something!');
}
```

- When having exactly one argument, you may omit the parentheses:
``` 
const callMe = name => { 
    console.log(name);
}
```

- When just returning a value, you can use the following shortcut:  
```
const returnMe = name => name;
// That's equal to:
const returnMe = name => { 
    return name;
}
```

Read more about ES6 Arrow functions [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions).

---

## Exports & Imports
In all modern JavaScript projects, you split your code across multiple JavaScript files - so-called modules. You do this to manage your large code base and make it understandable.

To access functionality in another file, you need `export` (to make it available) and `import` (to get access) statements.

### export
You got two different types of exports: **default** (unnamed) and **named** exports:

- **default** export
``` javascript
const user = {
  id: 1,
  name: 'SomeName'
}
// Export user
export default user
```

- **named** export
``` javascript
// export individual features (can export var, let,
// const, function, class)
export const initialize = () => { ... }
export const BASE_URL = 'https://xyz... '
// Or export features declared earlier like this:
export { initialize, BASE_URL };
```

#### Important
A file can only contain one default and an unlimited amount of named exports. You can also mix the one default with any amount of named exports in one and the same file.

### import
- You can import **default exports** like this:
```
import someNameOfYourChoice from './path/to/file.js';
```
Surprisingly, `someNameOfYourChoice` is totally up to you.

- **Named exports** have to be imported by their name:
```
import { someData } from './path/to/file.js';
```
When importing **named exports**, you can also import all named exports at once with the following syntax:
```
import * as upToYou from './path/to/file.js';
```
`upToYou` is, well, up to you and simply bundles all exported variables/functions in one JavaScript object.  
For example, if you `export const someData = ...`, you can access it on `upToYou` like this: `upToYou.someData`

Read more about `export` [here](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export).  
Read more about `import` [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import).

---

## Classes
Classes are a feature that basically replaces constructor functions and prototypes. You can define blueprints for JavaScript objects with them just like other OOP languages.

Like this:
``` javascript
class User {
    constructor () {
        this.name = 'SomeName';
    }
}
 
const user = new User();
console.log(user.name); // prints 'SomeName'
```

The syntax you see above is the "old" syntax for defining properties. In modern JavaScript projects, you can use the following, more convenient way of defining class properties:
``` javascript
class User {
    name = 'SomeName';
}
 
const user = new User();
console.log(user.name); // prints 'SomeName'
```

You can also define methods. Either like this:
``` javascript
class User {
    name = 'SomeName';
    printName () {
        console.log(this.name); // this is required to refer to the class!
    }
}
 
const user = new User();
user.printName(); // prints 'SomeName'
```

Or like this:
``` javascript
class User {
    name = 'SomeName';
    printName = () => {
        console.log(this.name);
    }
}
 
const user = new User();
user.printName(); // prints SomeName
```

The second approach has the same advantage as all arrow functions have: The `this` keyword doesn't change its reference.

You can also use inheritance when using classes:
``` javascript
class Human {
    gender = 'male';
}
 
class User extends Human {
    name = 'SomeName';
    printName = () => {
        console.log(this.name);
    }
}
 
const user = new User();
user.printName(); // prints 'SomeName'
console.log(user.gender); // prints 'male'
```

Read more about `class` [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes).

---

## Spread & Rest Operator
The **spread** and **rest** operators actually use the same syntax: `...`
Yes, it's just three dots! Its usage determines whether you're using it as the spread or rest operator.

### Spread Operator
The spread operator allows you to pull elements out of an array (split the array into a list of its elements) or pull the properties out of an object. 

Here are two examples:
``` javascript
// spread operator used on array
const oldArray = [1, 2, 3];
const newArray = [...oldArray, 4, 5]; // This now is [1, 2, 3, 4, 5];

// spread operator used on an object
const oldObject = {
    name: 'SomeName'
};
const newObject = {
    ...oldObject,
    age: 23
};

// newObject  would then be
{
    name: 'SomeName',
    age: 23
}
```

#### Note
The spread operator is extremely useful for cloning arrays and objects. Since both are reference types (and not primitives), copying them safely can be tricky. With the spread operator, you have an easy way of creating a clone of the object or array. 

### Rest Operator
The rest operator allows a function to accept an indefinite number of arguments as an array.

Here's how you use it:
```
const filter = (...args) => {
  return args.filter(el => el === 1)
}

console.log(filter(1, 2, 3)) // prints [1]
```

Read more about spread operator [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax).  
Read more about rest operator [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters).

---

## Destructuring
Destructuring allows you to easily access the elements of arrays or objects and assign them to variables.

Here's an example for destructuring an array:
``` javascript
const array = [1, 2, 3];
const [a, b] = array;
console.log(a); // prints 1
console.log(b); // prints 2
console.log(array); // prints [1, 2, 3]
```

And here for destructuring an object:
``` javascript
const user = {
    name: 'SomeName',
    age: 23
}
const {name} = user;
console.log(name); // prints 'SomeName'
console.log(age); // prints undefined
console.log(user); // prints {name: 'SomeName', age: 23}
```

Destructuring is very useful when working with function arguments. Consider this example:
``` javascript
const printName = (user) => {
    console.log(user.name);
}
printName({name: 'SomeName', age: 23}); // prints 'SomeName'
```

The above code forces us to call `user.name` inside of our function. We can condense this code with destructuring:
``` javascript
const printName = ({name}) => {
    console.log(name);
}
printName({name: 'SomeName', age: 23}); // prints 'SomeName'
```

We get the same result as above but we save some code. By destructuring, we simply pull out the `name` property and store it in a variable/argument named `name` which we can then use in the function body.

Read more about Destructuring [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

---

That's all folks! Do let me know what you liked and what can be improved in the comments below! Also, feel free to drop me a line if you don't understand something. ✌
 
Thanks for reading!

