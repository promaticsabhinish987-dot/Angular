---

# Observable — Simple Notes (Angular)

---

## 1️⃣ What Is an Observable? (In One Line)

> **An Observable represents a stream of values that arrive over time and can be subscribed to.**

---

## 2️⃣ Basic Observable Example (Custom)

### Creating an Observable

```ts
import { Observable } from 'rxjs';

const number$ = new Observable<number>(observer => {
  observer.next(1);
  observer.next(2);
  observer.next(3);
  observer.complete();
});
```

### Subscribing to It

```ts
number$.subscribe({
  next: value => console.log(value),
  complete: () => console.log('Done')
});
```

### Output

```
1
2
3
Done
```

---

## 3️⃣ Native Angular Use of Observable (HttpClient)

Angular **natively returns Observables** from HTTP calls.

### Service

```ts
import { HttpClient } from '@angular/common/http';

constructor(private http: HttpClient) {}

getUsers() {
  return this.http.get('https://api.example.com/users');
}
```

### Component

```ts
this.getUsers().subscribe(users => {
  console.log(users);
});
```

📌 **Important**:
No HTTP request happens until `.subscribe()` is called.

---

## 4️⃣ Observable with Async Pipe (Best Practice)

```html
<ul>
  <li *ngFor="let user of users$ | async">
    {{ user.name }}
  </li>
</ul>
```

✔ Automatically subscribes
✔ Automatically unsubscribes
✔ Triggers change detection

---

## 5️⃣ Little Bit About Internal Working (Short & Clear)

### Internal Flow

```
Observable created (lazy)
 ↓
Subscribe called
 ↓
Execution starts
 ↓
Value emitted (next)
 ↓
Zone.js notified
 ↓
Change Detection runs
 ↓
UI updates
```

---

## 6️⃣ Key Internal Facts (Quick Points)

* Observables are **lazy** → run only on subscribe
* Angular listens to async tasks using **Zone.js**
* Emissions trigger **change detection**
* `async` pipe manages subscription lifecycle

---

## 7️⃣ Why Angular Uses Observables (In Short)

✔ Multiple values over time
✔ Cancelable
✔ Powerful operators
✔ Reactive UI updates

---

## 8️⃣ One-Line Interview Answer

> **Observables provide a lazy, cancelable stream of values and form the backbone of Angular’s reactive architecture.**

---

If you want, I can:

* Add operators (`map`, `switchMap`)
* Show cold vs hot with example
* Convert this into a PDF cheat sheet
* Compare with Promise using same example
