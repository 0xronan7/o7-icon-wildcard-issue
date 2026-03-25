# Rolldown Wildcard Export Resolution Bug

## Reproduction

This repo demonstrates a bug where rolldown (Vite 8's bundler) cannot resolve wildcard exports patterns that work fine in Vite 7 (Rollup).

## Error

```
Error: Could not resolve '@o7/icon/lucide/check' in src/App.svelte
   ╭─[ src/App.svelte:4:23 ]
   │
 4 │ import CheckIcon from '@o7/icon/lucide/check';
   │                       ───────────┬───────────  
   │                                  ╰───────────── Package subpath is not defined by exports
```

## Root Cause

The `@o7/icon` package uses wildcard exports in its `package.json`:

```json
{
  "exports": {
    "./lucide": {
      "types": "./dist/lucide/index.d.ts",
      "svelte": "./dist/lucide/index.js"
    },
    "./lucide/*": {
      "types": "./dist/lucide/*.d.ts",
      "svelte": "./dist/lucide/*.svelte"
    }
  }
}
```

This pattern works in:
- ✅ Vite 7 (Rollup) - dev and build both work
- ✅ Vite 8 (rolldown) - **dev mode works** (plugin transforms imports correctly)
- ❌ Vite 8 (rolldown) - **build fails** with "Package subpath is not defined by exports"

## Expected Behavior

Rolldown should resolve wildcard exports (`"./lucide/*": "*.svelte"`) just like Rollup does.

## Reproduction Steps

```bash
pnpm install
pnpm build
```

## Workaround

Currently requires either:
1. Stay on Vite 7 (Rollup)
2. Use explicit imports: `@o7/icon/lucide/Check.svelte` (requires updating all imports)
3. Wait for rolldown fix

## Related

- @o7/icon: https://github.com/ottomated/o7-icon
- Rolldown: https://github.com/rolldown/rolldown
