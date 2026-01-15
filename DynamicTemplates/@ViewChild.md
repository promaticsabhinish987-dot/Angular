
---

## **`@ViewChild` in Angular**

`@ViewChild` is a **decorator** used to get a **reference** to:

1. A **DOM element**.
2. A **child component**.
3. A **directive**.

It allows a **parent component** to access **child elements, components, or directives** directly in TypeScript.

---

### **1️⃣ Basic syntax**

```ts
@ViewChild('referenceName') elementRef?: ElementRef;
```

* `'referenceName'` → the template reference variable (e.g., `#myDiv`).
* `elementRef` → gives you access to the underlying DOM element.
* `?` → optional, useful to avoid TypeScript errors if element is not found.
* Usually accessed in **`AfterViewInit`** lifecycle hook because the view must be fully initialized.

---

### **2️⃣ Example: Accessing a DOM element**

```html
<!-- Template -->
<div #myDiv>
  Hello, ViewChild!
</div>
<button (click)="changeColor()">Change Color</button>
```

```ts
// Component
import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';

@Component({
  selector: 'app-demo',
  templateUrl: './demo.component.html',
})
export class DemoComponent implements AfterViewInit {

  @ViewChild('myDiv') myDiv?: ElementRef;

  ngAfterViewInit() {
    console.log(this.myDiv); // Logs the div element
    // You can manipulate DOM directly (not recommended unless necessary)
    this.myDiv!.nativeElement.style.color = 'blue';
  }

  changeColor() {
    this.myDiv!.nativeElement.style.backgroundColor = 'yellow';
  }
}
```

**Key points:**

* `ElementRef.nativeElement` → gives access to the **actual DOM element**.
* DOM manipulation should be **used sparingly** in Angular; prefer **data binding** when possible.

---

### **3️⃣ Accessing a child component**

You can also access **child component methods or properties**.

```ts
// Child component
import { Component } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<p>Child component works!</p>`
})
export class ChildComponent {
  message = "Hello from Child";

  greet() {
    alert('Hello from child component!');
  }
}
```

```html
<!-- Parent template -->
<app-child #childComp></app-child>
<button (click)="callChildMethod()">Call Child Method</button>
<p>Child Message: {{ childMessage }}</p>
```

```ts
// Parent component
import { Component, ViewChild, AfterViewInit } from '@angular/core';
import { ChildComponent } from './child.component';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html'
})
export class ParentComponent implements AfterViewInit {

  @ViewChild('childComp') child?: ChildComponent;
  childMessage = '';

  ngAfterViewInit() {
    this.childMessage = this.child?.message || '';
  }

  callChildMethod() {
    this.child?.greet(); // Calls method of child component
  }
}
```

**Key points:**

* `@ViewChild` allows **access to child component methods and properties**.
* Useful for **popups, modals, forms**, or any component where parent needs control.
* Always access in `AfterViewInit` to ensure the child exists.

---

### ✅ Summary

* `@ViewChild('refName')` → gets **reference to DOM element, component, or directive**.
* Use `ElementRef` for **DOM access**.
* Use the child component reference to **call methods or get data**.
* Access in **`ngAfterViewInit`** lifecycle hook.

---
