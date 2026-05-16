# Anti-Pattern: RxJS in a Signals World

## The Problem

Mixing RxJS with Angular 21 Signals creates:
- Cognitive overhead (two reactivity systems)
- Memory leaks (forgot to unsubscribe)
- Zoneless incompatibility (some RxJS operators trigger zone)
- Harder debugging

## Common Violations

### ❌ BehaviorSubject for State

```typescript
// WRONG
private dataSubject = new BehaviorSubject<Item[]>([]);
readonly data$ = this.dataSubject.asObservable();

// RIGHT
readonly data = signal<Item[]>([]);
```

### ❌ subscribe() for Reactions

```typescript
// WRONG
constructor() {
  this.service.data$.subscribe(data => {
    this.localData = data;
  });
}

// RIGHT
constructor() {
  effect(() => {
    this.localData = this.service.data();
  });
}
```

### ❌ HttpClient

```typescript
// WRONG
constructor(private http: HttpClient) {}

loadData() {
  this.http.get('/api/data').subscribe(data => {
    this.data.set(data as any);
  });
}

// RIGHT
async loadData(): Promise<void> {
  const res = await fetch('/api/data');
  this.data.set(await res.json());
}
```

### ❌ combineLatest / map operators

```typescript
// WRONG
readonly combined$ = combineLatest([this.a$, this.b$]).pipe(
  map(([a, b]) => ({ a, b }))
);

// RIGHT
readonly combined = computed(() => ({
  a: this.a(),
  b: this.b()
}));
```

### ❌ async pipe

```html
<!-- WRONG -->
<span>{{ data$ | async }}</span>

<!-- RIGHT -->
<span>{{ data() }}</span>
```
