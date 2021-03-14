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
function giveMeAFunctionToCall(functionToCall){
    functionToCall();
}
var myObject = {
    giveMeAClassicalFunction: function(){
        var myClassicalFunction = function() {
            console.log('classicalFunction: this === myObject?', this===myObject)
            console.log('classicalFunction: this === window?', this===window)
        }
        return myClassicalFunction;
    }
}

var myClassicalFunction = myObject.giveMeAClassicalFunction();
giveMeAFunctionToCall(myClassicalFunction);
```

```javascript
function giveMeAFunctionToCall(functionToCall){
    functionToCall();
}
var myObject = {
    giveMeAnArrowFunction: function(){
        var myArrowFunction = () => {
            console.log('arrowFunction: this === myObject?', this===myObject)
            console.log('arrowFunction: this === window?', this===window)
        };
        return myArrowFunction2;
    },
}

var myArrowFunction = myObject.giveMeAnArrowFunction();
giveMeAFunctionToCall(myArrowFunction);
```

### How do we work out what `this` is bound to?

![What is `this` bound to?](./assets/this.png);

### `this` resources

- [Global object - MDN](https://developer.mozilla.org/en-US/docs/Glossary/Global_object)
- [`this` operator - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)
- [`new` operator - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)
- [Understanding the `this` keyword, call, apply, and bind in JavaScript - ui.dev](https://ui.dev/this-keyword-call-apply-bind-javascript/)

## Understanding scope

To understand scope, we need to understand how the Javascript interpreter executes our code. 

Our code is broken up into logical blocks called `Execution Contexts`. 

An `Execution Context` is created in one of two scenarios:

1. Javascript Intpreter begins executing a program: `Global Execution Context`
2. Whenever a function in invoked `Function Execution Context`

An `Execution Context` has two phases:

1. Creation
2. Execution

The first `Execution Context` created is called the `Global Execution Context`.

### Global Execution Context

What happens during the Global _Creation_ phase? 

1. Create a global object
2. Create a `this` object
3. Allocate memory for variables and functions
4. Assign variable declarations a default value of `undefined`
5. Load functions into memory

What happens during the Global _Exection_ phase? 

Code is loaded line by line and executed. This is when values are assigned to the variables declared during the _creation_ phase.

The process of assigning variable declarations a default value of `undefined` during the _creation_ phase is referred to as **Hoisting**.

#### [Empty program](https://ui.dev/javascript-visualizer?code=%2F%2FThis%20is%20an%20empty%20file%21)

```javascript
// This is an empty file
```

#### [A variable and a function](https://ui.dev/javascript-visualizer?code=var%20greeting%20%3D%20%22hi%22%3B%0Afunction%20iDontDoMuch%28%29%7B%0A%09%0A%7D)


```javascript
var  greeting = "hi";
function iDontDoMuch() {

}
```

#### [Hoisting](https://ui.dev/javascript-visualizer?code=alert%28greeting%29%3B%0Avar%20greeting%20%3D%20"hi"%3B%0Aalert%28greeting%29%3B)

```javascript
alert(greeting);
var  greeting = "hi";
alert(greeting);
```

### Function Execution Context

What happens during the Function _Creation_ phase? 

1. ~~Create a global object~~ Creates an `arguments` array
2. Create a `this` object
3. Allocate memory for variables and functions
4. Assign variable declarations a default value of `undefined`
5. Load functions into memory

What happens during the Function _Exection_ phase? 

Code is loaded line by line and executed. This is when values are assigned to the variables declared during the _creation_ phase.

#### [Empty function with 0 arguments](https://ui.dev/javascript-visualizer?code=function%20fooBar%28%29%7B%0A%20%0A%7D%0AfooBar%28%29%3B)

```javascript
function fooBar() {
    
}
fooBar();
```

#### [Empty function with several arguments](https://ui.dev/javascript-visualizer?code=function%20fooTwo%28fruit1%2Cfruit2%29%20%7B%0A%20%20%20%20alert%28fruit1%29%3B%0A%20%20%20%20alert%28fruit2%29%3B%0A%7D%0AfooTwo%28"apple"%2C"orange"%29%3B)

```javascript
function fooTwo(fruit1,fruit2) {
    alert(fruit1);
    alert(fruit2);
}
fooTwo("apple","orange");
```

#### [Execution Stack](https://ui.dev/javascript-visualizer?code=function%20one%28%29%20%7B%0A%20%20%20%20function%20two%28%29%20%7B%0A%20%20%20%20%20%20%20%20function%20three%28%29%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20%20%20three%28%29%3B%0A%20%20%20%20%7D%0A%20%20%20%20two%28%29%3B%0A%7D%0Aone%28%29%3B)

```javascript
function one() {
    function two() {
        function three() {
            
        }
        three();
    }
    two();
}
one();
```

#### [Scope Chain - Local Scope](https://ui.dev/javascript-visualizer?code=function%20step1%28%29%7B%0A%20%20var%20lyrics%20%3D%20"We%20can%20have%20so%20much%20fun..."%3B%0A%20%20console.log%28lyrics%29%3B%0A%7D%0A%0Afunction%20step2%28%29%7B%0A%20%20var%20lyrics%20%3D%20"There%27s%20so%20much%20we%20can%20do."%3B%0A%20%20console.log%28lyrics%29%3B%0A%7D%0A%0Afunction%20step3%28%29%7B%0A%20%20var%20lyrics%20%3D%20"It%27s%20just%20you%20and%20meeeeeeee%21"%3B%0A%20%20console.log%28lyrics%29%3B%0A%7D%0A%0Aconsole.log%28lyrics%29%3B%0Avar%20lyrics%20%3D%20"I%20can%20give%20you%20more"%3B%0Astep1%28%29%3B%0Astep2%28%29%3B%0Astep3%28%29%3B%0Aconsole.log%28lyrics%29%3B)

```javascript
function step1() {
    var lyrics = "We can have so much fun...";
    console.log(lyrics);
}
function step2() {
    var lyrics = "There's so much we can do";
    console.log(lyrics);
}
function step3() {
    var lyrics = "It's just you and meeeeeee!";
    console.log(lyrics);
}
console.log(lyrics);
var lyrics = "I can give you more";
step1();
step2();
step3();
console.log(lyrics);
```


#### [Scope Chain - Outer Scope](https://ui.dev/javascript-visualizer?code=var%20theFairestOfFair%20%3D%20"Meghan%20Markle"%3B%0A%0Afunction%20whoIsTheFairestOfThemAll%28%29%7B%0A%20%20alert%28theFairestOfFair%29%3B%0A%7D%0A%0AwhoIsTheFairestOfThemAll%28%29%3B)

```javascript
var theFairestOfFair = "Meghan Markle"
function whoIsTheFairestOfThemAll() {
    alert(theFairestOfFair);
}
whoIsTheFairestOfThemAll();
```

#### [Scope Chain - Local and Outer Scope](https://ui.dev/javascript-visualizer?code=var%20a%20%3D%20"a"%3B%0Afunction%20one%28%29%20%7B%0A%20%20%20%20var%20b%20%3D%20"b"%3B%0A%20%20%20%20console.log%28"a%20in%20one%28%29%27s%20function%20execution%20context"%29%3B%0A%20%20%20%20console.log%28a%29%3B%0A%20%20%20%20console.log%28"b%20in%20one%28%29%27s%20function%20execution%20context"%29%3B%0A%20%20%20%20console.log%28b%29%3B%0A%7D%0Aone%28%29%3B%0A%0Aconsole.log%28"a%20in%20global%20execution%20context"%29%3B%0Aconsole.log%28a%29%3B%0Aconsole.log%28"b%20in%20global%20execution%20context"%29%3B%0Aconsole.log%28b%29%3B)

```javascript
var a = "a";
function one() {
    var b = "b";
    console.log("a in one()'s function execution context");
    console.log(a);
    console.log("b in one()'s function execution context");
    console.log(b);
}
one();

