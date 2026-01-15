

---

## **`<ng-container>` in Angular**

`<ng-container>` is an **Angular-specific element** that **does not render in the DOM**. It is often used for:

1. **Conditional rendering** with `*ngIf`.
2. **Rendering templates** using `*ngTemplateOutlet`.

It acts like an "invisible wrapper" in the HTML.

---

### **1️⃣ Using `<ng-container>` for conditional rendering**

```html
<div>
  <!-- Show loader when loading -->
  <ng-container *ngIf="loading">
    <p>Loading...</p>
  </ng-container>

  <!-- Show data when not loading -->
  <ng-container *ngIf="!loading">
    <p>Data has been loaded!</p>
  </ng-container>
</div>
```

**Explanation:**

* `*ngIf="loading"` → only renders content inside when `loading` is `true`.
* `*ngIf="!loading"` → only renders content inside when `loading` is `false`.
* **No extra `<div>` or element** will appear in the DOM; it’s completely invisible.

---

### **2️⃣ Using `<ng-container>` with `<ng-template>`**

Sometimes you want to define a **template** and render it elsewhere.

```html
<!-- Define a template -->
<ng-template #myTemplate>
  <p>This is template data</p>
</ng-template>

<!-- Render the template -->
<ng-container *ngTemplateOutlet="myTemplate"></ng-container>
```

**Explanation:**

* `<ng-template>` → defines a template that is **not rendered** immediately.
* `*ngTemplateOutlet="myTemplate"` → renders the content of the template wherever the `<ng-container>` is used.
* Again, `<ng-container>` itself does **not add extra DOM elements**.

---

✅ **Key points about `<ng-container>`:**

* It **doesn’t render in the DOM**.
* Useful for **structural directives** (`*ngIf`, `*ngFor`, `*ngSwitch`) when you don’t want extra wrapper elements.
* Can be used to **render `<ng-template>`** dynamically with `*ngTemplateOutlet`.

---

