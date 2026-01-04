# Loading Guards and Providers

## Loading Guard Pattern

Create single unified LoadingGuard checking ALL async loading states.

- ✅ Consolidate all checks: `sessionStatus === 'loading' || mapsStatus !== 'LOADED'`
- ✅ Single loading UI, not nested guards
- ❌ Don't create separate guards per resource

## Provider Architecture

Use third-party providers directly in layout.tsx - don't create wrapper components.

- ✅ Direct: `<APIProvider apiKey={...}><SessionProvider>` in layout.tsx
- ✅ Explicit config visible
- ❌ No custom wrappers like `<GoogleMapsProvider>`

### Provider Hierarchy

Order matters - outer to inner:

1. Framework utilities (AppRouterCacheProvider, theme)
2. External APIs (APIProvider, analytics)
3. Auth (SessionProvider)
4. Loading Guards (guards ALL above)
5. App content (Container, Header, QueryProviders, children)

```typescript
<AppRouterCacheProvider>
  <APIProvider apiKey={...}>
    <SessionProvider>
      <LoadingGuard>             {/* Guards session + APIs */}
        <Container>
          <Header />
          <QueryProviders>{children}</QueryProviders>
        </Container>
      </LoadingGuard>
    </SessionProvider>
  </APIProvider>
</AppRouterCacheProvider>
```

## Session Access Custom Hooks

Extract session data into focused custom hooks.

- ✅ Location: `auth/useSessionAccess.ts`
- ✅ Return only relevant fields (not entire session)
- ✅ Assumes LoadingGuard ensures session loaded
