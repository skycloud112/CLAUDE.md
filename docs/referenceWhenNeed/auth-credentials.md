# Authentication - Username/Password

## Overview
Login via username and password form. Auth.js is a thin framework layer, business logic lives in LoginUseCase.

## Flow
1. **LoginPage**: Form with username/password inputs
2. **LoginUseCase**: Server-side orchestration
   - Validates credentials (via AuthGateway)
   - Creates session with user data (via SessionGateway)
   - Returns `{success: true}` or `{success: false, error: string}`
3. **SessionGateway**: Thin wrapper around Auth.js signIn/signOut
4. **Auth.js authorize callback**: Pure pass-through - validation already done

## File Organization

All auth code at root (NOT in admin/):
- `gateways/SessionGateway.ts` - SessionData interface (shared package at root)
- `gateways/SessionGatewayImpl.ts` - NextAuth integration (shared package at root)
- `auth/LoginUseCase/LoginUseCase.ts` - Business logic at root
- `auth/auth.ts` - NextAuth config at root
- `auth/next-auth.d.ts` - Type augmentation at root
- `auth/useSessionAccess.ts` - Custom hook at root

## SessionData Flow

### Single Source of Truth

```typescript
// gateways/SessionGateway.ts
export interface SessionData {
  id: string;
  userId: string;
  hasLocationAccess: boolean;
  hasPolygonAccess: boolean;
}
```

### Data Flow

**1. LoginUseCase validates and creates SessionData**:
```typescript
const user = await this.authGateway.validateCredentials(username, password);
if (!user) return { success: false, error: 'Invalid credentials' };

const sessionData: SessionData = {
  id: user.id,
  userId: user.id,
  hasLocationAccess: user.hasLocationAccess,
  hasPolygonAccess: user.hasPolygonAccess,
};
await this.sessionGateway.createSession(sessionData);
```

**2. SessionGateway passes to Auth.js**:
```typescript
await signIn('credentials', {
  sessionData: JSON.stringify(sessionData),
  redirect: false,
});
```

**3. Auth.js callbacks store and expose fields**:
```typescript
// jwt callback
async jwt({ token, user }) {
  if (user) {
    token.id = user.id;
    token.userId = user.userId;
    token.hasLocationAccess = user.hasLocationAccess;
    token.hasPolygonAccess = user.hasPolygonAccess;
  }
  return token;
}

// session callback
async session({ session, token }) {
  session.id = token.id;
  session.userId = token.userId;
  session.hasLocationAccess = token.hasLocationAccess ?? false;
  session.hasPolygonAccess = token.hasPolygonAccess ?? false;
  return session;
}
```

**4. useSessionAccess hook for components**:
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

## Type Augmentation

```typescript
// auth/next-auth.d.ts
declare module 'next-auth' {
  interface User {
    id: SessionData['id'];
    userId: SessionData['userId'];
    hasLocationAccess: SessionData['hasLocationAccess'];
    hasPolygonAccess: SessionData['hasPolygonAccess'];
  }
  interface Session extends DefaultSession {
    id: SessionData['id'];
    userId: SessionData['userId'];
    hasLocationAccess: SessionData['hasLocationAccess'];
    hasPolygonAccess: SessionData['hasPolygonAccess'];
  }
}
declare module 'next-auth/jwt' {
  interface JWT {
    id?: SessionData['id'];
    userId?: SessionData['userId'];
    hasLocationAccess?: SessionData['hasLocationAccess'];
    hasPolygonAccess?: SessionData['hasPolygonAccess'];
  }
}
```

## Adding New Session Fields

1. Add to `SessionData` interface
2. TypeScript errors guide updates to jwt() and session() callbacks

## Best Practices

- ✅ All session fields in `SessionData` interface
- ✅ Business logic in `LoginUseCase`, not `auth.ts` callbacks
- ✅ Custom hooks: `useSessionAccess()` not direct `useSession()`
- ✅ All auth code at root level (NOT in admin/)
- ✅ Test: Use case business logic (`LoginUseCase.spec.ts`)
- ❌ Don't test: NextAuth callbacks (just data transfer)
- ❌ No sensitive data in JWT (not encrypted)
