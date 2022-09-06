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
