# Signals & Reactivity — Angular 21

## Core Principle

**Signals replace everything RxJS used to do for state management.**

| Old (RxJS) | New (Signals) |
|---|---|
| `BehaviorSubject<T>` | `signal<T>()` |
| `Observable<T>` | `signal<T>()` or `Promise<T>` |
| `combineLatest` / `map` | `computed<T>()` |
| `.subscribe()` | `effect()` in constructor |
| `async pipe` | `mySignal()` in template |

## Signal Types

### State Signal
```typescript
readonly data = signal<MyType | null>(null);
readonly isLoading = signal<boolean>(true);
readonly hasError = signal<boolean>(false);
readonly count = signal<number>(0);
```

### Computed Signal (Derived State)
```typescript
readonly topItems = computed(() =>
  this.data()
    ?.filter(x => x.active)
    .sort((a, b) => b.value - a.value)
    .slice(0, 7) ?? []
);

readonly isEmpty = computed(() => this.data().length === 0);
readonly total = computed(() => this.items().reduce((sum, x) => sum + x.amount, 0));
```

### Effect (Side Effects Only)
```typescript
constructor() {
  // ✅ CORRECT — effect for side effects
  effect(() => {
    document.title = `${this.pageTitle()} | MyApp`;
  });

  // ✅ CORRECT — effect for reacting to signal changes
  effect(() => {
    if (this.loginService.isLoggedIn()) {
      this.loadUserData();
    }
  });
}
```

## Service as Signal Store Pattern

```typescript
@Injectable({ providedIn: 'root' })
export class MarketService {
  // ── State ──────────────────────────────────────────
  readonly pairs = signal<Pair[]>([]);
  readonly selectedPair = signal<string>('BTC-USDT');
  readonly isLoading = signal<boolean>(true);
  readonly hasError = signal<boolean>(false);

  // ── Derived ────────────────────────────────────────
  readonly currentPair = computed(() =>
    this.pairs().find(p => p.code === this.selectedPair())
  );

  readonly topByVolume = computed(() =>
    [...this.pairs()]
      .sort((a, b) => b.volume24h - a.volume24h)
      .slice(0, 7)
  );

  // ── Actions ────────────────────────────────────────
  selectPair(code: string): void {
    this.selectedPair.set(code);
  }

  async loadPairs(): Promise<void> {
    this.isLoading.set(true);
    try {
      const res = await fetch(`${environment.api.basePublicURL}pairs/`);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      const data = await res.json();
      this.pairs.set(data.pairs ?? []);
      this.hasError.set(false);
    } catch {
      this.hasError.set(true);
    } finally {
      this.isLoading.set(false);
    }
  }
}
```

## Component Consuming Signals

```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    @if (marketService.isLoading()) {
      <p-progressSpinner />
    } @else {
      @for (pair of marketService.topByVolume(); track pair.code) {
        <span>{{ pair.code }} — {{ pair.price }}</span>
      }
    }
  `
})
export class MarketsComponent {
  protected readonly marketService = inject(MarketService);
}
```

## Signal Update Methods

```typescript
// Set new value
this.count.set(10);

// Update based on current value
this.count.update(v => v + 1);

// Mutate object/array (use sparingly)
this.items.update(items => [...items, newItem]);
```

## Rules

1. **Never** use `signal()` inside `ngOnInit` — initialize in constructor or as class fields
2. **Never** use `effect()` for derived state — use `computed()` instead
3. **Always** use `computed()` for values derived from other signals
4. **Always** initialize signals with a sensible default (not undefined)
5. **Never** call signals inside `ngOnChanges` — use `input()` signals instead
