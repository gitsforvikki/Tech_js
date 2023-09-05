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

> For instance, in the case of an event listener, we first listen for a specific event, and if the function detects it, then only the callback function is executed. It's just like when you go into a restaurant, the function(action) to make a pizza will only run after you order a pizza. And in the meantime, the restaurant staff(browser or nodeJS) will do some other work.

- Callback functions are also needed to prevent running into errors because of the non-availability of some data that are needed by some other function.

> For example, when a function fetches some data from an API that takes some time to get downloaded. We use the callback function here so that the function that needs the data will only get executed when all the required data is downloaded.

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
### function statement
A function statement (also known as a function definition or function declaration) consists of the function keyword, followed by:

- The function name
- The parameter list(enclosed in parentheses)
- The javascript statements that defines the function (enclosed in curly braces{})


For example:
```javascript
function add(a,b){
return a+b;
}
console.log(add(5,10))

```

### function expression

Function assign to a variable,It's act like a variable, known as function expression.
```javascript
let sayHi = function() {
  alert( "Hello" );
};

sayHi();

```
### Diff. between function statement and function expression

The main difference is hoisting.
You can access function statement before it's declaration but cat't access function expression before declaration. It will throw typeError i.e not a function.

### Anonymous function

Anonymous functions in JavaScript, are the functions that do not have any name or identity. Just like, you have a name by which everyone calls you or identifies you. But, the anonymous functions, do not have any name, so we cannot call them like any other function in JavaScript.

Below given is the syntax for the anonymous function in JavaScript --
```javascript
let variableName = function () {
    //your code here
}

variableName();     //Can call the anonymous function through this
```

***Explanation:***
We write the anonymous function in Javascript by writing function() followed by the parentheses, where we may pass the parameters, and skipping the function name. Then in a pair of curly braces {}, we write our desired code. Finally, we assign this function to a variable. We can later use the variable whenever we need the value of the anonymous function.

Example-2
```javascript
button.addEventListener('click', 
    function(event) {
    alert('Button is clicked!')
})

```
### Arrow function
Arrow functions are another great use case for anonymous functions in javascript. Arrow functions provide a shorthand way of declaring anonymous functions. They were introduced as part of ES6.


***Examples of Arrow functions***

After having studied in detail the arrow functions in JavaScript, let us now see some examples which will further enhance our understanding.

```javascript
let x = a => a * a;	//Example of single param & single line code
console.log(x(10));

//output 100
```

```javascript
let x = (a,n) => {
    let pow = 1;
    for(let i = 0; i < n; i++){
	    pow = pow * a;
	}
    return pow;
};
console.log(x(10,3));

```


### How Does Code Execute in Javascript?

To maintain the order of what’s currently running, Javascript uses a stack while executing the code.
executions happen inside execution contexts, executions contexts as javascript objects that contain the execution of code. Functions have local executions contexts which are generated when the functions are called and everything else is run on the global execution context.


The execution context of the code currently being executed will be on top of the stack. Once it is done executing, the execution context is popped out of the stack. This data structure is famously known as the call stack.


Let’s understand with an example:

