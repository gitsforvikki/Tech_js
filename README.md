# Tech_js
This contain fundamental of javascript.


# content

1. Primitive Types
2. Expression vs Statement
3. DOM and Layout Trees
4. Map, Reduce, Filter
5. High Order Functions
6. Recursion
7. Promises
8. Async/Await
9. Data Structures
10. Algorithms
11. Design Patterns
12. Clean Code

# promise in javaScript
A Promise is a special JavaScript object. It produces a value after an asynchronous operation completes successfully, or an error if it does not complete successfully due to time out, network error, and so on.

Successful call completions are indicated by the resolve function call, and errors are indicated by the reject function call.

You can create a promise using the promise constructor something like that

```javascript
let promise = new Promise(function(resolve, reject) {    
    // Make an asynchronous call and either resolve or reject
});
```

> Since javascript is a single threaded, synchronous programming language but callback functions help to make it an asynchronous programming language.

promise may in any phase given below : -
- pending : Initially when the executor function starts the execution.
- fullfilled : When promise is resolved.
- reject : when promise is rejected.


> The way promise are resolved or rejcted

- In the given example,Promise will be resolved with the `value` of I am done here immedialtely.

```javascript
let promise = new  Promise((resolve , reject)=>{
  resolve("I am done here");
});
```

- In the given example,Promise will be rejected with the `error messaage` of something went wrong immedialtely.

```javascript
let promise = new  Promise((resolve , reject)=>{
  reject("something went wrong");
});
```

> A Promise executor should call only one resolve or one reject. Once one state is changed (pending => fulfilled or pending => rejected), that's all. Any further calls to resolve or reject will be ignored.

for example : 

 ```javascript
 let promise =  new Promese((resolve , reject)=>{
  resolve("I have Done!");

  reject(" Not complete yet!"); //this will be ignored.
 });
 ```

## How to handle the Promise

 >  A promise uses a executer function to complete the task asynchroniousley. After the successsfully complete the next task is to handle the promise, for that we have some handler functions like `resolve(), reject() , finally()`.

- The handler functions help to create the link between the executer function and the consumer function so that they can be sync when promise  get resolve or reject.


```javascript
// executer function

let primise = new  Promise((resolve , reject)=>{
  resolve('I am resolved');
});

```

```javascript
// consumer function
let consumer=()=>{
  promise.then((res)=>{
    console.log(res);
  });
};
```


## How to Use the .then() and catch() Promise Handler

> `.then()` handler should called to the promise object to handle a result(resolve) or error(reject)
, However, you can handle errors in a better way using the `.catch()` method .

- Here, this is the example to demonstrate the use of `.then()` and `.catch()`.

```javascript
// executer function

function getPromise(URL) {
  let promise = new Promise( (resolve, reject) => {
    let req = new XMLHttpRequest();
    req.open("GET", URL);
    req.onload = function () {
      if (req.status == 200) {
        resolve(req.response);
      } else {
        reject("There is an Error!");
      }
    };
    req.send();
  });
  return promise;
}
```

```javascript
const ALL_POKEMONS_URL = 'https://pokeapi.co/api/v2/pokemon?limit=50';

// We have discussed this function already!
let promise = getPromise(ALL_POKEMONS_URL);

const consumer = () => {
    promise.then(result => console.log(result))
            .catch(err => console.log(err))
};

consumer();

```


# How to handle dependent promises

```javascript
    //dependent promises
    //promise produce or execute

  let promiseObj = new Promise((resolve , reject)=>{
    setTimeout(()=>{
      let roll_no =[1,2,3,4,5];  //supose this roll number coming from the server.
      resolve(roll_no);
    },2000);
  });
```
> now the bellow promise is depends on the first promise.
its take a student roll number as a parameter.

```javascript
const getStudentData = (stu_roll)=>{
    return new Promise((resolve , reject)=>{
      setTimeout((stu_roll)=>{
          let stuBiodata  = {
            name : "vikash",
            agr :24
          } //suppose this stuBiodata comming from the server
          
          
          resolve(` student roll number ${stu_roll} and student name ${stuBiodata.name} of age ${stuBiodata.age} yrs.`)
      },2000 , stu_roll);
    });
  };
  
  ```
  
  > NOw handle the promise without `async/await`

```javascript
    promiseObj.then((res)=>{
        console.log(res);
         return getStudentData(res[1])
      }).then((result)=>{
        console.log(result);
      }).catch((error)=>{ console.log(error)});

  ```
> Now handle the promise using `async/await`

