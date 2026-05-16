# Pattern: Translation Guard

## The Problem

Without the guard, translations show as raw keys (`navbar.BuyCrypto`)
on first render because the JSON file hasn't loaded yet.

## The Pattern

```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class MyComponent {
  private readonly languageService = inject(LanguageService);
  private readonly cdr = inject(ChangeDetectorRef);

  protected trans: any = {};

  constructor() {
    // MANDATORY in every component that uses translations
    effect(() => {
      this.languageService.lang(); // track language changes
      if (!this.languageService.translationsLoaded()) return; // GUARD
      this.trans = { ...this.languageService.translations };
      this.cdr.markForCheck();
    });
  }
}
```

## Template Usage

```html
<!-- Using trans object directly -->
<span>{{ trans.navbar?.BuyCrypto }}</span>
<span>{{ trans.common?.Submit }}</span>

<!-- With fallback -->
<span>{{ trans.page?.title ?? 'Loading...' }}</span>
```

## Language Service Pattern

```typescript
@Injectable({ providedIn: 'root' })
export class LanguageService {
  readonly lang = signal<string>(localStorage.getItem('lang') || 'en');
  readonly translationsLoaded = signal<boolean>(false);
  translations: any = {};

  async loadJson(lang: string): Promise<void> {
    this.translationsLoaded.set(false);
    try {
      const res = await fetch(`/i18n/${lang}.json`);
      this.translations = await res.json();
      this.translationsLoaded.set(true);
    } catch {
      console.error('Failed to load translations for:', lang);
    }
  }

  changeLanguage(lang: string): void {
    localStorage.setItem('lang', lang);
    this.lang.set(lang);
    this.loadJson(lang);
  }
}
```

## Rules

1. **Every** component using `trans` MUST have the effect with guard
2. The guard `if (!this.languageService.translationsLoaded()) return;` is mandatory
3. Always use optional chaining `trans.section?.key` in templates
4. Initialize `trans: any = {}` as empty object (not null/undefined)