```javascript
let sayHello = (name) ⇒ { console.log(`Hello ${name}` };
let sayBye = (name) ⇒{ console.log(`Bye ${name}` } ;
let communicate() = (thingToSay) = {console.log(`${thingsToSay`)};
console.log(”Communication Started”);
sayHello("Harsh");
communicate("This is how code is executed in JS");
sayBye("Harsh");
console.log(”Communication Over”);

```
Doing things” in Javascript is achieved using a call stack and JS Engine. While JS Engine executes the code, the call stack maintains the order of execution.At the top of the stack is the object representing the call in current execution, which upon completion/termination will be popped out and the control will be passed to the next call/ execution context.



![callstack](https://user-images.githubusercontent.com/52384251/226542352-de53312e-c2da-44c3-be6d-8b9c69b35f78.png)


- Here’s what shall happen during the execution of the above code.

1. A global execution context is created and immediately put on the call stack which was earlier empty. Once the context is in the call stack the JS engine begins executing the code.

2. When the control starts with line 4, it logs “Communication Started” on the console.

3. On line 5, the control finds a function invocation that creates a local execution context, immediately the execution context of sayHello is put on top of the call stack.

4. S Engine will only execute the code within the context of the top element of the stack and hence the code inside sayHello begins executing and “Hello Harsh” is printed on the console.

5. Since there is no code to further execute The context of sayHello is popped out of the stack and the control moves to the next line.

6. The control finds another function invocation and the process from 3 to 5 happens for the communication function. As a result “This is how code is executed in JS” is printed on the console.

7. When control reaches the next line, a similar process follows, and “Bye Harsh” is printed on the console.

8. Now, the control is back with the global execution context which was only half done executing. The control starts executing from where it left off and “Communication Over” is logged

9. Since there is no code left in the global execution context, that is always popped out of the call stack and call stack is empty.



### But Where is Concurrency?
Concurrency is the ability to do multiple things at once. It comes with asynchronicity which is an important concept in present-day javascript. It brings along another special data structure called a callback queue. Let’s try to understand with a classic example of setTimeout.

setTimeout introduces most of the developers to asynchronicity in javascript. It accepts two parameters, a callback function and time in milliseconds.

Back to an Example:


```javascript
console.log("Start")
setTimeOut(function delayThis(){console.log(”This will be delayed”)}, 5000)
console.log("End")

```
What do you think will be out of the above lines of code?


- Whenever setTimeOut is called, the call stack now contains setTimeOut and waits for the delay to happen before moving on to the next line.

- When setTimeout is called, the function call goes to the top of the call stack but is immediately popped. The next line gets executed immediately 	(without waiting for the delay)




Let’s try to understand the order of execution here. When the code begins executing, the first thing that is logged on the console is “Start”. This is pretty obvious.

When the control comes to the second line, setTimeout is called and a callback function is registered for future execution.

The control does not wait for the delay to happen. Essentially delay does not mean “a delay in the program” rather it means “a delay in execution of the callback function”. Both are very different things.Next up the control immediately moves to the third line

***Note***


Note: Not all callbacks have same priority. Some callbacks use a special queue known as the micro task queue/Job queue which has higher priority order than the callback queue. It introduces more interesting concepts like starvation in a queue.

### Event Loop in JavaScript

![event-loop](https://user-images.githubusercontent.com/52384251/226545252-e4f55618-fb20-4112-83d4-a7c2ecca515e.png)



We know by this point that JS Engine and the call stack are the primary things responsible for running the javascript code. In order for the code to be executed, it has to be brought on the call stack for JS Engine to execute it.

Now recall the callback function in the example above. Last we heard of it was when it was in some fancy new memory unit called the callback queue. After 10 seconds have passed, it has to be executed. Event loop in JavaScript is such a mechanism which handles this. This means it manages the lifecycle of a callback function from the callback queue to the call stack.

### But How does it work?
Let’s begin by stating the universal truth - “A call stack will have to be empty for the event loop to bring anything in it”.

Whenever the call stack is empty, the first entered unit in the callback queue is brought to the call stack by the event loop which was observing both the call stack and callback queue.

Stack: This is where all your javascript code gets pushed and executed one by one as the interpreter reads your program, and gets popped out once the execution is done. If your statement is asynchronous: setTimeout, ajax(), promise, or click event, then that code gets forwarded to Event table, this table is responsible for moving your asynchronous code to callback/event queue after specified time.

Heap: This is where all the memory allocation happens for your variables, that you have defined in your program.

Callback Queue: This is where your asynchronous code gets pushed to, and waits for the execution.

Event Loop: Then comes the Event Loop, which keeps running continuously and checks the Main stack, if it has any frames to execute, if not then it checks Callback queue, if Callback queue has codes to execute then it pops the message from it to the Main Stack for the execution.

Job Queue: Apart from Callback Queue, browsers have introduced one more queue which is “Job Queue”, reserved only for new Promise() functionality. So when you use promises in your code, you add .then() method, which is a callback method. These `thenable` methods are added to Job Queue once the promise has returned/resolved, and then gets executed.

Quick Question now: Check these statements for example, can you predict the sequence of output?:

```javascript
console.log('Message no. 1: Sync');
setTimeout(function() {
   console.log('Message no. 2: setTimeout');
}, 0);
var promise = new Promise(function(resolve, reject) {
   resolve();
});
promise.then(function(resolve) {
   console.log('Message no. 3: 1st Promise');
})
.then(function(resolve) {
   console.log('Message no. 4: 2nd Promise');
});
console.log('Message no. 5: Sync');
```
Some of you might answer this:
```javascript
// Message no. 1: Sync
// Message no. 5: Sync
// Message no. 2: setTimeout
// Message no. 3: 1st Promise
// Message no. 4: 2nd Promise

```
because setTimeout was pushed to Callback Queue first, then promise was pushed. But this is not the case, the output will be:
```javascript
// Message no. 1: Sync
// Message no. 5: Sync
// Message no. 3: 1st Promise
// Message no. 4: 2nd Promise
// Message no. 2: setTimeout
```
All `thenable` callbacks of the promise are called first, then the setTimeout callback is called.

Why?: Job Queue has high priority in executing callbacks, if event loop tick comes to Job Queue, it will execute all the jobs in job queue first until it gets empty, then will move to callback queue.

### Did You Notice?

setTimeout of an ‘n’ seconds does not guarantee execution exactly when ‘n’ seconds have passed, rather it guarantees a minimum delay of ‘n’. This is because after a defined limit, the callback function moves to the callback queue (stating ready for execution) where the event loop is supposed to pick it for actual execution.

> Conclusion
- JS is single threaded. It has only one call stack.
- Execution of code in javascript is always line by line.
- Code in javascript is executed by JS Engine which uses the call stack to determine the order of execution.
- Event loop in JavaScript is a mechanism through which the ‘calls waiting for execution’ in the callback queue/job queue can be put on the call stack.
- For any event from the callback queue/job queue to come to call stack, the call stack will have to be empty.





## Debouncing in javaScript

![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/2d250c54-4534-4e11-bcb9-38a3afba5bb1)

### What is debouncing?
Debouncing is a way of delaying the execution of a function until a certain amount of time has passed since the last time it was called. This can be useful for scenarios where we want to avoid unnecessary or repeated function calls that might be expensive or time-consuming.

For example, imagine we have a search box that shows suggestions as the user types. If we call a function to fetch suggestions on every keystroke, we might end up making too many requests to the server, which can slow down the application and waste resources. Instead, we can use debouncing to wait until the user has stopped typing for a while before making the request.

### How to implement debouncing in JavaScript?
There are different ways to implement debouncing in JavaScript, but one common approach is to use a wrapper function that returns a new function that delays the execution of the original function. The wrapper function also keeps track of a timer variable that is used to clear or reset the delay whenever the new function is called.


```javascript
const debounce = (mainFunction, delay) => {
  // Declare a variable called 'timer' to store the timer ID
  let timer;

  // Return an anonymous function that takes in any number of arguments
  return function (...args) {
    // Clear the previous timer to prevent the execution of 'mainFunction'
    clearTimeout(timer);

    // Set a new timer that will execute 'mainFunction' after the specified delay
    timer = setTimeout(() => {
      mainFunction(...args);
    }, delay);
  };
};

```

### Using wrapping function with debounce

```javascript
// Define a function called 'searchData' that logs a message to the console
function searchData() {
  console.log("searchData executed");
}

// Create a new debounced version of the 'searchData' function with a delay of 3000 milliseconds (3 seconds)
const debouncedSearchData = debounce(searchData, 3000);

// Call the debounced version of 'searchData'
debouncedSearchData();
```
Now, whenever we call ```debouncedSearchData```, it will not execute ```searchDataimmediately```, but wait for 3 seconds. If ```debouncedSearchData``` is called again within 3 seconds, it will reset the timer and wait for another 3 seconds. Only when 3 seconds have passed without any new calls to ```debouncedSearchData```, it will finally execute ```searchData```.






##What is throttling
Throttling is also used to rate-limit the function call. Throttling will fire the function call only once in 1000ms(the limit which we have provided), no matter how many times the user fires the function call.

![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/653ee35c-e0f0-4517-ba78-90a22db0344a)

### Custom Throttling function
![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/ef712a39-67d8-4c7b-a4fb-9080a732cade)

### Conclusion
I hope after reading this article these two concepts by javascript are cleared.

Throttling and debouncing can be implemented to enhance the searching functionality, infinite scroll, and resizing of the window.




## Critical rendering path
The Critical Rendering Path is the sequence of steps the browser goes through to convert the HTML, CSS, and JavaScript into pixels on the screen. Optimizing the critical render path improves render performance.
The critical rendering path includes the Document Object Model (DOM), CSS Object Model (CSSOM), render tree and layout.

### How does the browser rendering engine work?
In order to render content the browser has to go through a series of steps:
1. Document Object Model(DOM)
2. CSS object model(CSSOM)
3. Render Tree
4. Layout
5. Paint.

![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/2970fb2e-8de6-4853-98ed-2976fdfcda5d)

### Document Object Model (DOM)
When we request data from server using URL ,we receive the response in the form of HTTP messages which consists of three parts Start line,Header files and Body.
The start line and headers are textual and the body can contain arbitrary binary data(images,videos,audio) as well as text.

Once the browser receives the response (HTML markup text) , browser must convert all the markup into something which we usually see on or screens.

The browser follows well defined set of steps and it starts with processing the HTML and building the DOM.

Convert bytes to characters
Identify tokens
Convert tokens to nodes
Build DOM Tree


When this process is finished the browser will have the full content of the page, but to be able to render the browser has to wait for the CSS Object Model, also known as CSSOM event, which will tell the browser how the elements should look like when rendered.

### CSS Object Model
Just as with HTML, the CSS rules need to be converted into something that the browser understands, so these rules go through the same steps as the document object model.

1. Convert bytes to characters
2. Identify tokens
3. Convert tokens to nodes
4. Build CSSOM

In this stage the CSS parser goes through each node and gets the styles attributed to it.

###  The Render Tree
This stage is where the browser combines the DOM and CSSOM, this process outputs a final render tree, which contains both the content and the style information of all the visible content on the screen.

### Layout
This stage is where the browser calculates the size and position of each visible element on the page, every time an update to the render tree is made, or the size of the viewport changes, the browser has to run layout again.

### Paint
When we get to the paint stage, the browser has to pick up the layout result, and paint the pixels to the screen, beware in this stage that not all styles have the same paint times, also combinations of styles can have a greater paint time than the sum of their parts. For an instance mixing a border-radius with a box-shadow, can triple the paint time of an element instead of using just one of the latter.

## Difference Between ‘async’ and ‘defer’ Attributes in JavaScript

‘async’ and ‘defer’ are boolean attributes which we use along with script tags to load external javascript libraries efficiently into our web page.

### How things work without these attributes?
When the web page is loaded, the web browser looks at the entire HTML document and looks for any CSS, JavaScript and images that are referenced by that page. This is what we refer as HTML Parsing. If resources are found in the HTML, the web browser then requests each of those resource files from the server. Once the web browser has the required resources it can start building the page.

Now one of the important things is how the scripts are loaded. Typically JavaScript is considered as a Parser blocking language. Why? Lets understand this.

![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/6f91429d-779b-4498-9ee7-48e87733ae62)


The web browser starts parsing the HTML and it gets paused when the script tag is reached (here I am strictly talking about external JS Files and not the inline scripts). At that point, parsing of HTML is blocked and browser makes a request to fetch/download the respective script file. Once the script is fetched, it gets executed and then HTML parsing resumes again.


But this is not good as the JavaScript files are blocking the rendering of HTML. So this is where we can introduce our two attributes ‘async’ and ‘defer’.

### How to use ‘async’ attributes?



![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/3202e3f1-d752-40fe-ba8a-e43b0444c126)

With async (asynchronous) attribute, the HTML parsing continues until the browser fetches the script file over the network so parsing and script fetching happens in parallel (as shown in the figure below). Once the scripts are available in the browser, HTML parsing is paused and scripts are executed. Once the execution is complete, HTML parsing continues.

### How to use ‘defer’ attributes?
![image](https://github.com/gitsforvikki/Tech_js/assets/52384251/29f7ad61-bdf4-4b1a-aad7-fcb9853fd7ce)

The word ‘defer’ in English means to ‘hold back’. So with defer attribute mentioned in the script tag, the script files are downloaded in parallel while the HTML parsing continues. But the execution is deferred until the HTML parsing is done. In simple words, the downloaded scripts are executed only when the browser finishes its HTML parsing.

### When to use ‘async’ and when to use ‘defer’?
Well, you can use async attribute when your page does not depend on the script files (for example analytics). Why? Because async cannot guarantee the order in which your scripts files will be downloaded. So if there is any dependency amongst your script files, it may break your code. In such cases you can use defer attribute.


## How to Use the "this" Keyword in JavaScript

In JavaScript, the this keyword is a reference variable to the current object. Basically, it refers to the object which is defining the current context. In simple words, it points to the object which is executing the current piece of JavaScript Code this keyword in JavaScript can be used in global and function contexts. this takes different values in various scenarios. Let us dive in and learn more!

 ```this``` keyword in JavaScript, as a reference variable points to an object, according to the context it was defined.

### Global Context
Global execution context basically means everything out of any function, in other words, the global scope. In Global Context, this refers to the global object for example in a browser, it is the window object or global object on Node.js.
```javascript
console.log(this === window) 

//Output: true
```

True is returned since this refers to the window object.

If any property is assigned to this object in the global context, JavaScript will add it to the window object. This is because the global object and window object both imply the same as discussed above.
```javascript
this.name= 'James';
 console.log(window.name); // 'James'

// Output:James
```
In the above case, ```window.name``` returns the value assigned to ```this.name```.

 Let us take an example to understand better:

```javascript
//variable declared in a global scope
var name = "James";

function Employee() {
    var name = "Jack"; 
    console.log("name: " + name); 
//this here refers to the global object 
    console.log("this.name = " + this.name); 
}

Employee(); //can be considered as window.Employee()

//output

Jack
James


```
In the above example, the function "Employe()" is being called in the global scope i.e. in the context of the window object. Hence, this can be alternatively called as window.Employee(). Thus, the this keyword in the function shall refer to the window object as well. Hence this.name returns James. Whereas, for the local scope if the variable is called i.e without this, it refers to the local name variable.

### Function Context
In JavaScript, functions have their own properties. this is one of those properties. Hence, when this is used inside the function, its value will change according to the way the function is defined and invoked as the context also changes accordingly. Basically, invocation means the execution of the code inside the function or calling the functions. This can be done in the following ways:

### Direct Invocation
It is a simple invocation method where we invoke a function using its name or some expressions that evaluate the function object. Here, this refers to the global object. Let us take an example:

```javascript
var grade = 10;
function student() { 
	console.log(this === window); // true 
	console.log(this.grade); // 10
} 
student();
}

//output
true
10
```

So, the student() function is invoked through the simple function invocation and hence this refers to the global or window object.

### Indirect Invocation
In JavaScript, all the functions are instances of the Function type. So basically, Function has two methods that can be used to set the reference of thethis keyword. These are:

- call(): Calls a method using the owner object as a parameter.
- apply(): Similar to call(), with apply() you can write a method that can be used on different objects.
When invoked using these methods, it is known as indirect invocation. We shall discuss these methods along with examples in a section ahead

### Invoking Function: call() and apply()
call() and apply() essentially perform the same task. These methods are called on the function and take different parameters. These help you call or invoke the function indirectly and set the values of the this keyword.

Syntax for call()
```javascript
function.call(thisArg,arguments)

```

Parameters for call():

- thisArg: It is the object that this refers to inside the function. It is a compulsory parameter.
- arguments: These are the function arguments. It is optional in nature.

Syntax for apply()

```javascript
function.apply(thisArg,arrayOfarguments)

```
Parameters for apply():

- thisArg: It is the object that this refers to inside the function. It is a compulsory parameter.
- arrayOfarguments: apply() method takes arguments in form of an array. It is optional in nature.

Let us take an example to understand better!


```javascript
function getDetails(student) {
    console.log(student + this.Grade);
}

	let student1 = {
	    Grade: '10'
	};
	let student2 = {
	    Grade: '8'
	};

	getBrand.call(student1, "The Grade obtained by James: ");
	getBrand.apply(student2, ["The Grade obtained by Jack: "]);
}


//output
The Grade obtained by James: 10
The Grade obtained by Jack: 8

```

In the above program, we have called the ```getDetails()``` function indirectly using the ```call()``` and ```apply()``` methods. We have passed the ```student1 and student2``` objects as the first argument of the ```call() and apply()``` methods, therefore, the corresponding Grade is obtained in each call. So this refers to the object passed as a parameter in each call. This is how we can set the value of this by indirectly invoking the function.

### Bind()
call() and apply() can invoke a function but to return a new function that can be stored or passed bind() method is used. bind() was introduced in ECMAScript 5. When a function is invoked, this method sets the reference of this as per the provided value and returns a new function.

Syntax:

The bind method is called on the method as follows:

```javascript
  function.bind(thisArg , arg1, arg2......)

```
Parameters:

- thisArg: Object that this shall refer to.
- arg1, arg2, argN...: These are the function arguments, n in number.

```javascript
// student1 object with getName() funcn

let student1 = {
    name: 'James',
    getName: function () {
        return this.name;
    }
}

// student2 object
let student2 = {
    name: 'Jack'
}

console.log(student1.getName()); //James
//reference of this defined in student1 is set to student2 object using bind
let name = student1.getName.bind(student2);
console.log(name());//Jack


//output
James
Jack

```

In the above example, we have two objects ```student1 and student2```. ```student1``` has a method ```getName``` and the this inside this method shall refer to the ```student1``` object itself. This is because in JavaScript ```this``` keyword inside the object's method refers to the owner object. Now in order to set the reference of this inside the method of ```student1 ``` to another object, (student2 in this case), we have used the ```bind()``` method. As a parameter to the ```bind()``` method, ```student2``` is passed. Thus, the reference of this is set according to the specified value.

