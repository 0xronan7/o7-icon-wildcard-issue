# @o7/icon Wildcard Export Resolution Bug

**Affects:** Vite 8 (rolldown) - production stable

## Reproduction

This repo demonstrates a bug where the @o7/icon package's wildcard exports pattern fails to resolve in Vite 8.

## Error

```
"./lucide/check" is not exported under the conditions ["module", "browser", "production", "import"]
from package @o7/icon (see exports field in package.json)
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

This wildcard pattern fails in Vite 8 (rolldown) in both dev and build modes.

## Expected Behavior

Wildcard exports should resolve:
- `@o7/icon/lucide/*` → `./dist/lucide/*.svelte`

## Reproduction Steps

```bash
pnpm install
pnpm build  # FAILS
pnpm dev    # FAILS
```

## Workaround

Use explicit paths without wildcard pattern:

```js
import CheckIcon from '@o7/icon/dist/lucide/check.svelte';
```

## Related

- @o7/icon: https://github.com/ottomated/o7-icon
- Vite: https://github.com/vitejs/vite
