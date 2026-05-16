# Rule: PrimeNG 21 Layout — span vs div

## The Rule

**NEVER use `<div>` inside PrimeNG component slots or templates.**
**ALWAYS use `<span>` instead.**

## Why

PrimeNG components use internal CSS flexbox/grid layouts.
Inserting a `<div>` (block element) breaks these layouts because it creates
unexpected block formatting contexts. `<span>` (inline element) integrates cleanly.

## Examples

### p-card

```html
<!-- ❌ WRONG — breaks card layout -->
<p-card>
  <div class="flex gap-3">
    <div>Content A</div>
    <div>Content B</div>
  </div>
</p-card>

<!-- ✅ CORRECT -->
<p-card>
  <span class="flex gap-3">
    <span>Content A</span>
    <span>Content B</span>
  </span>
</p-card>
```

### ng-template inside PrimeNG

```html
<!-- ❌ WRONG -->
<ng-template pTemplate="header">
  <div class="flex justify-between">
    <div>Title</div>
    <div>Actions</div>
  </div>
</ng-template>

<!-- ✅ CORRECT -->
<ng-template pTemplate="header">
  <span class="flex justify-between w-full">
    <span>Title</span>
    <span>Actions</span>
  </span>
</ng-template>
```

### p-dialog

```html
<!-- ❌ WRONG -->
<p-dialog>
  <ng-template pTemplate="content">
    <div>Dialog content</div>
  </ng-template>
</p-dialog>

<!-- ✅ CORRECT -->
<p-dialog>
  <ng-template pTemplate="content">
    <span class="flex flex-col gap-4">
      Dialog content
    </span>
  </ng-template>
</p-dialog>
```

## PrimeNG Component Conventions

```html
<!-- Boolean props — use binding -->
<p-button [raised]="true" />          <!-- ✅ -->
<p-button raised="true" />            <!-- ❌ -->

<!-- Classes on PrimeNG components -->
<p-card styleClass="my-custom-class"> <!-- ✅ -->
<p-card class="my-custom-class">      <!-- ❌ (may not apply) -->

<!-- Images -->
<p-image [src]="imgUrl" />            <!-- ✅ -->
<img [src]="imgUrl" />                <!-- ❌ (use p-image) -->

<!-- Buttons -->
<p-button label="Click" />            <!-- ✅ -->
<button>Click</button>                <!-- ❌ (use p-button) -->

<!-- Dividers -->
<p-divider />                         <!-- ✅ between sections -->
```

## PrimeNG Theming

```typescript
// Dark/light mode via layoutService
readonly isDark = computed(() => this.layoutService.layoutConfig().darkTheme);

// Toggle
this.layoutService.layoutConfig.update(cfg => ({
  ...cfg,
  darkTheme: !cfg.darkTheme
}));
```

```css
/* PrimeNG overrides — ONLY in .css file, never inline */
::ng-deep .p-card-body {
  padding: 8px !important;
}

/* Never hardcode colors — use PrimeNG CSS variables */
color: var(--p-primary-color);
background: var(--p-surface-ground);
```
