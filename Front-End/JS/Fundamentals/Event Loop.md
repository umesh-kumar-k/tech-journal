
📚 Topic: [Event Loop]
**Date:** [Insert Date]  
**Course/Class:** [https://www.lydiahallie.com/blog/event-loop]  

---

## 🔍 Keywords
> 

- JS Runtime
- Single Call Stack
- Stack Frame
- Execution Context
- Event Loop
- Web APIs
	- fetch,setTimeout,getElementById
- Task Queue
- Microtask Queue
	- MutationObserver callback
	- queueMicrotask callback
- Asynchronous tasks
- Relationship between Call Stack & Thread
- Multi Threaded vs Single Threaded


---

## 📖 Notes 
> 

- ## Call Stack

	- Manages execution of the program.
	    
	- For every function invocation, a new execution context is created and pushed onto the call stack.
	    
	- The execution context is popped off the call stack when the function execution completes.
	    
	- JavaScript is single-threaded, so it can handle only one task at a time.
	    
	    - Longer-running tasks can block other tasks from executing.
	        
	- Network requests, timers, or any task that requires user input are long-running tasks.
	    
	    - These tasks aren’t part of the JS runtime itself but are provided by the browser’s Web APIs.
## Web APIs

- Act as a bridge between the JS runtime and browser features (e.g., rendering, network services).
    
- Some Web APIs initiate async tasks and offload long-running work to the browser.
    
- Handlers (callbacks) are registered to run upon completion of those async tasks.
    
- After initiating an async task, its execution context is popped off the call stack—making it non-blocking.
    
- Async-capable APIs use either callbacks or promises:
    
    - Callbacks: Pass handler functions directly or attach them to the returned object.
        
    - Promises: Use `fetch().then().catch()` or `async/await`.

## Callback APIs

Example: Geolocation API

```
navigator.geolocation.getCurrentPosition(
  () => {
    // success handler
  },
  () => {
    // error handler
  }
);
```

- `getCurrentPosition()` execution context is pushed onto the call stack.
    
- The Web API registers the callbacks and offloads the operation to the browser.
    
- The execution context is then popped off the call stack.
    
- Until the browser returns data, other execution contexts can be pushed and popped off the stack.
    
- Once the browser finishes, the success handler is added to the Task Queue.

## Task Queue

- Also called the “callback queue.”
    
- Handlers from callback-based APIs are enqueued here.
    
- The event loop pulls from this queue when the call stack is empty.
    
- Multiple handlers may wait in the Task Queue before execution.

## Microtask Queue

- Used for promise-based callbacks (`.then()`, `.catch()`, `async/await`).
    
- Also used for `MutationObserver` callbacks.
    
- Has higher priority than the Task Queue.
    
- Microtasks can schedule additional microtasks—excessive chaining may freeze the program briefly.

## Event Loop

- Continuously checks if the call stack is empty.
    
- When empty, it first drains all microtasks, then pulls the first task from the Task Queue onto the call stack.
    
- Example:
        
    ```
    setTimeout(() => {
      console.log('Timer expired');
    }, 1000);
    ```
    
    1. Calling `setTimeout()` pushes its execution context onto the call stack.
        
    2. The browser starts the timer and pops the context off the call stack.
        
    3. When the timer expires, the callback is queued in the Task Queue.
        
    4. Only when the call stack is empty will the event loop move that callback onto the stack.
        
- After microtasks run, the event loop returns to servicing the Task Queue if the call stack remains empty.

## Fetch API

- Invoking `fetch()` pushes its execution context onto the call stack.
    
- A promise object is created in the browser with properties:
    
    - `PromiseStatus` (initially `"pending"`)
        
    - `PromiseResult`
        
    - `PromiseFulfillReactions` (handlers registered via `.then()`)
        
- Once the network request is initiated, `fetch()` is popped off the call stack.
    
- When the server responds:
    
    1. `PromiseStatus` changes to `"fulfilled"`.
        
    2. `PromiseResult` is set to the response.
        
    3. The registered `.then()` handler is added to the Microtask Queue.
        
- The event loop moves that handler onto the call stack when it’s empty.

## Wrapping Callbacks with Promises

### Geo location with Promise



```
function getCurrentPosition() {
  return new Promise((resolve, reject) => {
    navigator.geolocation.getCurrentPosition(resolve, reject);
  });
}

// Using async/await
async function fetchAndLogCurrentPosition() {
  try {
    const position = await getCurrentPosition();
    console.log(position);
  } catch (error) {
    console.error(error);
  }
}

// Or using .then/.catch
function fetchAndLogCurrentPosition() {
  getCurrentPosition()
    .then(position => console.log(position))
    .catch(error => console.error(error));
}
```

### Delay with Promise



```
function delay(timeInMs) {
  return new Promise(resolve => {
    setTimeout(resolve, timeInMs);
  });
}

async function doStuff() {
  await delay(5000);
  console.log('Done waiting');
}
```
---

## 🧠 Summary
> 

- [[https://www.lydiahallie.com/blog/event-loop](https://www.lydiahallie.com/blog/event-loop)]

