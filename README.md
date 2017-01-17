# *Node.js Notes*
My Node.js notes.

## Some Definitions
* **Node.js** <br/>
Node.js is designed to use JavaScript to write server code.
* **Break Point** <br/>
A spot in our code where we tell debugging tool to pause the execution of our code.
* **Module** <br/>
A reuseable block of code whose existence does not accidentally impact other code.
* **Commonjs Modules** <br/>
A agreed upon standard for how code modules should be structured.

## Module patterns
- Example 1
```javascript
// greet.js
module.exports = function() {
  console.log('Hello World');
};
```
```javascript
// app.js
var greet = require('./greet');
greet();
```
- Example 2
```javascript
// greet.js
module.exports.greet = function() {
  console.log('Hello World');
};
```
```javascript
// app.js
var greet = require('./greet').greet;
greet();
```
- Example 3
```javascript
// greet.js
function Greetr() {
  this.greeting = 'Hello World';
  this.greet = function() {
    console.log(this.greeting);
  }
}

module.exports = new Greetr();
```
```javascript
// app.js
var greet = require('./greet');
greet.greet(); // Hello World
greet.greeting = 'Changed Hello World';

var greet1 = require('./greet');
greet1.greet(); // Changed Hello World
```
In this example, *greet* and *greet1* reference to the same object. *require* function caches and stores the result of the require function (object) of any particalur file name. Therefore, the changes made in *greet* are reflected as well in *greet1*.
- Example 4
```javascript
// greet.js
function Greetr() {
  this.greeting = 'Hello World';
  this.greet = function() {
    console.log(this.greeting);
  }
}

module.exports = Greetr;
```
```javascript
// app.js
var Greet = require('./greet');
var grtr = new Greet();
grtr.greet();
```
- Example 5
```javascript
// greet.js
var greeting = 'Hello World';
function greet() {
  console.log(greeting);
}
module.exports = {
  greet: greet
};
```
```javascript
// app.js
var greet = require('./greet');
greet.greet();
```
This example is a very common and clean way to struture and protect code within modules, it is called revealing module pattern: exposing only the properties and methods you want via an returned object.

## *exports* vs *module.exports*
When a code is run through node, is wrapped in a function expression:
```javascript
(function (exports, require, module, __filename, __dirname) {
  var greet = function() {
    console.log('Hello');
  };
  module.exports = greet;
});
```
When the function is executed:
```javascript
fn(module.exports, require, module, filename, dirname);
```
Actually, *exports* is shorthand to module.exports, it's two variable pointing to the same object in memory. But why not just use *exports* all the time? Well, the *require* function returns *module.exports*, so it's returing the propery on *module* object. However, if we replace *module.exports* with *exports*:
```javascript
exports = function() {
  console.log('Hello');
}
console.log(exports);        // [Function]
console.log(module.exports); // {}
```
Initially, *module.exports* is pointing to the an empty object that is setup for us, and when the function expression is invoked, *module.exports* is passed the variable *exports*. They still both point to the same memory location. That is pass by reference. However, equlas (=) operator sets up new address, so when assign *exports* to some value, it points to a new memory address, the reference is broken. 

Use of mutation can make this work:
```javascript
exports.greet = function() {
  console.log('Hello');
}
console.log(exports);        // { greet: [Function] }
console.log(module.exports); // { greet: [Function] }
```
