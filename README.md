# Angular

```scss
Angular Core
├── Modules & Components
│   ├── NgModule
│   ├── Component Decorator
│   └── Lifecycle Hooks
│       ├── ngOnInit
│       ├── ngAfterViewInit  ← @ViewChild ready here
│       └── ngAfterViewChecked
├── Templates & Data Binding
│   ├── Interpolation {{ }}
│   ├── Property Binding [prop]
│   ├── Event Binding (event)
│   ├── Two-way Binding [(ngModel)]
│   └── Template Reference Variables (#ref)
│       └── Used with @ViewChild
├── Directives
│   ├── Structural (*ngIf, *ngFor, *ngSwitch)
│   └── Attribute (ngClass, ngStyle)
├── Component Interaction
│   ├── @Input() → Parent → Child data
│   ├── @Output() → Child → Parent events
│   ├── ViewChild / ViewChildren → Parent → Child methods/refs
│   └── ContentChild / ContentChildren → access projected content
├── Pipes
│   ├── Built-in (date, currency, json, async)
│   └── Custom Pipes
├── Dynamic Templates
│   ├── ng-template
│   ├── ng-container
│   └── ngTemplateOutlet → often used with @ViewChild for dynamic access
├── Forms
│   ├── Template-driven
│   └── Reactive Forms
├── Routing & Navigation
├── HTTP / Services
└── Advanced
    ├── Dependency Injection
    ├── RxJS Observables
    └── Change Detection

```
