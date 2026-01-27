```ts

1. Change Detection (CPU)
2. Rendering & DOM (UI)
3. Data Flow & State (Memory + CPU)
4. Bundling & Build (Load time)
5. Runtime & Network (User experience)

```

# 1. Change Detection (How angular tracks and updates the data)


## Definition

**Change Detection** is the process by which Angular **detects data changes and updates the UI to keep it in sync with the application state**.

---

## How Angular Detects Changes

* Angular uses **Zone.js** to track async events (clicks, API calls, timers).
* After an async event, Angular runs a **change detection cycle**.
* It checks component templates and updates the DOM where values changed.

> 🔸 **Signals (Angular 16+)** track exact dependencies and **do not need Zone.js**.
> Angular 18 aims to reduce Zone.js usage due to performance overhead.

---

## Detection Internals

* **KeyValueDiffers** → Detect changes in objects/maps
* **IterableDiffers** → Detect changes in arrays (`*ngFor`)

---

## ChangeDetectionStrategy

Each component controls detection using:

```ts
changeDetection: ChangeDetectionStrategy
```

### Types

### 1️⃣ Default

* Runs on every async event
* Checks entire component tree
* Detects all mutations
* ❌ Can be slow in large apps

### 2️⃣ OnPush

* Runs only when:

  * `@Input()` reference changes
  * UI event occurs
  * Observable emits (`async` pipe)
  * Manually triggered
* ❌ Mutation alone does not update UI
* ✅ Best for performance

---

## Manual Change Detection

Used when Angular can’t detect changes automatically (e.g. third-party async).

```ts
constructor(private cdr: ChangeDetectorRef) {}

this.cdr.markForCheck();
```

> Prefer `markForCheck()` over `detectChanges()`.

---

## Signals (Recommended)

* Automatic reactivity
* No Zone.js
* Fine-grained updates
* Best choice for new Angular apps

---

## Best Practices

✅ Use `OnPush` by default
✅ Use immutable data patterns
✅ Prefer `async` pipe
✅ Use signals where possible
❌ Avoid heavy template logic
❌ Avoid object mutation in OnPush

---

```ts
//before
import { Component } from '@angular/core';

@Component({
  selector: 'app-employee',
  template: `
    <p>Employee: {{ employee.name }}</p>
    <button (click)="updateName()">Update</button>
  `
})
export class EmployeeComponent {
  employee = { name: 'John' };

  updateName() {
    // Mutation
    this.employee.name = 'David';
  }
}

```
```ts
//after
import { Component, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-employee',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <p>Employee: {{ employee.name }}</p>
    <button (click)="updateName()">Update</button>
  `
})
export class EmployeeComponent {
  employee = { name: 'John' };

  updateName() {
    // New reference (immutability)
    this.employee = {
      ...this.employee,
      name: 'David'
    };
  }
}


