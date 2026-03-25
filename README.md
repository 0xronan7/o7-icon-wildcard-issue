# Rolldown Wildcard Export Resolution Bug

**UPDATE:** This bug affects **both Vite 7 (Rollup) AND Vite 8 (rolldown)**.

## Reproduction

This repo demonstrates a bug where neither Rollup nor rolldown can resolve wildcard exports patterns from the @o7/icon package.

## Error

### Vite 7 (Rollup)
```
[commonjs--resolver] No known conditions for "./lucide/check.svelte" specifier 
in "@o7/icon" package
```

### Vite 8 (rolldown)
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
      "types": "./dist/lucide/*.svelte.d.ts",
      "svelte": "./dist/lucide/*.svelte"
    }
  }
}
```

This pattern **fails in both bundlers**:
- ❌ Vite 7 (Rollup) - build fails
- ❌ Vite 8 (rolldown) - build fails

## Expected Behavior

Wildcard exports should resolve:
- `@o7/icon/lucide/*` → `./dist/lucide/*.svelte`

## Reproduction Steps

```bash
pnpm install
pnpm build
```

## Workaround

Use explicit paths without wildcard pattern:

```js
// This works but defeats the purpose of exports wildcards
import CheckIcon from '@o7/icon/dist/lucide/check.svelte';
```

Or the @o7/icon Vite plugin transforms barrel imports in dev mode, but production builds still fail.

## Related

- @o7/icon: https://github.com/ottomated/o7-icon
- Rolldown: https://github.com/rolldown/rolldown
- Rollup: https://github.com/rollup/rollup
