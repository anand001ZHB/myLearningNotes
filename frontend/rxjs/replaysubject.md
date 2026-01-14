# RxJS ReplaySubject — Final Understanding

## Definition

- **ReplaySubject**: a special RxJS object that acts as both an event sender and a broadcaster, with **history**.
- It allows one place to emit events and multiple places to listen to those events.
- Unlike Subject and BehaviorSubject, ReplaySubject **replays past values** to new subscribers.
- In simple terms:  
  **ReplaySubject = EVENT broadcaster WITH HISTORY**

> Important: A ReplaySubject stores multiple previously emitted values and replays them to new subscribers based on its configuration.

```ts
import { ReplaySubject } from 'rxjs';

const event$ = new ReplaySubject<number>(2); // buffer size = 2
````

* At this moment: the ReplaySubject is empty but ready to store up to **2 past values**.

```
event$

[  ]   (empty history)
```

---

## How ReplaySubject stores data

```ts
event$.next(1);
event$.next(2);
event$.next(3);
```

Now the ReplaySubject stores the **last 2 values**:

```
event$

[ 2, 3 ]
```

* Older values are dropped automatically
* History size is controlled by **buffer size**

---

## What does `subscribe()` mean?

* `subscribe()` means:

  > "Give me the previously stored values first, then tell me when something NEW happens."

* ReplaySubject **replays past emissions** before listening to new ones.

Example:

```ts
event$.subscribe((value) => {
	console.log('Subscriber received:', value);
});
```

Output immediately:

```
Subscriber received: 2
Subscriber received: 3
```

Then it continues listening for future `.next()` calls.

---

## Visual idea for `subscribe()`

```
ReplaySubject history: [ 2, 3 ]

Component A calls: subscribe()
        ↓
Replays: 2
        ↓
Replays: 3
        ↓
Now listening for future .next()
```

---

## What does `.next()` mean?

* `.next()` means:
  **"A new value happened RIGHT NOW."**
* ReplaySubject:

  * Stores the value in history
  * Removes oldest value if buffer is full
  * Broadcasts the value to all current subscribers

```ts
event$.next(4);
```

History becomes:

```
[ 3, 4 ]
```

---

## Example: shared service + components

Service (single ReplaySubject, shared)

```ts
@Injectable({ providedIn: 'root' })
export class EventService {
	event$ = new ReplaySubject<string>(2);

	triggerEvent(value: string) {
		this.event$.next(value);
	}
}
```

---

## Timeline

```
TIME →

event$.next('E1') ----|
event$.next('E2') ----|   (history = [E1, E2])
A subscribes ----------|
event$.next('E3') ----|   (history = [E2, E3])
B subscribes ----------|
```

---

## Output

**Component A receives:**

* E1 (replayed)
* E2 (replayed)
* E3 (live)

**Component B receives:**

* E2 (replayed)
* E3 (replayed)

---

## Important Behavior Rules

* ReplaySubject **does NOT require an initial value**
* ReplaySubject **replays multiple past values**
* ReplaySubject can be configured with:

  * **buffer size** (how many values)
  * **time window** (how long to keep values)

```ts
const subject$ = new ReplaySubject<number>(2, 5000); // last 2 values within 5 seconds
```

---

## Real-Time Use Cases (DETAILED)

### 1. Application Event History / Logs (Very Common)

**Problem:**

* Logs/events happen continuously
* A debugging panel opens later
* Developer still wants to see **recent events**

**Why ReplaySubject?**

* Subject → misses past logs ❌
* BehaviorSubject → only shows last log ❌
* ReplaySubject → shows recent logs ✅

```ts
@Injectable({ providedIn: 'root' })
export class LogService {
	logs$ = new ReplaySubject<string>(50);

	addLog(message: string) {
		this.logs$.next(message);
	}
}
```

**What happens:**

* App emits logs continuously
* Debug panel opens later
* Panel instantly receives last 50 logs
* Then continues receiving new logs

👉 ReplaySubject acts like a **rolling log buffer**

---

### 2. WebSocket / Live Data Streams

**Problem:**

* WebSocket sends messages continuously
* A component opens late (chat window, dashboard)
* It should still see recent messages

```ts
@Injectable({ providedIn: 'root' })
export class SocketService {
	messages$ = new ReplaySubject<Message>(20);

	onMessage(msg: Message) {
		this.messages$.next(msg);
	}
}
```

**Why ReplaySubject?**

* Late subscribers still see **recent messages**
* No need to refetch data
* Works perfectly for chat, notifications, live feeds

---

### 3. Analytics & User Tracking

**Problem:**

* User actions happen before analytics component loads
* Analytics dashboard still needs past actions

```ts
@Injectable({ providedIn: 'root' })
export class AnalyticsService {
	events$ = new ReplaySubject<AnalyticsEvent>(100);

	track(event: AnalyticsEvent) {
		this.events$.next(event);
	}
}
```

**Result:**

* Dashboard subscribes later
* Still receives last 100 user actions
* Continues tracking live actions

---

### 4. Multi-Step Workflow / Wizard

**Problem:**

* User navigates through steps
* Some steps load late
* Late steps still need earlier data

```ts
@Injectable({ providedIn: 'root' })
export class WizardService {
	steps$ = new ReplaySubject<StepData>(3);

	saveStep(step: StepData) {
		this.steps$.next(step);
	}
}
```

**Why ReplaySubject works:**

* Each step emits its data
* Later steps instantly replay previous step data
* Workflow remains consistent

---

### 5. System Notifications / Alerts

**Problem:**

* Alerts fired before notification panel opens
* User still needs to see recent alerts

```ts
@Injectable({ providedIn: 'root' })
export class NotificationService {
	alerts$ = new ReplaySubject<Alert>(10);

	pushAlert(alert: Alert) {
		this.alerts$.next(alert);
	}
}
```

**Behavior:**

* Notification panel opens
* Instantly receives last 10 alerts
* Then listens for new alerts

---

## Key Differences: Subject vs BehaviorSubject vs ReplaySubject

| Feature                   | Subject | BehaviorSubject | ReplaySubject    |
| ------------------------- | ------- | --------------- | ---------------- |
| Stores data               | ❌ No    | ✅ Latest only   | ✅ Multiple       |
| Initial value required    | ❌ No    | ✅ Yes           | ❌ No             |
| Late subscribers get data | ❌ No    | ✅ Latest        | ✅ History        |
| Memory size               | None    | 1 value         | Configurable     |
| Best for                  | Events  | State           | History / replay |

---

## Final Mental Model

* **Subject** → fire-and-forget event
* **BehaviorSubject** → state with current value
* **ReplaySubject** → event/state with memory of past values

```
ReplaySubject = Subject + History
```

---

## Final Takeaway

👉 Use **ReplaySubject** when:

* Late subscribers MUST know past events
* You need **event history**
* You don’t want to force an initial value