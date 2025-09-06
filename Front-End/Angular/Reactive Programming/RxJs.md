# 📚 Topic: [Reactive Progamming with RxJs]
**Date:** [Insert Date]  
**Course/Class:** [Insert Course Name]  
**Author:** [Your Name]

---

## 🔍 Cues / Questions / Keywords
> Use this section to jot down key terms, questions, or prompts that help you recall or organize the main ideas.

- [ ]
- [ ]
- [ ]
- [ ]

---

## 📖 Notes / Main Ideas

### RxJS
- Library for composing async & event based programs
- Combination of Observer pattern + Iterator pattern + Functional programming
- Observable
	- invokeable collection of future values or events (producer)
- Observer
	- collection of callbacks - listen to values delivered by observable (consumer)
- Schedulers
	- centralized dispatchers to control concurrency
- Subjects
	- equivalent to EventEmitter , only way to multicast a value or event to multiple observers
- Operators
	- pure functions that enable functional programming style with collections
- Subscription
	- represents the execution of observable , primarily used for cancelling the execution
	

### Push vs Pull

Pull - consumer decides when to receive data from producer , producer is unaware when the data will be delivered to consumer 
Push - producer determines when to send data to consumer

|      | Single Value | Multiple Values      |
| ---- | ------------ | -------------------- |
| Pull | JS function  | Iterators/generators |
| Push | Promise      | Observable           |

- Function is lazily evaluated computation that **synchronously** returns single value on invocation
- Generator/Iterator is lazily evaluated computation that synchronously returns 0 or infinite values on invocation
- Promise is a eagerly evaluated immediate computation that may or may not eventually return a single value.
- Observable is a lazily evaluated computation that can **sync/async** return 0 to infinite values. Doesn't have shared execution 

### Observable

- Creating Observable
	- new Observable()  / creation operator 
- Subscribing Observable
	- observer
- Executing Observable
	- deliver next(), error() , complete() notifications
- Disposing Observable
	- unsubscribe

#### Creating Observable's
Observable constructor  takes one arg - subscribe function 
```
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  const id = setInterval(() => {
    subscriber.next('hi');
  }, 1000);
});
```

#### Subscribe to Observable
```
observable.subscribe((val) => {

})
```

- subscribing to observable is like calling a function & providing CB where the data will be delivered to
- subscribe calls are not shared among multiple observers of the same observable
- each call to subscribe() triggers own setup for the given subscriber/observer
- with subscribe() - the given observer is not registered as a listener with the Observable

#### Executing Observable 
- Code inside the subscribe function passed to the Observable constructor represents the lazy computation - Observable execution
- Observable execution happens for each observer that subscribes 
- Execution produces multiple values over time - async/sync 
- 3 types of values that an observable can deliver 
	- next notification - sends a value
	- error notification - sends a JS Error or exception
	- complete notification - doesn't send a value 
- error/complete can happen only once & are mutually exclusive 
- Observable contract 
````
next*(error|complete)?
````

#### Dispose Observable Execution
- Since Observable Executions can be infinite , we need an API for cancelling execution
- subscribe() returns a subscription object - which represents an ongoing execution

```
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  // Keep track of the interval resource
  const intervalId = setInterval(() => {
    subscriber.next('hi');
  }, 1000);

  // Provide a way of canceling and disposing the interval resource
  return function unsubscribe() {
    clearInterval(intervalId);
  };
});

```

```
const subscription = observable.subscribe((x) => console.log(x));
```

- call subscription.unsubscribe() 

### Observer

- consumer of values delivered by Observable - set of CBs 
- CBs - next notification , error notification & completed notification

```
const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};
```

- supply the observer object to the subscribe function
````
observable.subscribe(observer);
````

or just provide the next CB to the subscribe function call 

```
observable.subscribe(x => console.log('Observer got a next value: ' + x));
```

- internally an Observer object will be created with the next CB function

- Observer can also be **partial** - if we don't provide error/complete CB, the execution of observable will happen but some types of notifications (error notification, complete notification) will be ignored as they don't have the CB.

```
const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
};
```


### Subscription

- represents an execution of Observable & returns unsubscribe() 
- dispose the resources held by subscription
- unsubscribe() of one subscription can unsubscribe multiple subscriptions

```
const subscription = observable1.subscribe(...));
   
const childSubscription = observable2.subscribe(...));

subscription.add(childSubscription);

setTimeout(() => {
// Unsubscribes BOTH subscription and childSubscription
subscription.unsubscribe();
}, 1000);
```


### Subject

- special type of Observable 
- plain Observable -> unicast = each subscribed observer owns an independent execution of the observable
- subject -> multicast to many observers 
- subjects are like EventEmitters - they maintain a registry of observers 
- subject is an observer  & observable as well 
- from the perspective of an observer it cannot tell whether a observable execution is coming from unicast observable or multicast subject 
- in subject the subscribe() -> registers the observer to a list of observers similar to JS addEventListener() 
- subject's role as an observer 
	- it has the next(), error() & complete() method
	- calling the next() method on the subject will multicast the value to all observers registered to listen to the subject

```
import { Subject } from 'rxjs';

const subject = new Subject<number>();

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(1);
subject.next(2);

// Logs:
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
```


- convert a regular unicast observable into multicast observable 

- unicast

```
const observable = from([1, 2, 3]);

observable.subscribe((x)={
.... own execution context
});

observable.subscribe((x)={
.... own execution context
});

```

- multicast

```
const subject = new Subject<number>();

observable.subscribe(subject);
```


#### Subject Types

1. Behaviour Subject
		- sends old value to the observers (consumers)
		- stores the latest emitted value 
		- when a new observer subscribes it gets the latest value 

2. Replay Subject
		- similar to Behaviour Subject
		- records / stores multiple  emitted values 
		- when a new observer subscribes it replays all old values 
	```
	const subject = new ReplaySubject(3);
	```

3. Async Subject
		- only the last value of the Observable execution is sent to its observers, and only when the execution completes


---


## 🧠 Summary
> After reviewing your notes, write a brief summary of the key takeaways in your own words.

- [Summary paragraph or bullet points]

---
