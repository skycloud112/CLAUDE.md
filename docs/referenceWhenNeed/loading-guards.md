# Loading Guards and Providers

## Loading Guard Pattern

Create single unified LoadingGuard checking ALL async loading states.

- ✅ Consolidate all checks: `sessionStatus === 'loading' || mapsStatus !== 'LOADED'`
- ✅ Single loading UI, not nested guards
- ✅ Location: `components/LoadingGuard.tsx` (sensorry-ai), `admin/components/LoadingGuard.tsx` (rogerapps-turbo)
- ❌ Don't create separate guards per resource

**See:** `components/LoadingGuard.tsx` (sensorry-ai) or `admin/components/LoadingGuard.tsx` (rogerapps-turbo)

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

- ✅ Location: `auth/useSessionAccess.ts` (root, NOT admin/ in rogerapps-turbo)
- ✅ Return only relevant fields (not entire session)
- ✅ Assumes LoadingGuard ensures session loaded
- ✅ Use nullish coalescing (`??`) defensively

**IMPORTANT:** All auth code in `auth/` at root

**File locations (both projects):**
- ✅ `auth/useSessionAccess.ts` (root)
- ✅ `auth/LoginUseCase/`, `auth/MiddlewareUseCase/` (root)
- ✅ `auth/auth.ts` (NextAuth config)
- ❌ NOT `admin/auth/`, NOT `utils/`

**Example:**
```typescript
// auth/useSessionAccess.ts
export const useSessionAccess = () => {
  const { data: session } = useSession();
  return {
    hasLocationAccess: session?.hasLocationAccess ?? false,
    hasPolygonAccess: session?.hasPolygonAccess ?? false,
  };
};
```

## Access Control Component Pattern

Extract conditional rendering based on permissions into separate components.

- ✅ Component uses `useSessionAccess()` internally
- ✅ Parent passes callbacks, not permissions
- ❌ Don't mix access control in parent

**Testing:** Test all permission combinations
