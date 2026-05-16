# Contributing

## Philosophy

This is an **opinionated** architecture system based on production experience.
Contributions must align with the core principles — not work around them.

## What We Accept

- ✅ New patterns backed by production use cases
- ✅ Additional anti-patterns with real examples
- ✅ Generator templates for common features
- ✅ Bug fixes in existing documentation
- ✅ Improvements to existing patterns

## What We Don't Accept

- ❌ RxJS patterns (zero tolerance)
- ❌ Angular module-based patterns (standalone only)
- ❌ Patterns not validated in production
- ❌ Generic "best practices" without Angular 21 specificity

## How to Contribute

1. Fork the repository
2. Create a branch: `git checkout -b pattern/my-new-pattern`
3. Add your pattern/rule/anti-pattern with real code examples
4. Open a Pull Request with:
   - What production problem this solves
   - Why it's specific to Angular 21 / Zoneless / PrimeNG 21

## Pattern Template

````markdown
# Pattern: Your Pattern Name

## Problem
What production problem does this solve?

## Solution

```typescript
// Real production code
```

## Rules
1. Rule one
2. Rule two
````
