# Next.js Application

Modern Next.js application with App Router.

## Tech Stack

- **Framework**: Next.js 14+
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **Database**: Prisma / Drizzle
- **Auth**: NextAuth.js / Clerk
- **Testing**: Vitest + Playwright

## Project Structure

- `app/` - App Router pages and layouts
  - `(auth)/` - Auth route group
  - `(dashboard)/` - Dashboard route group
  - `api/` - API routes
  - `layout.tsx` - Root layout
  - `page.tsx` - Home page
- `components/` - React components
  - `ui/` - UI primitives
  - `forms/` - Form components
- `lib/` - Utility functions
- `hooks/` - Custom hooks
- `types/` - TypeScript types
- `prisma/` - Database schema

## Development Commands

- **Dev**: `npm run dev`
- **Build**: `npm run build`
- **Start**: `npm start`
- **Lint**: `npm run lint`
- **Test**: `npm test`

## Coding Guidelines

### Server Components (Default)

```typescript
// app/users/page.tsx - Server Component
import { getUsers } from '@/lib/api';

export default async function UsersPage() {
  const users = await getUsers();
  
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Client Components

```typescript
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count: {count}
    </button>
  );
}
```

### Layouts

```typescript
// app/layout.tsx
import { Inter } from 'next/font/google';
import './globals.css';

const inter = Inter({ subsets: ['latin'] });

export const metadata = {
  title: 'My App',
  description: 'App description',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={inter.className}>{children}</body>
    </html>
  );
}
```

### Data Fetching

```typescript
// Server Component with fetch
async function getData() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 }, // Revalidate every hour
  });
  
  if (!res.ok) throw new Error('Failed to fetch');
  return res.json();
}

// With caching options
const data = await fetch(url, {
  cache: 'no-store', // Dynamic data
  // cache: 'force-cache', // Static data (default)
  // next: { revalidate: 60 }, // ISR
});
```

### Server Actions

```typescript
// app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string;
  const email = formData.get('email') as string;
  
  await db.user.create({ data: { name, email } });
  
  revalidatePath('/users');
  redirect('/users');
}

// Usage in form
<form action={createUser}>
  <input name="name" required />
  <input name="email" type="email" required />
  <button type="submit">Create</button>
</form>
```

### API Routes

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const users = await db.user.findMany();
  return NextResponse.json(users);
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  const user = await db.user.create({ data: body });
  return NextResponse.json(user, { status: 201 });
}

// Dynamic route: app/api/users/[id]/route.ts
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } }
) {
  const user = await db.user.findUnique({ where: { id: params.id } });
  if (!user) {
    return NextResponse.json({ error: 'Not found' }, { status: 404 });
  }
  return NextResponse.json(user);
}
```

### Loading & Error States

```typescript
// app/users/loading.tsx
export default function Loading() {
  return <div>Loading...</div>;
}

// app/users/error.tsx
'use client';

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

### Middleware

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const token = request.cookies.get('token');
  
  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*'],
};
```

### Metadata

```typescript
// Static metadata
export const metadata = {
  title: 'Page Title',
  description: 'Page description',
};

// Dynamic metadata
export async function generateMetadata({ params }) {
  const product = await getProduct(params.id);
  return {
    title: product.name,
    openGraph: { images: [product.image] },
  };
}
```

## Key Guidelines

1. Default to Server Components
2. Use 'use client' only when needed
3. Colocate data fetching with components
4. Use Server Actions for mutations
5. Implement proper loading/error states
