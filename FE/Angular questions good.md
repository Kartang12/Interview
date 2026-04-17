### **Directives & Components**
*   **What are Directives and what types?** Classes that add behavior to elements. Three types: **Components** (directives with templates), **Attribute directives** (modify appearance/behavior, e.g., `ngClass`), and **Structural directives** (modify DOM layout, e.g., `ngIf`).
*   **Distinct attribute/structural?** Structural directives use an asterisk prefix (`*ngIf`), which is syntactic sugar for `<ng-template>`. Attribute directives look like standard HTML attributes (usually wrapped in brackets `[ngClass]`).
*   **Is a component a directive?** Yes. Technically, the `@Component` decorator extends `@Directive`. It is a directive with a template.

### **Lifecycle Hooks**
*   **Lifecycle hooks purpose:** Functions called by Angular as it creates, updates, and destroys a component to allow logic at specific moments.
*   **ngOnInit vs ngOnChanges:** `ngOnChanges` is called **first**. It fires whenever data-bound input properties change; `ngOnInit` fires once after the first `ngOnChanges`.
*   **What to do in the constructor?** Only Dependency Injection (DI) and basic variable initialization. 
*   **Why no business logic in constructor?** The component isn't fully "live" yet—Input properties aren't available, and the DOM hasn't been rendered. It also makes unit testing difficult.
*   **ngAfterViewInit:** Called after Angular initializes the component's views and child views. Logic available: Accessing DOM elements or child components via `@ViewChild`.

### **Subscribing & Performance**
*   **Managing unsubscriptions:** 
    *   `Async` pipe (auto-unsubscribes).
    *   `takeUntil(notifier$)` pattern.
    *   `take(1)` for one-time fetches.
    *   `DestroyRef` (the modern replacement for `ngOnDestroy`).
*   **Why "track by id" in ngFor?** By default, `ngFor` re-renders the whole list if the array reference changes. `trackBy` tells Angular to only re-render the specific DOM element that matches the unique ID, drastically improving performance.

### **DOM & View Encapsulation**
*   **View Encapsulation:** A mechanism to scope CSS styles to a specific component so they don't "leak" to the rest of the app.
*   **Shadow DOM:** A browser-native web standard that provides a way to attach a hidden, separated DOM to an element.
*   **Shadow vs. Emulated DOM:** `Shadow` uses the browser's native API. `Emulated` (default) fakes this by adding unique attributes (like `_ngcontent-c1`) to HTML and CSS selectors to scope them manually.

### **Change Detection**
*   **Strategies:** `Default` (checks every component on every event) and `OnPush` (only checks if Inputs change by reference, an event originates from the component, or a Signal updates).
*   **What triggers it?** Anything that happens inside `NgZone`: DOM events (click), timers (`setTimeout`), and AJAX calls.
*   **MarkForCheck vs DetectChanges:** `markForCheck()` flags the component and its ancestors to be checked in the *next* cycle. `detectChanges()` triggers an *immediate* change detection run for the component and its children.

### **Dependency Injection (DI)**
*   **What is Lazy Loading?** A technique where a feature module or component is only downloaded and loaded when the user navigates to its route, reducing initial load time.
*   **How DI works?** It’s a hierarchical system. A component requests a dependency; the Injector looks at the component's providers, then bubbles up to the parent injector, and finally to the root injector.
*   **Lazy loaded + providedIn: root:** Even if the service is defined in a lazy module, `providedIn: 'root'` makes it a global singleton. It is registered in the root injector when the module loads.
*   **Who throws the exception?** The **Injector** (it throws a `NullInjectorError` if a provider isn't found).
*   **Component interaction (built-in):** `@Input()` / `@Output()`, `@ViewChild()` / `@ContentChild()`, and shared services.
*   **Root vs. Component provider:** If you provide a service at the component level that is also `providedIn: 'root'`, the component (and its children) gets a **new, private instance**, while the rest of the app uses the root singleton.

### **State Management & NgRx**
*   **NgRx Lifecycle:** **Action** (dispatched) -> **Effect** (side effect/API) -> **Reducer** (updates state) -> **Store** (state holder) -> **Selector** (component consumes data).
*   **When to use state management?** When the app has many components sharing the same data, complex data flows, or if the "source of truth" is hard to track across routes.

### **Pipes & Templates**
*   **Pure vs Impure Pipes:** `Pure` pipes only re-run when the input reference changes (efficient). `Impure` pipes run on every single change detection cycle (potentially slow).
*   **Methods in templates?** Avoid them. They execute on every change detection cycle, which can kill performance.
*   **Optimizing pure methods:** Wrap them in a **Pure Pipe** or use **Signals**, as these only re-evaluate when specific dependencies change.

### **RxJS**
*   **What is RxJS?** A library for reactive programming using Observables. Angular uses it to handle asynchronous operations (HTTP, Forms, Routing).
*   **Main parts:** Observable, Observer, Subscription, Operators, Subject, and Schedulers.
*   **Observable vs Observer:** The **Observable** is the producer/stream of data; the **Observer** is the object that "listens" and reacts (`next`, `error`, `complete`).
*   **Hot vs Cold:** **Cold** starts a new execution for every subscriber (like a movie on Netflix). **Hot** shares the same execution regardless of subscribers (like a live TV broadcast).
*   **Subscribe twice to Cold?** You get two independent executions (e.g., two separate HTTP calls).
*   **Make Cold Hot?** Use operators like `share()` or `shareReplay()`, or use a `Subject`.
*   **RxJS Schedulers:** They control the execution context and timing of when a subscription starts and when notifications are delivered (e.g., `asyncScheduler`).
*   **Unsubscribe to everything?** Not strictly. `HttpClient` and `finite` observables (like `timer(1)`) complete themselves. You must unsubscribe from `infinite` observables (DOM events, Subjects, Intervals).

### **HTTP & Interceptors**
*   **HttpClient vs Fetch:** `HttpClient` provides RxJS streams, interceptors, typed response objects, and built-in error handling.
*   **Interceptors:** Middleware that sits between the app and the backend. It can transform outgoing requests (add Auth headers) or incoming responses.
*   **Design Pattern:** **Chain of Responsibility**.
*   **Multiple Interceptors:** They execute in the order they are provided in the `providers` array. Request: 1 -> 2 -> 3. Response: 3 -> 2 -> 1.

### **Optimization Techniques**
*   Using `OnPush` Change Detection.
*   Using `trackBy` for lists.
*   Lazy loading routes and components.
*   Using `Pure Pipes` instead of methods in templates.
*   Image optimization (Angular `NgOptimizedImage`).
*   Tree-shaking (removing unused code via `providedIn: 'root'`).
*   Using Signals for fine-grained reactivity.