```javascript
let consumePromise =  async ()=>{
      const resp = await promiseObj;
      console.log(resp);
      const biodata = await getStudentData(resp[1]);
      console.log(biodata);
  };
  consumePromise();
  ```


# combine multiple promises

## Promise.all()

 > Sometimes, you need all the promises to be fulfilled, but they don't depend on each other. 
  In a case like that, it's much more efficient to start them all off together, then be notified
   when they have all fulfilled.
  Promise.all() methode you need here.

  > fulfilled when and if all the promises in the array are fulfilled. In this case, the .then() 
  handler is called with an array of all the responses, in the same order that the promises were 
  passed into all().
  
  > rejected when and if any of the promises in the array are rejected. 
  In this case, the catch() handler is called with the error thrown by the promise that rejected.
  
  ```javascript
  const fetchPromise1 = fetch('https://jsonplaceholder.typicode.com/users/');
const fetchPromise2 = fetch('https://jsonplaceholder.typicode.com/posts');
const fetchPromise3 = fetch('https://jsonplaceholder.typicode.com/todos');

Promise.all([fetchPromise1, fetchPromise2, fetchPromise3])
  .then((responses) => {
    for (const response of responses) {
      console.log(`${response.url}: ${response.status}`);
    }
  })
  .catch((error) => {
    console.error(`Failed to fetch: ${error}`)
  });
  
    //https://jsonplaceholder.typicode.com/users/: 200
    //app.js:475 https://jsonplaceholder.typicode.com/posts: 200
    //app.js:475 https://jsonplaceholder.typicode.com/todos: 200
  
  ```
  
  > If we try the same code with a badly formed URL, like this:

```javascript
 const fetchPromise1 = fetch('https://jsonplaceholder.typicode.com/use.......');
const fetchPromise2 = fetch('https://jsonplaceholder.typicode.com/posts');
const fetchPromise3 = fetch('https://jsonplaceholder.typicode.com/todos');

Promise.all([fetchPromise1, fetchPromise2, fetchPromise3])
  .then((responses) => {
    for (const response of responses) {
      console.log(`${response.url}: ${response.status}`);
    }
  })
  .catch((error) => {
    console.error(`Failed to fetch: ${error}`)
  });
  
  //Failed to fetch: TypeError: Failed to fetch
  ```
## Promise.any()

>Sometimes, you might need any one of a set of promises to be fulfilled, and don't care which one. In that case, you want Promise.any(). This is like Promise.all(), except that it is fulfilled as soon as any of the array of promises is fulfilled, or rejected if all of them are rejected:

```javascript
const fetchPromise1 = fetch('https://jsonplaceholder.typicode.com/users/');
const fetchPromise2 = fetch('https://jsonplaceholder.typicode.com/posts');
const fetchPromise3 = fetch('https://jsonplaceholder.typicode.com/todos');

Promise.any([fetchPromise1, fetchPromise2, fetchPromise3])
  .then((response) => {
    console.log(`${response.url}: ${response.status}`);
  })
  .catch((error) => {
    console.error(`Failed to fetch: ${error}`)
  });
  ```
  - In this case you can not predict which promise fulfill first.
  
  
  
  
  ### Closure
  
  Functions and its lexical scope bundled together forms a closure.
  In other words, a closure gives you access to an outer function’s scope from an inner function. 
  ```javascript
  function x(){
  let a=7;
  function y(){
        console.log(a);
    }
    return y;
  }
  
  //now calling the outer function 
  var z = x();
  //after to many lines of code
  
  z();  //output will be 7
  
  ```
When a function return, it remember its lexical scope means from where it was actually returned.
In the given example, When we return from function x, It's execution context would be vanised but the inner function y still remembered or maintain there lexical scope. This concept is known as ***Closure***.


```javascript
function sayHello() {
  var say = function() { console.log(hello); }
  // Local variable that ends up within the closure 
  var hello = 'Hello, world!';
  return say;
}
var sayHelloClosure = sayHello(); 
sayHelloClosure(); // ‘Hello, world!’
```

### Hoisting
 Hoisting is a concept in javascript by which you can access variables and function even before its initialized it.
 Take a look at these examples
 
 ***example-1***
 ```javascript
 var x= 10;
function getName(){
    console.log("vikki");
}

console.log(x); //output 10

getName(); // output vikki
```

***example-2***

```javascript
console.log(x); //output undefined

getName(); // output vikki
 var x= 10;
function getName(){
    console.log("vikki");
}

```
***NOTE***

