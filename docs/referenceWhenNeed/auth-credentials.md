# Authentication - Username/Password

## Overview

Login via credentials form. NextAuth.js is a thin framework layer, business logic lives in LoginUseCase.

## File Organization

All auth code in `auth/` folder within the app:

- `SessionUser.ts` - SessionUser type definition
- `SessionUserJWT.ts` - JWT type (mirrors SessionUser)
- `SessionCookieHandler.ts` - Interface for session creation
- `auth.ts` - NextAuth config with callbacks
- `next-auth.d.ts` - Type augmentation
- `handleMiddlewareRequest.ts` - Route protection logic
- `SessionLoadingGuard.tsx` - Loading guard with SessionProvider
- `sessionValidation.ts` - Server-side session validation
- `useSessionUser.ts` - Hook to get current user
- `usePermissions.ts` - Hook to get permissions with loading state
- `useHasPermission.ts` - Hook to check specific permissions
- `getDefaultPathToRedirect.ts` - Permission-based routing
- `LoginUseCase/` - Login business logic
- `api/auth/[...nextauth]/route.ts` - API route

## SessionUser Type

```typescript
export type SessionUser = {
  id: string;
  email: string;
  name: string;
  permissions: Permission[];
};
```

## SessionUserJWT Type

```typescript
export type SessionUserJWT = {
  [K in keyof SessionUser]: SessionUser[K];
};
```

## Auth.js Configuration

```typescript
const result: NextAuthResult = NextAuth({
  cookies: {
    sessionToken: {
      name: `next-auth.session-token-app-name`,
      options: {
        httpOnly: true,
        sameSite: "lax",
        path: "/",
        secure: process.env.NODE_ENV === "production",
      },
    },
  },
  callbacks: {
    jwt({ token, user }) {
      if (user) {
        const u = user as SessionUser;
        const sessionUser: SessionUserJWT = {
          id: u.id,
          email: u.email,
          name: u.name,
          permissions: u.permissions,
        };
        Object.assign(token, sessionUser);
      }
      return token;
    },
    session({ session, token }) {
      const t = token as SessionUserJWT;
      const user: SessionUser = {
        id: t.id,
        email: t.email,
        name: t.name,
        permissions: t.permissions,
      };
      return { ...session, user };
    },
  },
  providers: [
    Credentials({
      credentials: {},
      authorize: async (user) => {
        return user as User;
      },
    }),
  ],
});

export const { handlers, signIn, signOut, auth } = result as any;
```

## Type Augmentation

```typescript
declare module "next-auth" {
  interface User extends SessionUser {}

  interface Session extends DefaultSession {
    user: SessionUser;
  }
}

declare module "next-auth/jwt" {
  interface JWT extends Partial<SessionUserJWT> {}
}
```

## SessionCookieHandler Pattern

```typescript
export interface SessionCookieHandler {
  createSessionCookie(user: SessionUser): Promise<void>;
}

class SessionCookieHandlerImpl implements SessionCookieHandler {
  async createSessionCookie(user: SessionUser) {
    await signIn("credentials", { ...user, redirect: false });
  }
}
```

## Login Use Case

```typescript
export class LoginUseCase {
  constructor(
    private password: string,
    private sessionCookieHandler: SessionCookieHandler,
    private userGateway: UserGateway,
  ) {}

  async execute(request: LoginRequest): Promise<LoginResponse> {
    // Validate request, check user exists, validate password
    // ...

    const sessionUser: SessionUser = {
      id: user.id,
      email: user.email,
      name: user.fullName,
      permissions: user.permissions,
    };

    await this.sessionCookieHandler.createSessionCookie(sessionUser);

    return { sessionUser };
  }
}
```

## Route Protection with Middleware

```typescript
const AUTH_PROTECTED_PAGES = ["/dashboard", "/settings", "/profile"];

export const handleMiddlewareRequest = ({
  pathname,
  auth,
  method,
}: {
  pathname: string;
  auth: Session | null;
  method: string;
}): Result => {
  if (method === "POST") return { redirect: null };

  if (pathname === "/") {
    if (!auth) return { redirect: "/login" };
    const defaultPath = getDefaultPathToRedirect(auth.user.permissions);
    return { redirect: defaultPath };
  }

  if (isProtectedPage(pathname) && !auth) {
    return { redirect: "/login?expired=1" };
  }

  return { redirect: null };
};
```

## Session Loading Guard

```typescript
export const SessionLoadingGuard = ({ children }: { children: React.ReactNode }) => {
  return (
    <SessionProvider>
      <ChildrenWrapper>{children}</ChildrenWrapper>
    </SessionProvider>
  );
};

const ChildrenWrapper = ({ children }: { children: React.ReactNode }) => {
  const { isLoading } = usePermissions();
  if (isLoading) return <LoadingCircular />;
  return <>{children}</>;
};

// Usage in layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <SessionLoadingGuard>
      <AppLayout>{children}</AppLayout>
    </SessionLoadingGuard>
  );
}
```

## Client-side Hooks

```typescript
export const useSessionUser = () => {
  const session = useSession();
  return session.data!.user;
};

export const usePermissions = () => {
  const session = useSession();
  const permissions = (session.data?.user as SessionUser)?.permissions || [];
  const isLoading = session.status === "loading";
  return { permissions, isLoading };
};

export const useHasPermission = () => {
  const { permissions, isLoading } = usePermissions();
  const hasPermission = (permission: Permission) => {
    return (
      permissions.includes(permission) ||
      permissions.includes(Permission.SUPER_ADMIN)
    );
  };
  const hasSuperAdminPermission = permissions.includes(Permission.SUPER_ADMIN);
  return { hasPermission, hasSuperAdminPermission, permissions, isLoading };
};
```

## Server-side Session Validation

```typescript
export const validateSession = async (): Promise<void> => {
  const session: Session | null = await auth();
  if (!session) throw new Error("Invalid session");
};

export const getUserId = async (): Promise<string> => {
  const session: Session | null = await auth();
  if (!session) throw new Error("Invalid session");
  return session.user.id;
};
```

## Permission-based Routing

```typescript
export const getDefaultPathToRedirect = (permissions: Permission[]) => {
  if (permissions.includes(Permission.SUPER_ADMIN)) return Pages.Dashboard;
  if (permissions.includes(Permission.PAGE_SETTINGS)) return Pages.Settings;
  if (permissions.includes(Permission.PAGE_PROFILE)) return Pages.Profile;
  // ... other permission checks
  throw new Error("Not found a valid page to redirect to");
};
```

## API Route

```typescript
import { handlers } from "@/auth/auth";
export const { GET, POST } = handlers;
```

## Best Practices

- Business logic in LoginUseCase, not auth.ts callbacks
- SessionCookieHandler interface for testability
- SessionLoadingGuard wraps protected layouts
- useSessionUser hook for accessing user data in components
- useHasPermission for permission checks with SUPER_ADMIN fallback
- Server-side validation via sessionValidation.ts
- Custom cookie name to avoid conflicts between apps
- handleMiddlewareRequest for centralized route protection logic
