# WebSocket Architecture

## Critical Rule

**WebSocket is ONLY allowed on the Trade/Trading page.**

```text
✅ ALLOWED:   /account/trade  (Trade component)
❌ FORBIDDEN: Homepage
❌ FORBIDDEN: Markets page
❌ FORBIDDEN: Dashboard
❌ FORBIDDEN: Any public page
❌ FORBIDDEN: Any service that's not trade-specific
```

Use REST polling for all public/semi-public data.

## WebSocket Service Pattern

```typescript
@Injectable({ providedIn: 'root' })
export class TradingWebSocketService {
  private ws: WebSocket | null = null;

  // ── State Signals ──────────────────────────────────
  readonly orderBook = signal<OrderBook | null>(null);
  readonly userBalance = signal<Balance[]>([]);
  readonly openOrders = signal<Order[]>([]);
  readonly recentTrades = signal<Trade[]>([]);
  readonly isConnected = signal<boolean>(false);

  connect(pair: string): void {
    if (this.ws) this.disconnect();

    this.ws = new WebSocket(`${environment.wss_url}wsapi/v1/live_notifications`);

    this.ws.onopen = () => {
      this.isConnected.set(true);
      this.subscribe(pair);
    };

    this.ws.onmessage = (event) => {
      const msg = JSON.parse(event.data);
      this.handleMessage(msg);
    };

    this.ws.onclose = () => {
      this.isConnected.set(false);
      // Auto-reconnect after 3s
      setTimeout(() => this.connect(pair), 3000);
    };

    this.ws.onerror = () => {
      this.isConnected.set(false);
    };
  }

  private subscribe(pair: string): void {
    const token = localStorage.getItem('token');
    // Subscribe to public channels
    this.send({ command: 'add_stack', params: { pair }, token: '' });
    this.send({ command: 'add_recent_trades', params: { pair }, token: '' });
    // Subscribe to private channels (require JWT)
    if (token) {
      this.send({ command: 'add_balance', params: {}, token });
      this.send({ command: 'add_orders', params: {}, token });
    }
  }

  private handleMessage(msg: WsMessage): void {
    switch (msg.kind) {
      case 'stack':
        this.orderBook.set(msg.data);
        break;
      case 'balance':
        this.userBalance.set(msg.data);
        break;
      case 'opened_orders':
        this.openOrders.set(msg.data);
        break;
      case 'trades':
        this.recentTrades.set(msg.data);
        break;
    }
  }

  send(payload: object): void {
    if (this.ws?.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify(payload));
    }
  }

  disconnect(): void {
    this.ws?.close();
    this.ws = null;
    this.isConnected.set(false);
  }
}
```

## Trade Component Usage

```typescript
@Component({ /* ... */ })
export class TradeComponent implements OnDestroy {
  protected readonly wsService = inject(TradingWebSocketService);
  protected readonly route = inject(ActivatedRoute);

  constructor() {
    // Get pair from route and connect
    effect(() => {
      const pair = this.route.snapshot.params['pair'] ?? 'BTC-USDT';
      this.wsService.connect(pair);
    });
  }

  ngOnDestroy(): void {
    this.wsService.disconnect();
  }
}
```

## Message Protocol

**Client → Server:**
```json
{
  "command": "add_stack | add_balance | add_orders | add_recent_trades",
  "params": { "pair": "BTC-USDT" },
  "token": "jwt_access_token"
}
```

**Server → Client:**
```json
{
  "kind": "stack | balance | opened_orders | trades | pairs_volume",
  "data": {},
  "pair": "BTC-USDT"
}
```
