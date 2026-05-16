# Rule: Component Generation — CLI Only

## Absolute Rule

**NEVER create Angular component files manually.**
**ALWAYS use Angular CLI.**

## Why

Angular CLI:
- Registers the component in the correct module (if needed)
- Creates the correct file structure
- Sets up the correct imports
- Handles standalone configuration
- Prevents typos in boilerplate

## Commands

```bash
# Component
ng g c app/components/{feature}/{name}

# Service
ng g s app/services/{name}

# Guard
ng g g app/guards/{name}

# Pipe
ng g p app/pipes/{name}

# Directive
ng g d app/directives/{name}
```

## Generated Structure

```text
app/components/trading/order-book/
├── order-book.ts
├── order-book.html
└── order-book.css
```

## After Generation — Modify

After CLI generation, modify the files:

```typescript
// order-book.ts — after ng g c
@Component({
  selector: 'app-order-book',
  standalone: true,
  imports: [CommonModule, /* PrimeNG imports */],
  templateUrl: './order-book.html',
  styleUrl: './order-book.css',
  changeDetection: ChangeDetectionStrategy.OnPush  // ADD THIS
})
export class OrderBookComponent {
  // Add signals and logic here
}
```

## If CLI is Unavailable

If Angular CLI cannot be run, Claude MUST STOP and inform the developer.
Do NOT create files manually as a workaround.
