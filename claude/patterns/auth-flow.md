# Pattern: JWT Auth Flow

## Login Service Pattern

```typescript
@Injectable({ providedIn: 'root' })
export class LoginService {
  private readonly router = inject(Router);

  // ── State ──────────────────────────────────────────
  readonly isLoggedIn = signal<boolean>(!!localStorage.getItem('token'));
  readonly currentToken = signal<string | null>(localStorage.getItem('token'));

  // ── Actions ────────────────────────────────────────
  async login(payload: LoginPayload): Promise<boolean> {
    try {
      const res = await fetch(`${environment.api.baseURL}auth/login/`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });

      if (!res.ok) return false;

      const data = await res.json();
      // Fields are 'access' and 'refresh' — NOT access_token/refresh_token
      localStorage.setItem('token', data.access);
      localStorage.setItem('refresh_token', data.refresh);
      this.currentToken.set(data.access);
      this.isLoggedIn.set(true);
      return true;
    } catch {
      return false;
    }
  }

  async tryRefresh(): Promise<boolean> {
    const refresh = localStorage.getItem('refresh_token');
    if (!refresh) return false;

    try {
      const res = await fetch(`${environment.api.baseURL}auth/token/refresh/`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ refresh })
      });

      if (!res.ok) return false;

      const data = await res.json();
      localStorage.setItem('token', data.access);
      this.currentToken.set(data.access);
      return true;
    } catch {
      return false;
    }
  }

  logout(): void {
    localStorage.removeItem('token');
    localStorage.removeItem('refresh_token');
    this.currentToken.set(null);
    this.isLoggedIn.set(false);
    this.router.navigate(['/account/login']);
  }
}
```

## Auth Guard

```typescript
export const AuthGuard: CanActivateFn = () => {
  const loginService = inject(LoginService);
  const router = inject(Router);

  if (loginService.isLoggedIn()) return true;
  return router.createUrlTree(['/account/login']);
};
```

## Protected Request with Auto-Refresh

```typescript
async protectedRequest(url: string): Promise<any | null> {
  const makeRequest = async (token: string) => {
    return fetch(url, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`
      }
    });
  };

  let token = localStorage.getItem('token') ?? '';
  let res = await makeRequest(token);

  if (res.status === 401) {
    const ok = await this.loginService.tryRefresh();
    if (!ok) {
      this.loginService.logout();
      return null;
    }
    token = localStorage.getItem('token') ?? '';
    res = await makeRequest(token);
  }

  if (!res.ok) return null;
  return res.json();
}
```

## Inactivity Auto-Logout

```typescript
@Injectable({ providedIn: 'root' })
export class InactivityService {
  private timer: ReturnType<typeof setTimeout> | null = null;
  private readonly loginService = inject(LoginService);

  constructor() {
    effect(() => {
      if (!this.loginService.isLoggedIn()) this.stop();
    });
  }

  start(minutes: number): void {
    this.stop();
    const ms = minutes * 60 * 1000;
    const reset = () => {
      clearTimeout(this.timer!);
      this.timer = setTimeout(() => this.loginService.logout(), ms);
    };
    ['mousemove', 'keydown', 'click', 'scroll'].forEach(e =>
      document.addEventListener(e, reset)
    );
    reset();
  }

  stop(): void {
    if (this.timer) clearTimeout(this.timer);
  }
}
```