console.log("a in global execution context");
console.log(a);
console.log("b in global execution context");
console.log(b);
```

#### [Scope Chain - Closure Scope](https://ui.dev/javascript-visualizer?code=function%20meghanMarkle%28%29%7B%0A%09var%20meghansSecret%20%3D%20"I%20see%20dead%20people."%3B%0A%20%20%09return%20function%20princeHarry%28%29%7B%0A%20%20%20%20%09console.log%28"Meghan%20told%20me%20a%20secret.%20She%20said%3A%20"%29%3B%0A%20%20%20%20%20%20%09console.log%28meghansSecret%29%3B%0A%20%20%20%20%7D%3B%0A%7D%0A%0Avar%20princeHarryClosure%20%3D%20meghanMarkle%28%29%3B%0AprinceHarryClosure%28%29%3B)

```javascript
function meghanMarkle() {
    var meghansSecret = "I see dead people.";
    return function princeHarry() {
        console.log("Meghan told me a secret. She said: ") ;
        console.log(meghansSecret); 
    }
}
var princeHarryClosure = meghanMarkle();
princeHarryClosure();
```

#### Closure scope & `this` 

##### Classical Functions

```javascript
var meghansSecret = "I eat Archie's baby food.";
console.log(meghansSecret);
function meghanMarkle(){
	var meghansSecret = "I see dead people.";
  	return function princeHarry(){
    	console.log("Meghan told me a secret. She said: ");
      	console.log(this.meghansSecret);
    };
}

var princeHarryClosure = meghanMarkle();
princeHarryClosure();
```

##### Arrow functions `()=>{}`

```javascript
var meghansSecret = "I eat Archie's baby food.";
console.log(meghansSecret);
function meghanMarkle(){
	var meghansSecret = "I see dead people.";
  	return ()=>{
    	console.log("Meghan told me a secret. She said: ");
      	console.log(this.meghansSecret);
    };
}

var princeHarryClosure = meghanMarkle.call({meghansSecret});
princeHarryClosure();
```

### Scope resources

- [JavaScript Visualiser - ui.dev](https://ui.dev/javascript-visualizer/)
- [Ultimate Guide to Execution Contexts, Hoisting, Scopes and Closures in JavaScript - ui-dev](https://ui.dev/ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript/)