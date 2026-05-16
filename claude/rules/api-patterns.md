# Rule: API Patterns — fetch() Only

## HTTP Client is Banned

`HttpClient` from `@angular/common/http` uses Observables internally.
This violates the Zero RxJS rule. Use native `fetch()` for all HTTP requests.

## Standard Request Pattern

```typescript
async loadData(): Promise<void> {
  this.isLoading.set(true);
  try {
    const res = await fetch(`${environment.api.baseURL}endpoint/`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${localStorage.getItem('token') ?? ''}`
      }
    });

    if (!res.ok) throw { status: res.status };
    const data = await res.json();
    this.data.set(data);
    this.hasError.set(false);
  } catch (err: any) {
    if (err?.status === 401) {
      const ok = await this.loginService.tryRefresh();
      if (!ok) { this.loginService.logout(); return; }
      await this.loadData(); // retry after refresh
      return;
    }
    this.hasError.set(true);
  } finally {
    this.isLoading.set(false);
  }
}
```

## POST Request

```typescript
async createOrder(payload: OrderPayload): Promise<boolean> {
  try {
    const res = await fetch(`${environment.api.baseURL}orders/`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${localStorage.getItem('token') ?? ''}`
      },
      body: JSON.stringify(payload)
    });
    return res.ok;
  } catch {
    return false;
  }
}
```

## Public Request (No Auth)

```typescript
async loadPublicData(): Promise<void> {
  try {
    const res = await fetch(`${environment.api.basePublicURL}pairs/`);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    this.pairs.set(data.pairs ?? []);
  } catch {
    this.hasError.set(true);
  }
}
```

## Polling Pattern (for public pages)

```typescript
constructor() {
  this.loadData();
  setInterval(() => this.loadData(), 5000); // every 5 seconds
}
```

## REST vs WebSocket

| Data Type | Transport | Page |
|---|---|---|
| Public prices/tickers | REST polling (5s) | Homepage, Markets |
| Order book (live) | WebSocket | Trade page ONLY |
| User balances | WebSocket | Trade page ONLY |
| Dashboard data | REST polling | Dashboard |
| Profile | REST (on demand) | Settings |