Any variable is giving undefined in logs means memory allocated for the variable but not assigned any value to it.
but if a variable show "not defined" that is reference error means variable has no any existance. it has no memory allocation yet.


- In case of arrow function 
```javascript
getName();
let getName = () => {
    console.log("vikki");
}

// TypeError:- getName is not a function
```
Hence, In the case of arrow function, If you are trying to access before it's definition it behaive like a variable not like function. 


### Undefined and not defined

Undefined is a special keyword which assign to a variable to there memory location untill unless any particular value assigned to it.
but, not defined i.e reference error means there is no any existance of the variable in the particular scope.

### var,let and const explanation

var is the keyword used for declare a variable in javascript.
Before ES6 var declaration ruled.

***Scope of var***
Scope essentially means where these variables are available for use. var declarations are globally scoped or function/locally scoped.
The scope is global when a var variable is declared outside a function. This means that any variable that is declared with var outside a function block is available for use in the whole window.
var is function scoped when it is declared within a function. This means that it is available and can be accessed only within that function.


var variables can be re-declared and updated
This means that we can do this within the same scope and won't get an error.

```javascript
    var greeter = "hey hi";
    var greeter = "say Hello instead";
    
    //or we can do this like also
     var greeter = "hey hi";
    greeter = "say Hello instead";

```

***Hoisting of var***
Hoisting is a JavaScript mechanism where variables and function declarations are moved to the top of their scope before code execution. This means that if we do this:
```javascript
 console.log (greeter);
    var greeter = "say hello"

```

it is interpreted as this:

```javascript
    var greeter;
    console.log(greeter); // greeter is undefined
    greeter = "say hello"
    
```
   So var variables are hoisted to the top of their scope and initialized with a value of undefined.
   
***Problem with var***
There's a weakness that comes with  var. I'll use the example below to explain:
```javascript
    var greeter = "hey hi";
    var times = 4;

    if (times > 3) {
        var greeter = "say Hello instead"; 
    }
    
    console.log(greeter) // "say Hello instead"
```

***Let***

let is now preferred for variable declaration. It's no surprise as it comes as an improvement to var declarations.
  
  ***let is block scoped***
  
A block is a chunk of code bounded by {}. A block lives in curly braces. Anything within curly braces is a block.

So a variable declared in a block with let  is only available for use within that block. Let me explain this with an example:


***let can be updated but not re-declared***

Just like var,  a variable declared with let can be updated within its scope. Unlike var, a let variable cannot be re-declared within its scope. So while this will work:

```javascript
let greeting = "say Hi";
    greeting = "say Hello instead";
    
//this will return an error:

    let greeting = "say Hi";
    let greeting = "say Hello instead"; // error: Identifier 'greeting' has already been declared

```

However, if the same variable is defined in different scopes, there will be no error:

   ```javascript
   let greeting = "say Hi";
    if (true) {
        let greeting = "say Hello instead";
        console.log(greeting); // "say Hello instead"
    }
    console.log(greeting); // "say Hi"
```

***Hoisting of let***

Just like  var, let declarations are hoisted to the top. Unlike var which is initialized as undefined, the let keyword is not initialized. So if you try to use a let variable before declaration, you'll get a Reference Error.


***Const***
Variables declared with the const maintain constant values. const declarations share some similarities with let declarations.

***const declarations are block scoped***
Like let declarations, const declarations can only be accessed within the block they were declared.


***const cannot be updated or re-declared***

This means that the value of a variable declared with const remains the same within its scope. It cannot be updated or re-declared. So if we declare a variable with const, we can neither do this:


```javascript
const greeting = "say Hi";
    greeting = "say Hello instead";// error: Assignment to constant variable. 


//nor this:

    const greeting = "say Hi";
    const greeting = "say Hello instead";// error: Identifier 'greeting' has already been declared

```
Every const declaration, therefore, must be initialized at the time of declaration.

***Hoisting of const***
Just like let, const declarations are hoisted to the top but are not initialized.


### So just in case you missed the differences, here they are:

- var declarations are globally scoped or function scoped while let and const are block scoped.
- var variables can be updated and re-declared within its scope; let variables can be updated but not re-declared; const variables can neither be updated   nor re-declared.

- They are all hoisted to the top of their scope. But while var variables are initialized with undefined, let and const variables are not initialized.
- While var and let can be declared without being initialized, const must be initialized during declaration.

### let and const hoisting

let and const declaration are  hoisted but they are hoisted very differently.These are in temporal deadzone for the time being.

