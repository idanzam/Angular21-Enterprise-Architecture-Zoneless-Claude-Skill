# Angular21 Enterprise Architecture — Claude Instructions

## Read These First (In Order)

1. Architecture: `.claude/architecture/frontend.md`
2. Architecture: `.claude/architecture/signals.md`
3. Architecture: `.claude/architecture/primeng21.md`
4. Architecture: `.claude/architecture/websocket.md`
5. Architecture: `.claude/architecture/auth.md`
6. Architecture: `.claude/architecture/i18n.md`
7. Architecture: `.claude/architecture/performance.md`
8. Rules: `.claude/rules/no-rxjs.md`
9. Rules: `.claude/rules/zoneless.md`
10. Rules: `.claude/rules/primeng-layout.md`
11. Rules: `.claude/rules/tailwind.md`
12. Rules: `.claude/rules/api-patterns.md`
13. Rules: `.claude/rules/component-generation.md`

## Absolute Non-Negotiables

- ZERO RxJS — ever. No Observable, no Subject, no subscribe()
- ZERO manual component files — always use Angular CLI
- ZERO `<div>` inside PrimeNG components — use `<span>` only
- ZERO `ng build` or `ng serve` — developer handles this
- ZERO docker cp to any container
- ZERO custom CSS — Tailwind only (except ::ng-deep for PrimeNG internals)
- ZERO WebSocket outside of the Trade page

## Stack

- Angular 21 — Zoneless, Standalone components
- PrimeNG 21 — All UI components
- TailwindCSS 4 — All styling
- Signals + computed + effect — All reactivity
- fetch() — All HTTP (no HttpClient)
- @ngx-translate/core — i18n
