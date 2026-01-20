# Router Guards

Interfaces provided by angular which when implemented allow us to control the accessibility of a route based on condition provided in class implementation of that interface.

Route work based on condition.

## 5 types of Guards

1. CanActivate
2. CanActivateChild
3. CanLoad
4. CanDeactivate
5. Resolve


```ts
//High level execution order
1. CanLoad
2. CanActivate
3. CanActivateChild
4. Resolve
5. Component Initialization
6. CanDeactivate (on leaving previous route)
```

```ts
//Execution Order Diagram

[Current Component]
      |
      | CanDeactivate
      v
[Lazy Module?] ----> CanLoad
      |
      v
[Route Access] ----> CanActivate
      |
      v
[Child Routes] ---> CanActivateChild
      |
      v
[Data Fetch] ------> Resolve
      |
      v
[Component Rendered]

```
```ts

canActivate: [AuthGuard, RoleGuard, FeatureGuard]
//left to right execution of guard
```

---

# Router Guards in Angular — First Principles Explanation

---

## 1. The Core Problem (Before Route Guards)

### First Principle

> **A Single Page Application (SPA) changes views without reloading the browser.**

Angular uses the **Router** to switch components when URLs change.

### What went wrong before guards?

Before route guards:

* Any user could manually type a URL and access **any component**
* No control over:

  * Authentication (logged in or not)
  * Authorization (admin vs user)
  * Unsaved data loss
  * Lazy-loaded module protection
  * Data readiness before navigation

### Example Problem

```txt
User not logged in
↓
User types /admin in browser
↓
Admin page loads ❌
```

**This breaks security, UX, and application logic.**

---

## 2. The Solution: Router Guards

### First Principle

> **Navigation is just a function call — we should intercept it and decide whether it should continue.**

Angular introduced **Router Guards** to:

* Intercept navigation
* Run **conditions**
* Decide **allow / block / redirect / delay**

### Cause → Effect

| Cause                  | Effect               |
| ---------------------- | -------------------- |
| User tries to navigate | Guard runs           |
| Condition fails        | Navigation blocked   |
| Condition passes       | Navigation continues |

---

## 3. What Is a Router Guard?

A **Router Guard** is:

* A **class**
* That implements a **guard interface**
* Returns:

  * `true` → allow navigation
  * `false` → block navigation
  * `UrlTree` → redirect
  * `Observable / Promise` → async decision

---

## 4. How Guards Work (Navigation Lifecycle)

```text
User clicks link / enters URL
↓
Router checks guards
↓
Guard logic executes
↓
Decision returned
↓
Route allowed / blocked / redirected
```

---

## 5. Creating a Guard (Step-by-Step)

### Step 1: Generate Guard

```bash
ng generate guard auth
```

Angular creates:

```ts
auth.guard.ts
```

---

### Step 2: Implement Guard Interface

Example:

```ts
@Injectable({ providedIn: 'root' })
export class AuthGuard implements CanActivate {
  canActivate(): boolean {
    return true;
  }
}
```

---

### Step 3: Configure Guard in Routes

```ts
{
  path: 'dashboard',
  component: DashboardComponent,
  canActivate: [AuthGuard]
}
```

---

# 6. The 5 Types of Guards — Deep Explanation

---

## 1️⃣ CanActivate

### Problem It Solves

> “Should this route be accessible at all?”

### When It Runs

* Before navigating to a route

### Typical Use

* Authentication
* Role-based access

---

### Example Scenario

```txt
User tries to access /dashboard
↓
Check if logged in
↓
If yes → allow
If no → redirect to login
```

---

### Code Example

```ts
@Injectable({ providedIn: 'root' })
export class AuthGuard implements CanActivate {
  constructor(private router: Router) {}

  canActivate(): boolean {
    const isLoggedIn = false;

    if (!isLoggedIn) {
      this.router.navigate(['/login']);
      return false;
    }

    return true;
  }
}
```

---

### Importance

* Prevents unauthorized access
* Core security layer

---

## 2️⃣ CanActivateChild

### Problem It Solves

> “Parent route is protected, but what about all its children?”

Without this guard:

* Each child route needs its own `CanActivate`

