# @o7/icon Wildcard Export Resolution Bug

**Affects:** Both Vite 7 (Rollup) AND Vite 8 (rolldown)

## Reproduction

This repo demonstrates a bug where the @o7/icon package's wildcard exports pattern fails to resolve in modern bundlers.

## Error

### Vite 7 (Rollup)
```
✘ [ERROR] No known conditions for "./lucide/check.svelte" specifier in "@o7/icon" package
```
- ❌ Dev mode: fails
- ❌ Build: fails

### Vite 8 (rolldown)
```
Error: Could not resolve '@o7/icon/lucide/check' in src/App.svelte
   ╭─[ src/App.svelte:4:23 ]
   │
 4 │ import CheckIcon from '@o7/icon/lucide/check';
   │                       ───────────┬───────────  
   │                                  ╰───────────── Package subpath is not defined by exports
```
- ❌ Dev mode: fails
- ❌ Build: fails

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

This pattern **fails in both bundlers, in both dev and build modes**:
- ❌ Vite 7 (Rollup) - dev & build fail
- ❌ Vite 8 (rolldown) - dev & build fail

The `@o7/icon` Vite plugin (`o7Icon()`) attempts to rewrite imports at build time, but it doesn't fix the underlying exports resolution issue.

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

## Related

- @o7/icon: https://github.com/ottomated/o7-icon
- Vite: https://github.com/vitejs/vite
- Rollup: https://github.com/rollup/rollup
- Rolldown: https://github.com/rolldown/rolldown
