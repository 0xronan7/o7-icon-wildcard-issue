# Rolldown Wildcard Exports Issue Reproduction

This repo reproduces a bug where rolldown cannot resolve wildcard exports patterns in package.json.

## Error

```
Error: Could not resolve '@o7/icon/lucide/check' in src/App.svelte
Package subpath is not defined by exports
```

## Root Cause

The `@o7/icon` package uses wildcard exports in its package.json:

```json
{
  "exports": {
    "./lucide/*": "./dist/lucide/*.svelte"
  }
}
```

This works fine with:
- ✅ Vite 7 + Rollup (dev and build)
- ✅ Vite 8 + Rolldown (dev mode only)
- ❌ Vite 8 + Rolldown (production build)

## Reproduction Steps

```bash
pnpm install
pnpm build
```

## Expected Behavior

The build should succeed and resolve `@o7/icon/lucide/check` to `./dist/lucide/check.svelte` based on the wildcard export pattern.

## Actual Behavior

Rolldown fails to resolve the subpath import, throwing "Package subpath is not defined by exports".

## Environment

- Vite: 8.0.2
- Rolldown: 1.0.0-rc.11
- Node: v24.14.0
- Package: @o7/icon 0.3.79

## Related

This appears to be a rolldown limitation with wildcard export patterns, not an issue with the @o7/icon plugin (which correctly transforms imports in dev mode).
