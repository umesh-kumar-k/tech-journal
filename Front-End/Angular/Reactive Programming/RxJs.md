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


#### Hot vs Cold Observable

- cold 
	- standard observables in rxjs
	- New producer per subscriber
	- Data producer (code inside the subscription function in the observer that generates the values) is created inside the observable & activated only upon subscription
	- Each subscriber gets its own independent execution context , starting from the beginning 
	- Unicast (one to one ) & replayable for every subscriber 
	- Resource intensive e.g in ng multiple http requests if the get method is subscribed multiple times 

- hot
	- Producer outside subscribe
	- Data producer is created outside of the observable & starts emitted immediately or on demand (connect) regardless of the subscription 
	- Multicast - multiple subscribers share the same producer 
	- Late subscribers get only partial data 
	- fromEvent() , subjects etc

- cold observable can be made to hot 
	- share() , shareReplay() - uses ReplaySubject, shareRepeat(n, refCount)

- cold observable 

```
const cold$ = of(1, 2, 3).pipe(
  map(x => {
    console.log('Processing:', x);  // Logs per subscription
    return x * 2;
  })
);

cold$.subscribe(val => console.log('Sub1:', val));  // Processing:1,2,3 → Sub1:2,4,6
cold$.subscribe(val => console.log('Sub2:', val));  // Processing:1,2,3 → Sub2:2,4,6 (re-executes!)
```

- convert cold to hot 

```
const shared$ = of(1, 2, 3).pipe(
  map(x => {
    console.log('Processing:', x);  // Logs only once
    return x * 2;
  }),
  shareReplay(1)  // Multicasts; replays last value to late subs
);

shared$.subscribe(val => console.log('Sub1:', val));  // Processing:1,2,3 → Sub1:2,4,6
shared$.subscribe(val => console.log('Sub2:', val));  // Sub2:6 (last value only)
```

- ng notes 
	- HttpClient observables are cold—always pipe with shareReplay(1) for services used in multiple components to prevent multiple requests. Event emitters (e.g., @Output()) are hot


#### Sharing Observables

#####  Problem
- HttpClient observables are cold  by default - ensures fresh data per request & lazy loading - but in shared scenarios leads to duplicates
- Each subscription creates a new execution of the producer 
- If multiple subscribers (multiple components) both subscribe to the same observable it will trigger multiple http requests to the server
- 

```
Injectable({ providedIn: 'root' }) export class UserService { constructor(private http: HttpClient) {}
	getUsers(): Observable { // Cold observable: No sharing console.log('HTTP Request Initiated'); // Logs per subscription return 
	this.http.get('/api/users'); // Fires on each subscribe 
	} 
}

```

##### Solution
```
@Injectable({ providedIn: 'root' }) export class UserService { constructor(private http: HttpClient) {}

	getUsers(): Observable { // Now shared and replaying console.log('HTTP Request Initiated (only once)'); // Logs only once return 
	this.http.get('/api/users').pipe( shareReplay(1) // Multicasts: One request, replays last value to late subs ); 
	} 
}

```
- first subscription (getUsers() call) triggers http get 
- second subscription gets the cached value 
- when unsubscribed(component is destroyed) - refCount (implicit in shareReplay) keeps the subscription alive as long as any subscriber exists.


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


### Schedulers
- Control Execution Context & timing of tasks within observables
- Determine when (execution inside subscription function) & how notifications (next, error & complete) are delivered to observers
	- observeOn - when
	- subscribeOn - how 
- By default RxJS uses efficient scheduler automatically 
- Explicitly provider scheduler through observeOn & subscribeOn 
- Each RxJS Scheduler maps to the JS execution context 

| JS Execution Context                    | RxJS Scheduler          | Notes |
| --------------------------------------- | ----------------------- | ----- |
| Synchronous                             | null                    |       |
| Queued on current thread                | queueScheduler          |       |
| Microtask Queue - Promise()             | asapScheduler           |       |
| Macrotask Queue - setTimeout()          | asyncScheduler          |       |
| Browser Repaint cycle - Animation Frame | animationFrameScheduler |       |


#### subscribeOn & observeOn

- observeOn(scheduler)
	- controls the scheduler for delivering notifications 
	- it affects the downstream - observer/subscriber 
	- affects the operators in the chain
- subscribeOn(scheduler)
	- controls the scheduler (execution context) for the subscription process 
	- it affects the logic where the observable's creation logic run & propogates upstream
	

```
import { of, delay, asyncScheduler, asapScheduler } from 'rxjs';
import { observeOn, subscribeOn, map } from 'rxjs/operators';

// Simulate heavy upstream work: "Fetch" data with delay (like I/O)
const source$ = of('data1', 'data2', 'data3').pipe(
  delay(1000, asyncScheduler),  // Upstream delay on async scheduler (background)
  map(value => `Processed: ${value}`)  // Some transformation
);

const observable$ = source$.pipe(
  subscribeOn(asyncScheduler),  // Run subscription/upstream (delay + map) on async (background)
  observeOn(asapScheduler)      // Deliver emissions to observer on microtask (after sync, before macrotask)
);

console.log('Before subscribe (main context)');
observable$.subscribe(value => {
  console.log(`Received on ${asapScheduler.now()}: ${value}`);  // Logs after subscribe, non-blocking
});
console.log('After subscribe (main context)');
// Output timing:
// Before subscribe (main context)
// After subscribe (main context)
// [~1s later] Received on [timestamp]: Processed: data1
// [immediate after] Received on [timestamp]: Processed: data2
// [immediate after] Received on [timestamp]: Processed: data3

```

#### RxJs schedulers over JS APIs
- Abstraction & Composability 
- Performance & Browser Specific Optimizations

#### Use Cases
- Return the cached value async 

```
```
```
@Injectable({providedIn: 'root'})
export class MovieService {
  private cache: Map<number, Movie> = new Map<number, Movie>();
  constructor(private readonly http: HttpClient) {}
  // ...
  public getById(id: number): Observable<Movie> {
    if (this.cache.has(id)) {
      return scheduled(of(this.cache.get(id)), asyncScheduler);
    }
    return this.http.get<Movie>(...);
  }

```


- this fixes the zalgo problem
	- zalgo is building a function which could be resolved async/sync without the caller having any clear signal about what happened.

```
function getUsers(callback) {  
	if (cache.users) {  
		callback(null, cache.users); // synchronous callback  
	} else {  
		ajax('/api/users', callback); // asynchronous callback  
	}  
}
```

- zalgo can result in 
	- unexpeced race-conditions
	- makes the function less easy to reason about 


---

## 🧠 Summary
> After reviewing your notes, write a brief summary of the key takeaways in your own words.

- [[https://dev.to/this-is-learning/rxjs-schedulers-2fhl](https://dev.to/this-is-learning/rxjs-schedulers-2fhl)]
- [[https://rxjs.dev/guide/overview](https://rxjs.dev/guide/overview)]

---
