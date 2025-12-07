# 📚 Topic: [IRxJs operators]
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

### Operators
- Operators are functions 
- composability

#### Pipeable Operators 

``observableInstance.pipe(operator)


- Pure function that takes an observable as input & returns another observable
- Pipeable operators do not change the existing Observable instance 
- They return a new Observable whose subscription logic is based on the 1st observable

#### Pipeable Operator Factory 
``
```observableInstance.pipe(operatorFactory())`

- function that take parameters to set the context & return a pipeable operator, the arguments belong to the operators lexical scope 

#### Higher Order Observables
- Pperators like map can transform emissions into an observable
- HOO - emits other observables as its value  rather than plain values like numbers , strings, objects etc 
- These inner observables can emit values over time , creating an observables of observables structure 
- We can't consume a HOO directly - subscribing would give the observable not the data 
- Use flattening operators to merge emissions &  get the data 


#### Flattening operators

``mergeMap/flatMap , switchMap , concatMap , exhaustMap 


| Operator       | Behavior                                                         | Concurrency                           | Cancellation               | Use Case Example                                                                                                 |
| -------------- | ---------------------------------------------------------------- | ------------------------------------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **mergeMap**   | Flattens all inner obs in parallel; merges their emissions.      | Yes (unlimited)                       | No (all run to completion) | Multiple independent API calls from a list.                                                                      |
| **switchMap**  | Flattens and switches to the newest inner obs; cancels old ones. | One at a time (latest wins)           | Yes (on new emission)      | Typeahead search: Cancel old requests on new keystroke.                                                          |
| **concatMap**  | Flattens sequentially; waits for each inner to finish.           | One at a time (in order)              | No                         | Ordered processing, like saving form steps. Sequential API Calls to submit multi stepped form - Guaranteed Order |
| **exhaustMap** | Flattens only if no active inner; ignores new until done.        | One at a time (ignores during active) | No                         | Button clicks: Ignore rapid clicks until first request completes. eg Prevent Multiple form submissions           |
|                |                                                                  |                                       |                            |                                                                                                                  |

- switchMap eg Typeahead search 

	- Component 

  ```
  const input = this.el.nativeElement.querySelector('#searchInput'); 
  this.results$ = fromEvent(input, 'input').pipe( 
  map((event: any) => event.target.value), // Extract query string 
  debounceTime(300), // Wait 300ms after typing stops 
  distinctUntilChanged(), // Avoid duplicate queries 
  switchMap(query => this.searchService.search(query)) // Flatten: Map to inner HTTP obs, switch/cancel old );
    
  ```

	- SearchService

```
 search(query: string): Observable { 
  if (!query || query.length < 3) return EMPTY; // No search for short queries 
  return this.http.get(`/api/search?q=${encodeURIComponent(query)}`).pipe( 
  shareReplay(1) // Share if needed, but switchMap handles per-query ); 
  }
```

- Template 

```
<li *ngFor="let result of results$ | async">{{ result }}</li>
```

- exhaustMap eg Double Form Submission

	- Service
```
const saveToServer = (formData) => { 
	console.log(`Saving: ${formData}... (simulating 2s delay)`); 
	return of({ success: true, data: formData }).pipe( 
	// Simulate async delay/variability 
	delay(2000 + Math.random() * 1000) // 2-3s ); 
};

```


- Component

```
const submitButton = document.getElementById('submit'); 
const formDataExtractor = () => 'Current Form Data'; // e.g., from form.value

fromEvent(submitButton, 'click').pipe( 
	tap(() => console.log('Button clicked!')), // Log every click 
	exhaustMap(() => saveToServer(formDataExtractor())) // Map to inner obs; ignore source until inner completes 
).subscribe( 
	result => console.log('Save success:', result), 
	error => console.error('Save error:', error) 
);

```


- Output

```
// Simulation: Click 3 times quickly (at t=0, 100ms, 200ms) 
// Output: // Button clicked! (t=0) 
// Saving: Current Form Data... (t=0) 
// Button clicked! (t=100ms) // Logged, but ignored (inner active) 
// Button clicked! (t=200ms) // Logged, but ignored (inner active) 
// Save success: {success: true, data: "Current Form Data"} (t~2500ms) 
// Now a 4th click at t=3000ms would trigger a new save

```

- concatMap eg MultiStep form submission

	- Service

```
/ Simulate saving a form step (e.g., POST /api/step)

saveStep(stepData: string): Observable<{ success: boolean; step: string }> {
	console.log(`Initiating save for: ${stepData}`);
	return of({ success: true, step: stepData }).pipe(
	delay(1000 + Math.random() * 1000) // Simulate variable 1-2s network delay
	);
}

```


- Component 

```

const formSteps = ['Personal Info', 'Address', 'Preferences'];

// Create a source observable from the steps
from(formSteps).pipe(
	tap(step => console.log(`Processing step: ${step}`)),
	concatMap(step => this.formService.saveStep(step)) // Map to inner obs, process sequentially
).subscribe({
	next: result => {
		this.results.push(`Saved: ${result.step}`);
		console.log(`Completed: ${result.step}`);
	},
	error: err => console.error('Error:', err),
		complete: () => console.log('All steps saved!')
	}
);

```

---

## 🧠 Summary
> After reviewing your notes, write a brief summary of the key takeaways in your own words.

- [[https://rxjs.dev/guide/operators](https://rxjs.dev/guide/operators)]

---
