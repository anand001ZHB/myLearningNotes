# RxJS BehaviorSubject — Final Understanding

## Definition

- **BehaviorSubject**: a special RxJS object that acts as both an event sender and a broadcaster, with memory.
- It allows one place to emit events and multiple places to listen to those events simultaneously.
- In simple terms: **BehaviorSubject = EVENT broadcaster WITH MEMORY**.

> Important: A BehaviorSubject ALWAYS stores the current/latest value. It has memory and always remembers the last emitted value.

```ts
import { BehaviorSubject } from 'rxjs';

const userState$ = new BehaviorSubject<User>({ id: 1, name: 'John' });
```

- At this moment: the BehaviorSubject holds the initial value `{ id: 1, name: 'John' }`.

```
userState$

[  { id: 1, name: 'John' }  ]
```

## What does `subscribe()` mean?

- `subscribe()` means: "Give me the CURRENT value immediately, then tell me when something NEW happens."
- Unlike Subject, BehaviorSubject ALWAYS emits the last value to new subscribers.

Example:

```ts
userState$.subscribe((user) => {
	console.log('User:', user);
});

// Output immediately:
// User: { id: 1, name: 'John' }
```

Visual idea:

```
userState$ has current value: { id: 1, name: 'John' }

Component A calls: subscribe()
        ↓
    IMMEDIATE EMISSION of current value
        ↓
Component A receives: { id: 1, name: 'John' }
        ↓
Now listening for future .next() calls
```

## What does `.next()` mean?

- `.next()` means: "A new value happened RIGHT NOW."
- The BehaviorSubject stores this as the new current value and broadcasts it to all current subscribers and to any future subscribers.

```ts
userState$.next({ id: 2, name: 'Jane' });
```

## Example: shared service + components

Service (single BehaviorSubject, shared)

```ts
@Injectable({ providedIn: 'root' })
export class UserService {
	user$ = new BehaviorSubject<User | null>(null);

	setUser(user: User) {
		this.user$.next(user);
	}
}
```

Component A (subscribes first)

```ts
ngOnInit() {
	this.userService.user$.subscribe((user) => {
		console.log('A received:', user);
	});
}
```

Component B (subscribes after .next() was called)

```ts
ngOnInit() {
	// Even though B subscribes later, it gets the latest value
	this.userService.user$.subscribe((user) => {
		console.log('B received:', user);
	});
}
```

Timeline:

```
TIME →

Initial state: user$ = null
A subscribes ----|
                 |---- .next({ id: 1, name: 'John' }) ----|
                 |                                          |
                 |---- B subscribes ----|
```

Output:

- A received: null (immediately on subscribe)
- A received: { id: 1, name: 'John' } (after .next())
- B received: { id: 1, name: 'John' } (immediately on subscribe, gets the latest value)

## Understanding `@Injectable({ providedIn: 'root' })`

Before diving into use cases, let's understand the decorator used in the examples:

```ts
@Injectable({ providedIn: 'root' })
export class AuthService { ... }
```

### What does it mean?

- **`@Injectable()`** - Marks this class as a **Service** that can be injected into components
- **`providedIn: 'root'`** - Creates a **single instance** (singleton) at the app root level and shares it everywhere

### Visual representation:

```
Application Root
    ↓
  AuthService (one instance created here)
    ↓
  ├── Component A (gets reference to the same instance)
  ├── Component B (gets reference to the same instance)
  ├── Component C (gets reference to the same instance)
  └── Component D (gets reference to the same instance)

Result: All components access the SAME BehaviorSubject
        State stays in sync across the entire app
```

### Why is this important?

Without `providedIn: 'root'`, each component would get its own separate instance of the service with separate BehaviorSubjects, and state would NOT be shared.

With `providedIn: 'root'`, all components share one instance, so:
- ✅ State is centralized
- ✅ All components see the same data
- ✅ Changes propagate to all subscribers
- ✅ Single source of truth

## Real-Time Use Cases

### 1. Authentication / User Login State

```ts
@Injectable({ providedIn: 'root' })
export class AuthService {
	isLoggedIn$ = new BehaviorSubject<boolean>(false);
	currentUser$ = new BehaviorSubject<User | null>(null);

	login(credentials: any) {
		// API call...
		const user = { id: 1, email: 'user@example.com' };
		this.currentUser$.next(user);
		this.isLoggedIn$.next(true);
	}

	logout() {
		this.currentUser$.next(null);
		this.isLoggedIn$.next(false);
	}
}

// In Component:
export class NavbarComponent implements OnInit {
	ngOnInit() {
		this.authService.isLoggedIn$.subscribe((isLoggedIn) => {
			// Show/hide login button based on current state
			this.showLoginButton = !isLoggedIn;
		});
	}
}
```

### 2. Theme Switching (Dark/Light Mode)

```ts
@Injectable({ providedIn: 'root' })
export class ThemeService {
	theme$ = new BehaviorSubject<'light' | 'dark'>('light');

	toggleTheme() {
		const currentTheme = this.theme$.value;
		const newTheme = currentTheme === 'light' ? 'dark' : 'light';
		this.theme$.next(newTheme);
	}
}

// In Multiple Components:
// Header subscribes and gets current theme immediately
// Footer subscribes and gets current theme immediately
// All receive updates when theme is toggled
```

### 3. Shopping Cart State

```ts
@Injectable({ providedIn: 'root' })
export class CartService {
	cart$ = new BehaviorSubject<CartItem[]>([]);
	cartCount$ = new BehaviorSubject<number>(0);

	addToCart(item: CartItem) {
		const currentCart = this.cart$.value;
		const updatedCart = [...currentCart, item];
		this.cart$.next(updatedCart);
		this.cartCount$.next(updatedCart.length);
	}

	removeFromCart(itemId: number) {
		const currentCart = this.cart$.value;
		const updatedCart = currentCart.filter(item => item.id !== itemId);
		this.cart$.next(updatedCart);
		this.cartCount$.next(updatedCart.length);
	}
}

// In Header Component (cart badge):
// Shows item count immediately when component loads
// Updates whenever item is added/removed

// In Cart Page:
// Shows all items immediately when cart page opens
```

### 4. Form State Management

```ts
@Injectable()
export class FormStateService {
	formData$ = new BehaviorSubject<FormData>({
		name: '',
		email: '',
		phone: ''
	});

	updateField(field: string, value: any) {
		const current = this.formData$.value;
		this.formData$.next({ ...current, [field]: value });
	}
}

// Multiple form steps/sections can subscribe to same state
// Each gets current data immediately
// All stay in sync as data changes
```

### 5. Notification Badge Count

```ts
@Injectable({ providedIn: 'root' })
export class NotificationService {
	unreadCount$ = new BehaviorSubject<number>(0);

	fetchUnreadCount() {
		// API call...
		this.unreadCount$.next(5);
	}

	markAsRead() {
		const current = this.unreadCount$.value;
		this.unreadCount$.next(current - 1);
	}
}

// In navbar (shows current count immediately)
// In notification dropdown (shows current count immediately)
// Both update in real-time
```

## Key Differences from Subject

| Feature | Subject | BehaviorSubject |
|---------|---------|-----------------|
| Has memory | No | Yes |
| Initial value required | No | Yes |
| Late subscribers get data | No | Yes, gets the latest value |
| Use case | Events/signals | State management |

