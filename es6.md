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

## Modules and Classes

### Modules
Allows multiple split code in muiltiple files:

```javascript
// export.js
export let myExportedVar = 42;
// import.js
import { myExportedVar } from ‘./export.js’;
```

### Classes
Create classes using **class** keyword:

```javascript
class Person {
    greet() {
        this.name = ‘Max’; // this is how you set up properties!
        console.log('Hello!');
    }
}
let person = new Person();
person.greet(); // prints ‘Hello!’
```
Inheritance with ES6 classes:
```javascript
class Max extends Person {
    constructor(name) {
        this.name = name;
    }
}
```
Static methods:
```javascript
class Helper {
    static log(message) {
        console.log(message);
    }
}
```
## Symbols
Symbols are a new primitive type in ES6. Basically, a Symbol is a unique ID.
```javascript
let symbol = Symbol(‘only for debugging, this is not the ID!’);
```

Each symbol is unique

```javascript
let symbol1 = Symbol(‘only for debugging, this is not the ID!’);
let symbol2 = Symbol(‘only for debugging, this is not the ID!’);
if (symbol1 == symbol2) { ... } // false
```
When using static method *for* they are registered in a global symbol registry, so they become not unique.
```javascript
let ageSymbol1 = Symbol.for('age');
let ageSymbol2 = Symbol.for('age');
if (ageSymbol1 == ageSymbol2) { ... } // true
```

## Iterators & Generators

### Iterators
Iterator has a function `next()` which allows to output values step by step.
```javascript
let array = [1, 2, 3];
let iterator = array[Symbol.iterator]();
console.log(iterator.next()); // prints {done: false, value: 1}
```
Done becames true when there are no more values. 

```javascript
let iterableObj = {
    [Symbol.iterator]() {
        let nextVal = 5;
        return {
            next() {
                return {
                    value: nextVal++,
                    done: false
                }
            }
        }
    }
};
let iterator = iterableObj[Symbol.iterator]();
console.log(iterator.next()); // {done: false, value: 5}
```

### Generators
Functions which don’t necessarily run to the end upon
execution.
Instead, upon each call they yield a value. A generator is created by adding
an asterisk in front of the function name.

```javascript
function *select() {
    yield 'House Data';
    yield 'Person Data';
}
```
When executing a function an iterator is returned. 
```javascript
let iterator = select();
console.log(iterator.next()); // prints House Data
console.log(iterator.next()); // prints Person Data
console.log(iterator.next()); // prints undefined
```
## Promisses
Promises to return a certain value – even if the
underlying task fails. In such a failure case, the promise would be rejected
but the caller would still be informed.

```javascript
let promise = new Promise(function(resolve, reject) {
    setTimeout(function() { // setTimeout to simulate async task
        resolve('Done!');
    }, 1000);
});

promise.then(function(value) {
    console.log(value); // prints Done!
});

let fnWaitASecond = function(secondsPassed) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            secondsPassed++;
            resolve(secondsPassed);
        }, 1000);
    });
};
fnWaitASecond(0)
.then(fnWaitASecond) // Passes value automatically
.then(function(seconds) {
    console.log('Promises: waited ' + seconds + ' seconds');
});
```

`race()` returns first Promise to resolve. You’ll get a value as soon as the first Promise is resolved.
With `all()` returns value once all Promises were resolved.

## Maps, Sets & Subclassing

### Map
Map is a key-value collection
```javascript
let cardAce = {
name: 'Ace of Spades'
};
let cardKing = {
name: 'King of Clubs'
};
let deck = new Map();
deck.set('as', cardAce);
deck.set('kc', cardKing);
```
Use `get(key)` method to return a value. Use `delete(key)` to remove.

### Set
A Set is a collection that only hold unique values. 
You can loop through a set to retrieve the values (or use an Iterator). You
can also `clear()` a set or delete individual values by using `delete()`.
 ```javascript
 ou can create a Set like this:
let cardAce = {
name: 'Ace of Spades'
};
let cardKing = {
name: 'King of Clubs'
};
let deck = new Set();
deck.add(cardAce);
deck.add(cardKing);
deck.add(cardKing); // Won’t be added, only added once!
 ```

### Subclassing
Subclassing means that you can now extend certain JavaScript
base-objects. 
```javascript
class ConvertableArray extends Array {
convert() {
let returnArray = [];
this.forEach(value => returnArray.push('' + value));
return returnArray;
}
}
```

