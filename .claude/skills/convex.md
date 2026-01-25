# Convex Backend

This skill covers working with Convex in the IELTS Writing Band 9 project.

## Overview

Convex is the real-time database with built-in auth used for the backend.

## Commands

```bash
# Start Convex dev server (in separate terminal)
pnpm exec convex dev

# Deploy to production
pnpm exec convex deploy --prod

# View Convex dashboard
# Opens automatically at http://localhost:3001
```

## Database Schema

- `users` - User profiles and authentication (managed by Convex Auth)
- `essays` - Essay content (TipTap JSON), structure metadata, status, wordCount
- `annotations` - Sentence annotations and highlights with position references (from/to)
- `feedback` - Structure feedback and suggestions with severity levels

## Convex Imports

Convex backend uses `.js` extensions in imports even for TypeScript files:

```typescript
// In convex/ directory
import { query } from './_generated/server.js';
import { getAuthUserId } from '@convex-dev/auth/server';

// From other parts of the app (tests, src)
import { createEssay } from '#convex/essays.js';
import type { Id } from '#convex/_generated/dataModel.js';
```

## Authentication

All mutations/queries require authentication:

```typescript
const userId = await getAuthUserId(ctx);
if (userId === null) {
  throw new Error('Not authenticated');
}
```

## Authorization Pattern

Always verify user owns the resource:

```typescript
const essay = await ctx.db.get(args.essayId);
if (essay.userId !== userId) {
  throw new Error('Unauthorized');
}
```

## Using Convex in React Components

```typescript
import { useQuery, useMutation } from 'convex/react';
import { api } from '../../convex/_generated/api';

const essay = useQuery(api.essays.getEssay, { essayId });
const updateContent = useMutation(api.essays.updateEssayContent);
```

## Error Handling

Always throw descriptive errors for authentication/authorization:

```typescript
if (!userId) throw new Error('Not authenticated');
if (essay.userId !== userId) throw new Error('Unauthorized');
```

## Testing Convex Functions

```typescript
// Backend Convex function testing
import { createEssay } from '#convex/essays.js';
import { extractHandler } from '../../helpers/convexTestHelpers.ts';

const createEssayHandler = extractHandler(createEssay);
await createEssayHandler(mockContext, { title: 'Test' });
```

**Coverage Target:** 80%+ for Convex functions

## Project Structure

```
convex/
├── schema.ts            # Database schema
├── auth.config.ts       # Auth configuration
├── auth.ts              # Auth implementation
├── essays.ts            # Essay CRUD operations
├── annotations.ts       # Annotation operations
├── feedback.ts          # Feedback generation
└── _generated/          # Auto-generated types
```

## Important Files

- `convex/schema.ts` - Database schema
- `convex/essays.ts` - Essay operations

## Performance Considerations

- Convex queries: Indexed by userId for fast retrieval
