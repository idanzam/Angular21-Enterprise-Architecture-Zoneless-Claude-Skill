<div align="center">

<img src="https://angular.dev/assets/images/press-kit/angular_wordmark_gradient.png" alt="Angular 21" height="80"/>

# Angular21 Enterprise Architecture — Zoneless

### The only production-grade Claude Code architecture system for Angular 21

[![Angular](https://img.shields.io/badge/Angular-21-red?style=for-the-badge&logo=angular)](https://angular.dev)
[![PrimeNG](https://img.shields.io/badge/PrimeNG-21-blue?style=for-the-badge)](https://primeng.org)
[![TailwindCSS](https://img.shields.io/badge/Tailwind-4-38bdf8?style=for-the-badge&logo=tailwindcss)](https://tailwindcss.com)
[![Zoneless](https://img.shields.io/badge/Zoneless-✓-green?style=for-the-badge)](https://angular.dev/guide/experimental/zoneless)
[![Signals](https://img.shields.io/badge/Signals-First-purple?style=for-the-badge)](https://angular.dev/guide/signals)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Optimized-orange?style=for-the-badge)](https://claude.ai)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

---

> **Not a template. Not a boilerplate. Not prompts.**
>
> A complete, opinionated, production-tested architecture system —
> built from a real-world crypto exchange platform with 75+ trading pairs,
> WebSocket infrastructure, and enterprise-grade performance requirements.

---

[Architecture](#-architecture) · [Rules](#-rules) · [Patterns](#-patterns) · [Anti-Patterns](#-anti-patterns) · [Claude Integration](#-claude-code-integration) · [Showcase](#-showcase)

</div>

---

## Why This Exists

Most "Angular Claude skills" online are:

- 🗑️ Random prompts
- 🗑️ Vibe-coded snippets  
- 🗑️ Outdated Angular 14/15 patterns
- 🗑️ Theory with no production validation

**This is different.**

Every rule, pattern, and constraint in this repository comes from a production system:

- ✅ Real-time crypto exchange (75+ trading pairs)
- ✅ WebSocket infrastructure with live order books
- ✅ Enterprise dashboard with complex state management
- ✅ Angular 21 Zoneless with full Signals architecture
- ✅ PrimeNG 21 with custom theming
- ✅ Multi-language i18n (EN/RU/HE)
- ✅ JWT auth with auto-refresh and inactivity detection

---

## 📁 Repository Structure

```
Angular21-Enterprise-Architecture-Zoneless/
│
├── CLAUDE.md                          ← Claude Code brain (start here)
│
├── .claude/
│   ├── architecture/
│   │   ├── frontend.md                ← Full frontend architecture
│   │   ├── signals.md                 ← Signals & reactivity system
│   │   ├── performance.md             ← Performance rules & baselines
│   │   ├── primeng21.md               ← PrimeNG 21 conventions
│   │   ├── websocket.md               ← WebSocket architecture
│   │   ├── state-management.md        ← State without NgRx
│   │   ├── auth.md                    ← JWT auth patterns
│   │   └── i18n.md                    ← Translation system
│   │
│   ├── rules/
│   │   ├── no-rxjs.md                 ← Zero RxJS policy
│   │   ├── zoneless.md                ← Zoneless rules
│   │   ├── primeng-layout.md          ← span vs div enforcement
│   │   ├── tailwind.md                ← Styling conventions
│   │   ├── api-patterns.md            ← fetch() over HttpClient
│   │   └── component-generation.md    ← CLI-only generation
│   │
│   ├── patterns/
│   │   ├── signal-store.md            ← Service as signal store
│   │   ├── polling.md                 ← REST polling pattern
│   │   ├── websocket-store.md         ← WS + signal integration
│   │   ├── auth-flow.md               ← Login/refresh/logout
│   │   ├── translation-guard.md       ← i18n effect guard
│   │   ├── realtime-table.md          ← Live data tables
│   │   ├── dialog-pattern.md          ← PrimeNG dialog system
│   │   ├── dashboard-layout.md        ← Enterprise dashboard
│   │   └── lazy-routes.md             ← Route lazy loading
│   │
│   ├── anti-patterns/
│   │   ├── rxjs-in-signals-world.md
│   │   ├── div-in-primeng.md
│   │   ├── effect-overuse.md
│   │   ├── ssr-complexity.md
│   │   └── css-hacks.md
│   │
│   └── generators/
│       ├── service.template.md
│       ├── component.template.md
│       └── feature-module.template.md
│
└── showcase/                          ← Real Angular 21 app
    └── README.md
```

---

## ⚡ Quick Start for Claude Code

Add to your project root:

```bash
curl -O https://raw.githubusercontent.com/idanzam/Angular21-Enterprise-Architecture-Zoneless/main/CLAUDE.md
```

Then in Claude Code:

```
Read CLAUDE.md and all referenced architecture files before writing any Angular code.
```

---

## 🏗️ Architecture

### The Stack

| Layer | Technology | Version |
|---|---|---|
| Framework | Angular Zoneless | 21 |
| UI Library | PrimeNG | 21 |
| Styling | TailwindCSS | 4 |
| Reactivity | Signals + computed + effect | Native |
| HTTP | fetch() API | Native |
| State | Service-based Signal Stores | — |
| i18n | @ngx-translate/core | Latest |
| Auth | JWT + localStorage | — |

### Core Principles

**1. Zoneless First**
```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideZonelessChangeDetection(),
    // ... rest of providers
  ]
};
```

**2. Signals Only — Zero RxJS**
```typescript
// ✅ CORRECT
readonly data = signal<MyType | null>(null);
readonly derived = computed(() => this.data()?.field ?? 0);

// ❌ BANNED
readonly data$ = new BehaviorSubject<MyType | null>(null);
```

**3. fetch() over HttpClient**
```typescript
// ✅ CORRECT
const res = await fetch(`${environment.api.baseURL}endpoint/`, {
  headers: { Authorization: `Bearer ${localStorage.getItem('token') ?? ''}` }
});

// ❌ BANNED
this.http.get('/endpoint').subscribe(...)
```

---

## 📋 Rules

### The Non-Negotiables

| Rule | Why |
|---|---|
| No RxJS for state | Signals are cleaner, simpler, zoneless-compatible |
| No `<div>` in PrimeNG slots | Breaks internal PrimeNG layout |
| No `ng build` by Claude | Developer deploys, Claude codes |
| No manual component files | Always use Angular CLI |
| No custom CSS (except `::ng-deep`) | Tailwind handles everything |
| No `text-sm` for body text | Readability standard |

### Component Generation (Mandatory)

```bash
# ALWAYS use CLI — NEVER create .ts/.html/.css manually
ng g c app/components/{feature}/{name}
ng g s app/services/{name}
```

---

## 🎯 Patterns

### Signal Store (Service Pattern)

```typescript
@Injectable({ providedIn: 'root' })
export class MarketService {
  // State
  readonly pairs = signal<Pair[]>([]);
  readonly isLoading = signal<boolean>(true);
  readonly hasError = signal<boolean>(false);

  // Derived
  readonly topPairs = computed(() =>
    this.pairs().sort((a, b) => b.volume - a.volume).slice(0, 7)
  );

  constructor() {
    this.loadPairs();
    setInterval(() => this.loadPairs(), 5000);
  }

  async loadPairs(): Promise<void> {
    try {
      const res = await fetch(`${environment.api.basePublicURL}pairs/`);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      const data = await res.json();
      this.pairs.set(data.pairs);
    } catch {
      this.hasError.set(true);
    } finally {
      this.isLoading.set(false);
    }
  }
}
```

### Translation Guard (Mandatory)

```typescript
// Every component using translations MUST have this guard
constructor() {
  effect(() => {
    this.languageService.lang(); // track language changes
    if (!this.languageService.translationsLoaded()) return; // guard
    this.trans = { ...this.languageService.translations };
    this.cdr.markForCheck();
  });
}
```

### WebSocket Integration (Trading Pages Only)

```typescript
// WebSocket is ONLY allowed on trading pages
// NEVER on: homepage, markets, dashboard, public pages

constructor() {
  effect(() => {
    if (this.loginService.isLoggedIn()) {
      this.connectWebSocket();
    }
  });
}

private connectWebSocket(): void {
  this.ws = new WebSocket(`${environment.wss_url}wsapi/v1/live_notifications`);
  this.ws.onmessage = (event) => {
    const msg = JSON.parse(event.data);
    this.handleWsMessage(msg);
  };
}
```

### Auth Flow

```typescript
// Login
const data = await this.loginService.login({ username, password });
// Automatically stores token/refresh_token in localStorage

// Protected request with auto-refresh
const token = localStorage.getItem('token');
const res = await fetch(url, {
  headers: { Authorization: `Bearer ${token}` }
});

if (res.status === 401) {
  const ok = await this.loginService.tryRefresh();
  if (!ok) { this.loginService.logout(); return; }
  // retry request
}
```

---

## ⛔ Anti-Patterns

### 1. RxJS in a Signals World

```typescript
// ❌ NEVER DO THIS
this.service.getData().pipe(
  map(data => data.items),
  tap(items => console.log(items))
).subscribe(items => this.items = items);

// ✅ DO THIS
const data = await this.service.getData();
this.items.set(data.items);
```

### 2. div Inside PrimeNG Components

```html
<!-- ❌ BREAKS PRIMENG LAYOUT -->
<p-card>
  <div class="flex gap-3">...</div>
</p-card>

<!-- ✅ CORRECT -->
<p-card>
  <span class="flex gap-3">...</span>
</p-card>

<!-- ❌ WRONG -->
<ng-template pTemplate="header">
  <div>...</div>
</ng-template>

<!-- ✅ CORRECT -->
<ng-template pTemplate="header">
  <span>...</span>
</ng-template>
```

### 3. Effect Overuse

```typescript
// ❌ WRONG — effect for everything
effect(() => {
  const data = this.data();
  this.processedData = data.map(x => x.value * 2); // should be computed
});

// ✅ CORRECT — computed for derived state
readonly processedData = computed(() =>
  this.data().map(x => x.value * 2)
);

// ✅ Effect ONLY for side effects
effect(() => {
  document.title = `${this.pageTitle()} | Pool4Ever`;
});
```

### 4. WebSocket Everywhere

```typescript
// ❌ WRONG — WebSocket on homepage
ngOnInit() {
  this.ws = new WebSocket(environment.wss_url); // NO!
}

// ✅ Use REST polling for public pages
constructor() {
  this.fetchData();
  setInterval(() => this.fetchData(), 5000);
}
```

---

## 🤖 Claude Code Integration

### CLAUDE.md Entry Point

The `CLAUDE.md` in this repo acts as the intelligence layer for Claude Code.
When placed in your project root, Claude will:

- ✅ Follow all architecture rules automatically
- ✅ Generate components with correct patterns
- ✅ Never introduce RxJS
- ✅ Never create files manually (use CLI)
- ✅ Respect PrimeNG layout constraints
- ✅ Apply translation guards in every component

### Folder Conventions Claude Follows

```text
New feature component  → app/components/{feature}/{name}/
New service            → app/services/{name}.service.ts
New layout piece       → app/layout/{name}/
New guard              → app/guards/{name}.guard.ts
```

### Claude Code Commands

```bash
# Generate component (Claude uses this — never manual files)
ng g c app/components/trading/order-book

# Generate service
ng g s app/services/order-book

# Generate guard
ng g g app/guards/auth
```

---

## 🎪 Showcase

A real Angular 21 Zoneless application demonstrating all patterns:

- **Homepage** with live price ticker (polling, not WebSocket)
- **Markets page** with 75+ trading pairs
- **Trading page** with real-time WebSocket order book
- **Dashboard** with JWT-protected routes
- **Multi-language** (EN/RU/HE) with translation guard

→ [View Showcase](showcase/README.md)

---

## 🏆 Battle-Tested On

This architecture powers **Pool4Ever Exchange** — a live cryptocurrency exchange platform:

- 75+ trading pairs across BTC, ETH, USDT, TRX markets
- Real-time WebSocket order book
- JWT auth with inactivity auto-logout
- Multi-language support
- Enterprise dashboard
- 24/7 production uptime

→ [exchange.pool4ever.com](https://exchange.pool4ever.com)

---

## 📄 License

MIT © [Idan Zamir Halevy](https://izh.pool4ever.com)

---

<div align="center">

**If this saved you hours of architecture decisions — give it a ⭐**

Built with 💪 from production. Not from theory.

</div>
