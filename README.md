# Introduction to `this` and scope in JS

## Understanding the `this` keyword

### Global context

- Global object is always defined
- Every Javascript `Program` requires an environment to execute in. Execution context
- It's value depends on the execution context
- What is the execution context in Chrome or Firefox? Browser!
- In a browser the global object is called `window`, in Node.js it is called `global`
  
```javascript
// Run this code in your browser's console
console.log(window);
```

In the global execution context, declaring a `var` is the same as assigning it to the global object i.e. `window`

```javascript
// Run this code in your browser's console
var foo = "foobar";
console.log(foo === window.foo); // Returns: true
```

The same can be said for `function` declarations

```javascript
// Run this code in your browser's console
function fooFoo(){
    console.log("hello");
}
console.log(fooFoo === window.fooFoo); // Returns: true
```

So what does the `this` keyword refer to in the global execution context?

```javascript
//In the global execution context 'this' refers to the window global object
console.log(this === window); // Returns: true
```

You can always easily get the global object using the global `globalThis` property, regardless of the current context in which your code is running.

```javascript
//In the global execution context 'globalThis' refers to the window global object
console.log(globalThis === window); // Returns: true
console.log(this === window); // Returns: true
console.log(this === globalThis); // Returns: true
```

### Function context

When a `function` is invoked, it creates a new _function context_. The value of `this` will depend on how the `function` is invoked.

#### Window binding of `this` 

```javascript
//In a global context
function globalContextFunction(){
    return this;
}
console.log(globalContextFunction()=== window); // Returns: true
console.log(globalContextFunction()=== globalThis); // Returns: true
console.log(globalContextFunction()=== this); // Returns: true
```

#### Implicit binding of `this`

Invoke function in the following way `someObject.myFancyFunction()`. To work out what `this` is going to be bound to in the `function` body, we look to the left of the `.` operator.

```javascript
const totesMyRealDeetz = {
  name: 'Robyn',
  age: 21,
  greet: function() {
    console.log(`Hello, my name is ${this.name} and my age is defo ${this.age}`) // `this` is bound to the `user` object
  }
}
totesMyRealDeetz.greet(); 
```

#### Explicit binding of `this`

##### Using `call` and `apply`

```javascript
function greetMeAncientOne() {
    console.log(`Hello my name is ${this.name} and I am ${this.age} years old`);
}
const userObject = {
    name: "Rami",
    age: 9001
}
// 'this' is bound to the userObject in both examples
greetMeAncientOne.call(userObject); // Returns: Hello my name is Rami and I am 9001 years old. 
greetMeAncientOne.apply(userObject); // Returns: Hello my name is Rami and I am 9001 years old
```

##### When to use `apply` over `call`

```javascript
const userObject = {
    name: "Rami",
    age: 9001
}

const languages = ["English", "Arabic", "French", "Hindi", "Rust", "Robyn"];

function showOffMahSkillz(languages) {
    console.log(`I am ${this.name}. I can speak the following languages: `);
    for(var i=0; i<languages.length;i++){
        console.log(`* ${language[i]}`);
    }
}
// Using `call` can get tedious when you want to pass more than a few parameters to your function
showOffMahSkillz.call(userObject, language[0], language[1], language[2], language[3], language[4], language[5])
// Using `apply`, you can pass all your parameters in a single array
showOffMahSkills.apply(userObject, languages)

```

##### Using `bind` to create a _bound_ `function`

```javascript
const user1 = {
    name: "Rami",
    age: 9001
}
const user2 = {
    name: "Robyn",
    age: 19
}


function greetMe() {
    console.log(`Hello my name is ${this.name} and I am ${this.age} years old`);
}

const boundGreetMeAncientOne = greetMe.bind(user1); // Returns a function with 'this' explicitly bound to the 'user1' object
const boundGreetMeAncientTwo = greetMe.bind(user2); // Returns a function with 'this' explicitly bound to the 'user2' object
boundGreetMeAncientOne(); // Returns: Hello my name is Rami and I am 9001 years old
boundGreetMeAncientTwo(); // Returns: Hello my name is Robyn and I am 19 years old
```

#### `new` function binding of `this`

When we invoke a `function` using the `new` operator, then the function's `this` is bound to a new, empty object `{}`

```javascript
var favouriteThings = ["Sadness","Goths"];
 
function listMyFavouriteThings(myFaves) {
    this.favouriteThings = myFaves;
    console.log(this.favouriteThings);
}

// Invoking `listMyFavouriteThings` function using the `new` operator
// `this` === `{}` (new, empty object)
new listMyFavouriteThings( ["JavaScript", "Linux", "Pastries"]); //Logs: ["JavaScript", "Linux", "Pastries"]
console.log(this.favouriteThings) // Logs: ["Sadness","Goths"];

// Invoking `listMyFavouriteThings` function without the `new` operator 
// `this` === `globalThis`
listMyFavouriteThings(["JavaScript", "Linux", "Pastries"]); //Logs: ["JavaScript", "Linux", "Pastries"]
console.log(this.favouriteThings) //Logs: ["JavaScript", "Linux", "Pastries"]
```

#### Lexical binding of `this`

```javascript
function FooBar () {
    this.fooBarMethod = function(callback) {
        callback();
    }
}
FooBar.prototype.fooBarPrototypeMethod = function(callback) {
    callback();
}

var myObject = {
    fooBar: new FooBar(),
    myObjectMethod: function() {
        function callback() {
            console.log('this', this)
        }
        // Expected: callback function's this to be bound to myObject.fooBar
        // Actual: callback function's this was bound to Window
        this.fooBar.fooBarMethod(callback); 
        this.fooBar.fooBarPrototypeMethod(callback); 

        // Expected: callback function's this to be bound to {newThis: "newThis"}
        // Actual: callback function's this was bound to Window
        this.fooBar.fooBarPrototypeMethod.call({newThis: "newThis"}, callback);
        this.fooBar.fooBarMethod.call({newThis: "newThis"}, callback);
        
    }
}

myObject.myObjectMethod();
```

### `this` resources

[Global object- MDN](https://developer.mozilla.org/en-US/docs/Glossary/Global_object)
[`this` keyword - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
[`new` operator - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)
[`this` keyword - ui.dev](https://ui.dev/this-keyword-call-apply-bind-javascript/)

## Understanding scope

### Scope resources

[ui-dev](https://ui.dev/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)
