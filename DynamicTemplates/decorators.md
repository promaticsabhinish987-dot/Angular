# Decorator



---

## **What is a Decorator in Angular?**

A **decorator** is a **special function** that **adds metadata** to a class, property, method, or parameter.

* It tells Angular **“what this thing is and how to use it”**.
* Example: `@Component` tells Angular **this class is a component** with a template, styles, and selector.

In short: **decorators give Angular instructions about your code.**

---

## **Types of Angular Decorators**

### **1️⃣ Class Decorators**

* Applied **on top of a class**.
* Tell Angular **what the class represents** (component, service, pipe, directive, or module).

**Examples:**

```ts
import { Component, Injectable, Directive, Pipe, NgModule } from '@angular/core';

// Component decorator
@Component({
  selector: 'app-demo',
  template: `<p>Hello, Component!</p>`
})
export class DemoComponent {}

// Service decorator
@Injectable({
  providedIn: 'root'
})
export class DemoService {}

// Directive decorator
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {}

// Pipe decorator
@Pipe({
  name: 'customPipe'
})
export class CustomPipe {}

// Module decorator
@NgModule({
  declarations: [DemoComponent, HighlightDirective, CustomPipe],
  imports: [],
  providers: [DemoService],
})
export class DemoModule {}
```

---

### **2️⃣ Property Decorators**

* Applied **on a class property (variable)**.
* Used for **data binding, communication, or special Angular features**.

**Examples:**

```ts
import { Component, Input, Output, EventEmitter, HostBinding } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<p>Child Component</p>`
})
export class ChildComponent {
  @Input() dataFromParent!: string;    // Property to receive data
  @Output() notify = new EventEmitter<string>();  // Property to emit events
  @HostBinding('class.active') isActive = true;   // Bind class dynamically
}
```

---

### **3️⃣ Method Decorators**

* Applied **on a method**.
* Used for **listening to events or applying behavior**.

**Example:**

```ts
import { Directive, HostListener } from '@angular/core';

@Directive({
  selector: '[appClick]'
})
export class ClickDirective {

  @HostListener('click', ['$event'])
  onClick(event: Event) {
    console.log('Element clicked!', event);
  }
}
```

* `@HostListener` → decorator for **listening to DOM events**.

---

### **4️⃣ Parameter Decorators**

* Applied **on a constructor parameter**.
* Mainly used for **dependency injection** in Angular.

**Example:**

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  constructor(private http: HttpClient) {} // HttpClient is injected via parameter decorator
}
```

* Angular automatically injects dependencies based on type information.

---

### ✅ **Summary Table**

| Decorator Type | Where Used           | Example Usage                               | Purpose                                    |
| -------------- | -------------------- | ------------------------------------------- | ------------------------------------------ |
| **Class**      | On class             | `@Component`, `@Injectable`                 | Define type of class (component, service…) |
| **Property**   | On variables         | `@Input()`, `@Output()`, `@HostBinding()`   | Bind data, emit events, bind element props |
| **Method**     | On methods           | `@HostListener()`                           | Listen to events or extend method behavior |
| **Parameter**  | On constructor param | `constructor(private service: DemoService)` | Inject dependencies                        |

---

Decorators are **like labels for Angular** that explain **what the class, property, method, or parameter is for**. Without them, Angular would not know how to use your code.

1. class decorator => component , injectable (inservice),pipe , directive,module; class decorator are the decorator that we implement in class
2. property decorator(variable) => @Input(), @Output, @hostBining
3. method decorator => decorator that we use just above the method : @hostListener
4. parameter decorator => inject


What is decorator?
whatever we can create in angular , decorator tells angular its purpose., like piple , component 


