# Showcase — Pool4Ever Exchange

The architecture in this repository powers **Pool4Ever Exchange** —
a live cryptocurrency exchange platform built entirely with Angular 21 Zoneless.

## Live Demo

→ **[exchange.pool4ever.com](https://exchange.pool4ever.com)**

## What's Running

| Feature | Pattern Used |
|---|---|
| Homepage live prices | REST polling (5s interval) |
| Top 7 coins widget | `computed()` from signal store |
| Markets page (126+ pairs) | REST polling + signal store |
| Trading page order book | WebSocket + signal store |
| JWT auth + auto-refresh | LoginService signal pattern |
| Inactivity auto-logout | InactivityService + effect |
| Multi-language (EN/RU/HE) | Translation guard pattern |
| Dark/Light theme | layoutConfig signal |
| PrimeNG custom preset | mypreset.ts |

## Stack

```
Angular 21 (Zoneless)
PrimeNG 21
TailwindCSS 4
@ngx-translate/core
Native fetch() — zero HttpClient
Native WebSocket — zero socket.io
```

## Performance

- Zero Zone.js overhead
- OnPush everywhere
- Signals-driven updates (no unnecessary re-renders)
- orjson-serialized WebSocket messages
- REST polling with fetch (no Observable subscriptions)