```


## One-Line 

> Angular change detection keeps the UI in sync with data by tracking async events using Zone.js or Signals and updating the DOM using Default or OnPush strategies.

---

# 2. Rendering & DOM (UI)

> **The DOM is expensive.**
> Creating, destroying, and re-checking DOM nodes costs CPU, memory, and time.

Angular performance problems happen when we **force the browser to do unnecessary DOM work**.

---

# 2️⃣ Rendering & DOM Performance

## 2.1 Large Lists Optimization — `trackBy`

---

### 🔍 Root Cause (WHY the problem exists)

Angular’s `*ngFor` **does not know item identity by default**.

It assumes:

> “If the array changed, maybe everything changed.”

So Angular:

* Destroys old DOM nodes
* Creates new DOM nodes
* Re-renders all items

---

### ❌ Cause → Effect (Without `trackBy`)

```html
<li *ngFor="let emp of employees">{{ emp.name }}</li>
```

#### What happens internally:

1. API updates array
2. Angular compares array positions
3. Cannot match old vs new items
4. **Destroys entire list DOM**
5. Recreates all `<li>` elements

#### Effects:

* Slow rendering
* Scroll jumps
* Lost focus
* Poor UX on large lists

---

### ✅ Solution (Tell Angular the Identity)

```html
<li *ngFor="let emp of employees; trackBy: trackById">
```

```ts
trackById(index: number, emp: Employee) {
  return emp.id;
}
```

---

### 💡 Why `trackBy` Works (First Principle)

Angular now knows:

> “This DOM node belongs to Employee #5”

So Angular:

* Reuses DOM nodes
* Updates only changed items
* Keeps scroll & focus intact

---

### ✅ Best Practices for `trackBy`

✔ Always use `trackBy` for lists
✔ Return **unique & stable ID**
❌ Never use `index` unless list order never changes

---

# 2.2 Virtual Scrolling — Huge Lists

---

### 🔍 Root Cause

The browser **cannot efficiently handle thousands of DOM nodes**.

Even if Angular is optimized:

* Browser layout
* Paint
* Memory

…will suffer.

---

### ❌ Cause → Effect (Rendering everything)

```html
<div *ngFor="let emp of employees">
```

#### Effects:

* Slow initial render
* High memory usage
* Janky scrolling
* Mobile devices struggle

---

### ✅ Solution: Virtual Scrolling

```html
<cdk-virtual-scroll-viewport itemSize="50">
  <div *cdkVirtualFor="let emp of employees">
    {{ emp.name }}
  </div>
</cdk-virtual-scroll-viewport>
```

---

### 💡 Why Virtual Scroll Works

Angular renders:

* Only **visible items**
* Plus small buffer

Example:

* Total items: 10,000
* DOM rendered: ~20–40 items

> Same data, **90% less DOM**

---

### ✅ Best Practices for Virtual Scroll

✔ Use for lists > 100 items
✔ Combine with `trackBy`
✔ Fixed item height for best performance
❌ Avoid complex nested DOM inside rows

---

# 2.3 Avoid Heavy Template Logic

---

### 🔍 Root Cause

Angular templates are **not static**.

Every time change detection runs:

* All expressions in templates are re-evaluated

---

### ❌ Cause → Effect (Function in Template)

```html
{{ calculateTotal() }}
```

#### What happens:

* Click
* Timer
* HTTP response
* ANY async event

➡ `calculateTotal()` runs again
➡ Even if data didn’t change

---

### ⚠️ Effects

* Repeated expensive calculations
* Slower UI
* Hard-to-debug performance drops

---

### ✅ Solution: Precompute Once

```ts
total = this.calculateTotalOnce();
```

```html
{{ total }}
```

---

### 💡 First Principle Rule

> **Templates should display values, not compute them**

---

### ✅ Best Practices for Templates

✔ Keep templates declarative
✔ Move logic to component/service
✔ Use pure pipes for transformations
✔ Avoid method calls in templates

---

# 🧠 Cause → Effect → Solution (One Frame)

```
Problem                Cause                         Solution
------------------------------------------------------------
Large list slow         DOM recreated                 trackBy
Huge dataset            Too many DOM nodes            Virtual Scroll
Template slow           Functions run every CD        Precompute / pipes
```

---

# 🧠 Senior Engineer Mental Checklist

Before writing UI code, ask:

* Will this recreate DOM?
* Will this run every change detection?
* Is the browser doing unnecessary work?
* Can identity be preserved?
* Can rendering be reduced?

---

## 🔑 Golden Rule (Remember This)

> **Angular performance is about doing LESS work, not faster work.**

---


```ts
//common bug and solution

Problem                         Solution
-----------------------------------------
Memory leak                     async pipe / unsubscribe
List re-render                  trackBy
Huge data                       Virtual Scroll + pagination
Slow typing                     debounce + OnPush
Multiple API calls              shareReplay
Slow first load                 Lazy loading
Idle CPU usage                  Clear timers
Template slowness               No functions in template

```

#### Note :- Performance problems happen when Angular does too much work, too often, for no user-visible benefit.


