***Take the example***
```javascript

console,log(b);
let a = 10;
console.log(a);
var b=20;


```
Even before executing single line of code,these are the seen of the browser scope
- Script

    a:undefined
    
 - Global  

    b: undefined
    
Here we can see that a is not in the global object. It is not in the something else that is script. So Whenever we will try to acess let declaration. It will show reference error because it will be unable to find out in the global object.

***Temporal Dead Zone***
The time period from hoisting till variable assign some value,that time period is known as temporal dead zone.
When a variable in temporal dead zone one can't access them because  that will throw reference error.

### Callback
A callback function in javascript is a function that is passed as an argument in another function. Which is then called inside the parent function to complete a routine or an action. To put it more simply, it is a function that will be called(executed) later after its parent function(the function in which it is passed as an argument) is done executing.

Lets take a example
```javascript

function sum(a, b) {
  console.log(a + b)
}

function operation(val1, val2, callback) {
  callback(val1, val2)
}

operation(6, 5, sum)

```
In the above code, we can see in the function operation the third parameter is a callback function. We are first passing the "callback" as an argument and then calling it inside the parent function i.e.i.e., operation.

***Need for Callback Functions***

Javascript is a single-threaded language, which means, it executes the code sequentially one line after another. However, there are some cases in which a part of the code will only run after some lines of code, i.e. it is dependent on another function. This is called asynchronous programming.

- We need callback functions to make sure that a function is only going to get executed after a specific task is completed and not before its completion.

        - For instance, in the case of an event listener, we first listen for a specific event, and if the function detects it, then only the callback function is executed. It's just like when you go into a restaurant, the function(action) to make a pizza will only run after you order a pizza. And in the meantime, the restaurant staff(browser or nodeJS) will do some other work.

- Callback functions are also needed to prevent running into errors because of the non-availability of some data that are needed by some other function.

            - For example, when a function fetches some data from an API that takes some time to get downloaded. We use the callback function here so that the function that needs the data will only get executed when all the required data is downloaded.

1. Synchronous Callback Function
The code executes sequentially - synchronous programming.

```javascript
console.log('Start')

function divide(a, b) {
  console.log(a / b)
}

function operation(val1, val2, callback) {
  callback(val1, val2)
}

operation(16, 4, divide)

console.log('End')

//output
//Start
//4
//End


```

2. setTimeout() - Asynchronous Callback Function

```javascript
console.log('Start')

setTimeout(() => {
  console.log('We are in the setTimeout()')
}, 4000)

console.log('End')


//output
//Start
//End
//We are in the setTimeout()


```

***Let's see it in detail :***

Here, as we can see the code is not running sequentially. The console.log("End"); statement get executed before the setTimeout() function. This is called asynchronous programming.

setTimeout() function takes two parameters the first one is a callback function and the second parameter is the time value in milliseconds. The callback function will get executed after the amount of time specified in the second parameter.

The browser or the nodeJS engine will not wait for the setTimeout() function to execute its callback function. In the meantime, it'll move to the next statement and execute that, in our case, that is console.log("End");.

### First class function/citizen

The ability of a function to use as a value means assign to a variable, pass as a argument to another function,return from a function etc, is known as first class function.

When functions in a programming language are treated like any other variable then that programming language is known to have first-class functions. In javascript, the functions are known as the first-class citizens, which means functions can do what any other variables can. First-class functions javascript get this ability by treating the functions as an object.

***Examples for First Class Functions Javascript***

Following are the examples where we treat the functions as the first class functions in javascript.

Example 1: Assign function to a variable In the following coding example you can see how to assign the function to a variable.

```javascript
const myVariable = function () { // Assigning a function to a variable
    console.log("Inside the function...");
 }

 myVariable(); // Invoking the function using the variable

//output -   Inside the function...

```




```javascript
function wishHappyNewYear() {
    return "Happy New Year, ";
}
 
// Here in `wishMessage` we receive the function as a parameter.
function wishPerson(wishMessage, name) { 
   console.log(wishMessage() + name + '!!!');
 }

 // Pass `wishHappyNewYear` as an argument to the `wishPerson` function
 wishPerson(wishHappyNewYear, "John Doe");

//Happy New Year, John Doe!!!

```

```javascript
function sayHello() {
    // returning the function
    return function() {
       console.log("Hello!");
    }
 }

// Storing the returned function in the `newFun` variable
const newFun = sayHello();

// Calling the function which returned function with `newFun`
newFun();

```

```javascript
function sayHello() {
    // returning the function
    return function() {
       console.log("Hello!");
    }
 }

// Calling returned function using double parentheses
sayHello()();

```
