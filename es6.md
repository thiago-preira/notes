# ES6 Changes

## Setup

## Syntax Ghanges

### Strict Mode
Using strict mode is the default in ES6 Modules. “use strict” is not required.

### let & const
Ways to declare variables in ES6:
* *const* - a variable that should not be re-assigned.
* *let* - use it whenever you need to re-assign variables.
* *var* - should be not used. Prefer use const or let instead.

### Function / Arrow Functions
Fat Arrow Functions allow you to use a shorter syntax to create functions:

```javascript
const fn = (arg1, arg2) => return arg1 + arg2
```
Inside a fat arrow function, the **this** keyword will always
refer to the context of the function instead of the caller of the function

**Default parameters** allow you specify default values for parameters passed
to functions.
```javascript
function fn(arg1 = 'default string', arg2 = 23) { return arg1 + arg2 }
```

### Literal Notation Extensions
Changes to literal notation:

```javascript
let width = 10;
let length = 50;
let lengthField = 'length';
rectangle = {
    width, // width is automatically assigned => takes above value
    [lengthField]: length, // Dynamic field name using variable
    "outputSize"() { // shorter syntax + possibility of string name
    console.log(this.width * this.length); // this refers to object
    }
};
```
### Rest & Spread Operators (...)

**Rest** : Transforms a list of arguments (1, 2, 3) into an array ([1, 2, 3]) which may be
used inside the function. This behavior is triggered when used inside of a function argument list.

```javascript
function sumUp(start, ...toAdd) {}
```

**Spread** : Transforms an array into a list of arguments. This behavior is triggered
when used outside of a function argument list.

```javascript
let ids = [1, 2, 3, 4, 5, 6];
console.log(Math.max(...ids)); // prints 6
```

### For-of Loop
Allows you to loop through an array of items
```javascript
let testResults = [5.30, 2.84, 9.11, 1.01, 3.99];
for (let testResult of testResults) {
    if (testResult > 5) {
        console.log(testResult); // prints 5.30, 9.11
    }
}
```

### Template Literals
Template literals allow you create complex texts.
```javascript
let name = 'Max';
description = `
    This is a description, which may wrap across multiple lines.
    Written by ${name}.
`;
```

### Destructuring
Destructuring allows to extract values from an object or an array

**Array** - 
```javascript
let numbers = [1, 2, 3, 4, 5];
let [a, b] = numbers; // a => 1, b => 2
```

**Object** - 
```javascript
let person = {
    name: 'Max',
    age: 27
};
let {name, age} = person; // Notice the {} instead of []
```