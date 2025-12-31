# Authentication - UUID URL Param

## Overview
Login via UUID in URL parameter. Auth.js is a thin framework layer, business logic lives in LoginUseCase.

## Flow
1. **GuidPage**: Client-side page that calls LoginAction with UUID from URL
2. **useAuthenticate hook**: Handles UI redirection based on success/failure
3. **LoginUseCase**: Server-side orchestration
   - Validates UUID (via AuthGateway)
   - Creates/deletes session (via SessionGateway)
   - Logs errors (via Logger)
   - Returns `{success: true}` or `{success: false, error: string}`
4. **SessionGateway**: Thin wrapper around Auth.js signIn/signOut
5. **Auth.js authorize callback**: Pure pass-through - validation already done

## File Organization

- `gateways/SessionGateway.ts` - SessionData interface (single source of truth)
- `gateways/SessionGatewayImpl.ts` - NextAuth integration
- `auth/LoginUseCase/LoginUseCase.ts` - Business logic, creates SessionData
- `auth/auth.ts` - NextAuth config (mechanical data transfer only)
- `auth/next-auth.d.ts` - Type augmentation
- `auth/useSessionAccess.ts` - Custom hook for specific fields

## SessionData Flow

### Single Source of Truth

```typescript
// gateways/SessionGateway.ts
export interface SessionData {
  id: string;
  guid: string;
}
```

### Data Flow

**1. LoginUseCase creates SessionData**:
```typescript
const sessionData: SessionData = { id: guid, guid };
await this.sessionGateway.createSession(sessionData);
```

**2. SessionGateway passes to Auth.js**:
```typescript
await signIn('guid-login', {
  sessionData: JSON.stringify(sessionData),
  redirect: false,
});
```

**3. Auth.js authorize callback returns it**:
```typescript
async authorize(credentials) {
  const sessionData = JSON.parse(credentials.sessionData);
  return sessionData;  // Validation already done by LoginUseCase
}
```

**4. JWT callback stores fields**:
```typescript
async jwt({ token, user }) {
  if (user) {
    token.id = user.id;
    token.guid = user.guid;
  }
  return token;
}
```

**5. Session callback exposes fields at root**:
```typescript
async session({ session, token }) {
  if (token.id) session.id = token.id;
  if (token.guid) session.guid = token.guid;
  return session;
}
```

**6. Application reads session**:
```typescript
const session = await auth();
if (session) {
  console.log(session.guid);  // UUID from URL
}
```

## Type Augmentation

```typescript
// auth/next-auth.d.ts
declare module 'next-auth' {
  interface User {
    id: SessionData['id'];
    guid: SessionData['guid'];
  }
  interface Session extends DefaultSession {
    id: SessionData['id'];
    guid: SessionData['guid'];
  }
}
declare module 'next-auth/jwt' {
  interface JWT {
    id?: SessionData['id'];
    guid?: SessionData['guid'];
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
- ✅ Test: Use case business logic (`LoginUseCase.spec.ts`)
- ❌ Don't test: NextAuth callbacks (just data transfer)
- ❌ No sensitive data in JWT (not encrypted)
