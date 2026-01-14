# RxJS Subject — Final Understanding

## Definition

- **Subject**: a special RxJS object that acts as both an event sender and a broadcaster.
- It allows one place to emit events and multiple places to listen to those events simultaneously.
- In simple terms: **Subject = EVENT broadcaster**.

> Important: A Subject does NOT store data. It has no memory, no history, and no initial value.

```ts
import { Subject } from 'rxjs';

const refresh$ = new Subject<void>();
```

- At this moment: the Subject is empty — no value, no memory.

```
refresh$

[  empty  ]
```

## What does `subscribe()` mean?

- `subscribe()` means: “Tell me when something NEW happens from now on.”
- It does NOT mean: give me old data or current state.

Example:

```ts
refresh$.subscribe(() => {
	console.log('Component A refreshed');
});
```

Visual idea:

Component A
		|
		|  subscribe()
		v
refresh$   (listening started)

## What does `.next()` mean?

- `.next()` means: “An event happened RIGHT NOW.”
- The Subject immediately broadcasts that event only to subscribers that are already listening at that moment.

```ts
refresh$.next();
```

## Example: shared service + components

Service (single Subject, shared)

```ts
@Injectable({ providedIn: 'root' })
export class RefreshService {
	refresh$ = new Subject<void>();

	triggerRefresh() {
		this.refresh$.next();
	}
}
```

Component A

```ts
ngOnInit() {
	this.refreshService.refresh$.subscribe(() => {
		console.log('A refreshed');
	});
}
```

Component B

```ts
ngOnInit() {
	this.refreshService.refresh$.subscribe(() => {
		console.log('B refreshed');
	});
}
```

Timeline (both subscribed before `.next()`):

TIME →

A subscribes ----|  B subscribes ----|---- .next() ---->

Output:

- A refreshed
- B refreshed

Both receive the event because both were subscribed before `.next()`.

## Who calls `.next()`?

- It doesn't matter who calls `.next()` — Subject only cares who is listening.

If Component B triggers the refresh, both A and B still receive it.

## Late subscriber

- A late subscriber subscribes after `.next()` already happened.
- Because Subject has no memory, late subscribers do NOT receive past events.

Example:

Component C loads later and subscribes:

```ts
ngOnInit() {
	this.refreshService.refresh$.subscribe(() => {
		console.log('C refreshed');
	});
}
```

Timeline:

A subscribes ----|  B subscribes ----|---- .next() ----|  C subscribes

Output:

- A refreshed
- B refreshed
- C gets nothing (missed the past event)

If C later triggers `.next()`, everyone subscribed at that time (A, B, C) will receive the new event.

## Analogy

- Subject is like a doorbell: it rings now — people inside hear it; if you arrive later, you missed that ring. The doorbell does NOT record past rings.

## Where does a Subject get data?

- A Subject NEVER fetches data by itself. Data only comes via explicit `.next(...)` calls from sources such as:
	- button clicks
	- API responses
	- timers
	- WebSockets
	- user actions

```ts
subject.next(data);
```

## Correct uses (events)

- Button click events
- Refresh triggers
- Logout click
- Modal close
- Notification signals

These are EVENTS — Subject fits them well.

## Wrong uses (state)

- Logged-in user
- Theme (dark / light)
- Language
- Permissions

These are STATE and must be available to late subscribers. Use `BehaviorSubject`, `ReplaySubject`, or other state solutions instead.

## Naming convention

- The trailing dollar sign (e.g., `refresh$`) is a recommended convention for observables/streams, but not mandatory. It helps indicate the variable is a stream.

## One-line rule

- Subject = EVENT broadcaster — no memory, no state.

## Interview-ready conclusion

- Subject multicasts events to all active subscribers.
- It does not store values; late subscribers do not receive past events.
- Best suited for event-based communication.

---
