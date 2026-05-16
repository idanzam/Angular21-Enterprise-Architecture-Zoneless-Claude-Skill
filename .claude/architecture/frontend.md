# Frontend Architecture — Angular 21 Zoneless

## Stack

- **Angular 21** — Standalone components, Zoneless change detection
- **PrimeNG 21** — All UI components
- **TailwindCSS 4** — All styling
- **@ngx-translate/core** — i18n (JSON files per language)
- **No RxJS anywhere** — Signals + async/await only

## App Structure

```text
src/app/
├── environment.ts              # API URLs, config
├── app.config.ts               # Application config (provideZonelessChangeDetection)
├── app-routing.ts              # All routes
├── app.ts / app.html           # Root component

├── services/                   # All services (one file per service)
│   ├── login.service.ts        # Auth: login(), logout(), tryRefresh(), isLoggedIn signal
│   ├── auth.guard.ts           # Route guard: checks isLoggedIn signal
│   ├── profile.service.ts      # Profile signal store
│   ├── inactivity.service.ts   # Auto-logout on inactivity
│   ├── language.service.ts     # lang signal, translationsLoaded signal
│   ├── layout.service.ts       # layoutConfig signal (darkTheme, etc.)
│   └── api.service.ts          # Base API service

├── layout/                     # Structural UI
│   ├── navbar/
│   ├── footer/
│   └── status-bar/

└── components/                 # Feature pages
    ├── account/
    │   ├── login/
    │   ├── register/
    │   └── trade/              # WebSocket ONLY here
    ├── dashboard/
    ├── markets/
    └── shared/                 # Shared components
```

## Application Config (Zoneless)

```typescript
// app.config.ts
import { provideZonelessChangeDetection } from '@angular/core';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZonelessChangeDetection(),
    provideRouter(routes),
    // ... other providers
  ]
};
```

## Environment Pattern

```typescript
// environment.ts
export const environment = {
  production: false,
  api: {
    baseURL: 'https://your-api.com/api/v1/',
    basePublicURL: 'https://your-api.com/api/public/v1/',
    host: 'your-api.com',
  },
  wss_url: 'wss://',
};
```

## File Naming Conventions

```text
Component:  feature-name.ts / feature-name.html / feature-name.css
Service:    feature-name.service.ts
Guard:      feature-name.guard.ts
```

## Change Detection

```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
  // ...
})
export class MyComponent {
  constructor(private cdr: ChangeDetectorRef) {}

  // After signal updates in effects:
  this.cdr.markForCheck();
}
```

## Route Structure

```typescript
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'markets', component: MarketsComponent },
  {
    path: 'account',
    children: [
      { path: 'login', component: LoginComponent },
      { path: 'trade', component: TradeComponent },
      {
        path: 'dashboard',
        canActivate: [AuthGuard],
        children: [
          { path: 'main', component: DashboardMainComponent },
        ]
      }
    ]
  }
];
```