---

### Example Scenario

```txt
/admin
  ├── /users
  ├── /settings
  └── /reports
```

Instead of repeating guard everywhere, protect all children at once.

---

### Configuration

```ts
{
  path: 'admin',
  component: AdminComponent,
  canActivateChild: [AdminGuard],
  children: [
    { path: 'users', component: UsersComponent },
    { path: 'settings', component: SettingsComponent }
  ]
}
```

---

### Guard Implementation

```ts
export class AdminGuard implements CanActivateChild {
  canActivateChild(): boolean {
    return true; // role check here
  }
}
```

---

### Importance

* Cleaner routing
* Centralized child protection

---

## 3️⃣ CanLoad

### Problem It Solves

> “Why load a module if the user is not allowed?”

### First Principle

Lazy-loaded modules are:

* Downloaded **only when needed**
* But **once loaded**, they stay in memory

### Without CanLoad

```txt
Unauthorized user
↓
Module downloaded
↓
Navigation blocked ❌ (too late)
```

---

### With CanLoad

```txt
Check permission
↓
If allowed → download module
If not → block download
```

---

### Example

```ts
{
  path: 'admin',
  loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
  canLoad: [AdminLoadGuard]
}
```

---

### Guard Code

```ts
export class AdminLoadGuard implements CanLoad {
  canLoad(): boolean {
    return false; // permission check
  }
}
```

---

### Importance

* Performance
* Security
* Prevents unnecessary downloads

---

## 4️⃣ CanDeactivate

### Problem It Solves

> “What if the user leaves with unsaved changes?”

---

### Example Scenario

```txt
User edits form
↓
Clicks back / another route
↓
Data lost ❌
```

---

### Solution

Ask before leaving.

---

### Component Interface

```ts
export interface CanComponentDeactivate {
  canDeactivate(): boolean;
}
```

---

### Component

```ts
export class EditProfileComponent implements CanComponentDeactivate {
  canDeactivate(): boolean {
    return confirm('You have unsaved changes. Leave?');
  }
}
```

---

### Guard

```ts
export class UnsavedGuard implements CanDeactivate<CanComponentDeactivate> {
  canDeactivate(component: CanComponentDeactivate): boolean {
    return component.canDeactivate();
  }
}
```

---

### Importance

* Prevents data loss
* Improves UX

---

## 5️⃣ Resolve

### Problem It Solves

> “Component loads before data arrives → blank UI / loaders everywhere”

---

### Without Resolve

```txt
Component loads
↓
API call starts
↓
UI empty / loading
```

---

### With Resolve

```txt
Route requested
↓
Data fetched
↓
Component loads with data
```

---

### Resolver

```ts
@Injectable({ providedIn: 'root' })
export class UserResolver implements Resolve<any> {
  resolve() {
    return fetchUserFromAPI();
  }
}
```

---

### Route

```ts
{
  path: 'profile',
  component: ProfileComponent,
  resolve: { user: UserResolver }
}
```

---

### Component Access

```ts
constructor(private route: ActivatedRoute) {
  console.log(this.route.snapshot.data['user']);
}
```

---

### Importance

* Cleaner components
* Predictable data availability

---

# 7. Summary Table

| Guard            | Purpose                 | Key Benefit        |
| ---------------- | ----------------------- | ------------------ |
| CanActivate      | Protect route           | Security           |
| CanActivateChild | Protect child routes    | Clean routing      |
| CanLoad          | Prevent module download | Performance        |
| CanDeactivate    | Prevent accidental exit | UX                 |
| Resolve          | Preload data            | Clean architecture |

---

## Final First-Principle Insight

> **Routing is not just navigation — it is a decision-making process.**

Router Guards exist because:

* Navigation has **consequences**
* Conditions must be evaluated **before action**
* Control must be centralized

Angular Router Guards give you:
✔ Security
✔ Performance
✔ Data integrity
✔ Predictable UX

If you want, I can also:

* Compare guards vs interceptors
* Explain guard execution order
* Show real-world architecture diagrams

Note :- Guards control WHERE a user can go.
Interceptors control HOW the app talks to the server. like setting the token in http header.
