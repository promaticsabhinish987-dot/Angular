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
























