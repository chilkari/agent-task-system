# TypeScript Review Profile

This checklist is used during code review (Phase 6) and final polish (Phase 8)
for tasks involving TypeScript. Project-specific additions can be appended by
creating a `review-profiles/typescript.md` in the project config directory.

## Type Safety

- No `any` types. Use `unknown` with type narrowing, or define proper types.
  If `any` seems necessary, it usually means the types need to be designed
  differently.
- No type assertions (`as`). Prefer type guards, discriminated unions, or
  restructuring the code so TypeScript can infer the correct type. Type
  assertions bypass the compiler's safety checks.
- No non-null assertions (`!`). Handle nullability explicitly with checks,
  optional chaining, or by restructuring to eliminate the null case.
- Prefer `readonly` for data that should not be mutated. Apply to properties,
  arrays (`readonly string[]`), and parameters where appropriate.
- Prefer discriminated unions over boolean or string type flags. They provide
  exhaustiveness checking and make invalid states unrepresentable.

## Modules and Exports

- Prefer named exports over default exports. Named exports provide better
  refactoring support and prevent naming inconsistencies across imports.
- No barrel imports/exports (index.ts re-export files). Import directly from
  the file where the export is defined. Barrel files create circular dependency
  risks, make tree-shaking harder, and obscure where code actually lives.

## Patterns

- Avoid enums. Prefer `as const` objects or string/number union types. Enums
  have surprising runtime behavior and do not work well with type narrowing.
- Check for unhandled promise rejections. Every promise chain should have error
  handling. Async functions called without `await` in a non-async context must
  have a `.catch()` or be explicitly handled.
- Use strict TypeScript configuration (`strict: true` or equivalent individual
  flags). Do not weaken strictness to fix type errors -- fix the types instead.
