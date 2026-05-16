# Rule: Zero RxJS Policy

## Absolute Ban

The following are **completely banned** in this architecture:

```text
BANNED: Observable
BANNED: BehaviorSubject
BANNED: Subject
BANNED: ReplaySubject
BANNED: from()
BANNED: of()
BANNED: fromEvent()
BANNED: interval()
BANNED: timer()
BANNED: .subscribe()
BANNED: .pipe()
BANNED: .tap()
BANNED: .map() (RxJS operators)
BANNED: HttpClient (uses Observable internally)
BANNED: async pipe with Observables
```

## Why

1. Angular 21 Zoneless + Signals makes RxJS unnecessary for state management
2. Signals are simpler, more readable, and zoneless-compatible by design
3. Mixing RxJS and Signals creates cognitive overhead and bugs
4. `fetch()` covers all HTTP needs with async/await

## Migration Map

| RxJS Pattern | Signals Replacement |
|---|---|
| `new BehaviorSubject(val)` | `signal(val)` |
| `subject.next(val)` | `signal.set(val)` |
| `subject.getValue()` | `signal()` |
| `combineLatest([a$, b$])` | `computed(() => [a(), b()])` |
| `a$.pipe(map(x => x.y))` | `computed(() => a().y)` |
| `a$.subscribe(fn)` | `effect(() => fn(a()))` |
| `this.http.get(url)` | `fetch(url)` |
| `async pipe` | Direct signal call in template |
| `takeUntilDestroyed()` | `effect()` auto-cleanup |

## Exception

RxJS is allowed **ONLY** when a third-party library forces it (e.g., some PrimeNG internal APIs).
In this case, convert to signals at the boundary immediately.

```typescript
// If a library returns Observable — convert at the edge
someLibrary.someObservable$.subscribe(val => {
  this.mySignal.set(val); // convert to signal immediately
});
```
