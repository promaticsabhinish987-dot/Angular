# Angular View & Template Reference Concepts

---

## 1. `ng-template` Directive

* `ng-` → Angular directive, requires **CommonModule**.
* `<ng-template>` **does not render** itself (like a hidden div).
* Use `#identifier` to reference it.
* Render dynamically via `ngTemplateOutlet` or `ViewContainerRef`.

**Example: Conditional Rendering**

```html
<div *ngIf="isLogin; else notLogged">
  {{ UserName }}
</div>

<ng-template #notLogged>
  User not logged in
</ng-template>
```

---

### Dynamic Rendering

```html
<ng-template #dynamicTemp>
  User not logged in
</ng-template>

<div #dynamicContainer>
  <!-- Template will be rendered here -->
</div>

<button (click)="loadTemplate()">Load Template</button>
```

```ts
@ViewChild("dynamicTemp") dynTemplate: TemplateRef<any> | undefined;
@ViewChild("dynamicContainer", { read: ViewContainerRef }) dyanContainer: ViewContainerRef | undefined;

loadTemplate() {
  if (this.dynTemplate) {
    this.dyanContainer?.createEmbeddedView(this.dynTemplate);
  }
}
```

**Key Points:**

* Wraps code/UI that **does not display by default**.
* Can **render multiple templates conditionally**.
* `TemplateRef` → represents template data.
* `ViewContainerRef` → placeholder where template is rendered.

---

## 2. `@ViewChild` Decorator

* Access **any element/component/template** in the view.
* Works like `getElementById` but **Angular-friendly**.
* Requires a **template reference variable `#ref`**.
* Access in `ngAfterViewInit()` (after view initialization).

**Example: Access DOM / Template**

```html
<input #username />
```

```ts
@ViewChild('username') input!: ElementRef;

ngAfterViewInit() {
  this.input.nativeElement.focus();
}
```

---

### Access Child Component

```html
<app-popup #popupRef></app-popup>
```

```ts
@ViewChild('popupRef') popup!: PopupComponent;

ngAfterViewInit() {
  this.popup.open();
}
```

**Key Points:**

* Can access **DOM elements**, **components**, or **templates**.
* Combined with `TemplateRef` & `ViewContainerRef`, enables **dynamic rendering**.
* Option `{ static: true }` → available in `ngOnInit`.
* Option `{ static: false }` → available in `ngAfterViewInit` (default).

---

## 3. Template + View Dynamic Rendering Workflow

1. Define template using `<ng-template #temp>`.
2. Define container `<div #container>`.
3. Reference them in TS via `@ViewChild`.
4. Use `createEmbeddedView(template)` to render dynamically.
5. Repeat for multiple templates if needed.

---

### Summary Table

| Feature                | Purpose                       | Usage                   |
| ---------------------- | ----------------------------- | ----------------------- |
| `ng-template`          | Hidden template               | Wrap UI/code            |
| `#ref`                 | Template identifier           | Access via `@ViewChild` |
| `TemplateRef`          | Template type                 | Reference template      |
| `ViewContainerRef`     | Placeholder                   | Render template         |
| `@ViewChild`           | Access DOM/component/template | TS reference            |
| `createEmbeddedView()` | Render template dynamically   | Inside container        |

---

These are not required for all the project , its for specific use.
