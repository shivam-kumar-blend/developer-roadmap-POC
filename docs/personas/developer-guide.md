# Developer Guide

## Technical Architecture Overview

roadmap.sh is a modern web application built using Astro with React, providing server-side rendering (SSR) for optimal performance and SEO. This guide covers technical implementation details, architecture patterns, and development best practices.

## Technology Stack

### Frontend

- **Framework**: Astro 5.x
  - SSR with Node.js adapter
  - Islands architecture for selective hydration
  - File-based routing
  
- **UI Library**: React 19.x
  - Functional components with hooks
  - TypeScript for type safety
  - Client-side interactivity

- **Styling**: TailwindCSS 4.x
  - Utility-first CSS
  - Custom design system
  - JIT compiler for optimal bundle size

- **State Management**:
  - **Zustand**: Component-level state
  - **Nanostores**: Cross-framework reactive state
  - **React Query**: Server state and caching

### Build Tools

- **Package Manager**: pnpm with workspaces
- **Bundler**: Vite (via Astro)
- **TypeScript**: Strict mode enabled
- **Testing**: Playwright for E2E tests

### Content Management

- **Format**: Markdown with frontmatter
- **Processing**: gray-matter, remark, rehype
- **Storage**: File-based in `/src/data/`
- **Syntax Highlighting**: Shiki with Dracula theme

### Infrastructure

- **Hosting**: AWS (Node.js server)
- **CDN**: Amazon CloudFront
- **Asset Storage**: Amazon S3
- **API Backend**: Separate service (`api.roadmap.sh`)

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                        Users                             │
└────────────┬────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────┐
│              CloudFront CDN                              │
│  (Static Assets, Edge Caching, SSL/TLS)                 │
└────────────┬────────────────────────────────────────────┘
             │
        ┌────┴────┐
        │         │
        ▼         ▼
┌──────────┐  ┌──────────────────────────┐
│    S3    │  │  Astro SSR Application   │
│ (Images) │  │  (AWS - Node.js)         │
└──────────┘  └────────┬─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │  Backend API     │
              │ (api.roadmap.sh) │
              └────────┬─────────┘
                       │
                       ▼
              ┌─────────────────┐
              │    Database      │
              │  (User Data,     │
              │   Progress)      │
              └──────────────────┘
```

### Component Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   Browser                                │
├─────────────────────────────────────────────────────────┤
│  React Islands (Client-Side Interactive Components)     │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐               │
│  │ Roadmap  │ │ Progress │ │ AI Chat  │               │
│  │ Renderer │ │ Tracker  │ │ Widget   │               │
│  └──────────┘ └──────────┘ └──────────┘               │
├─────────────────────────────────────────────────────────┤
│  Static HTML (Server-Rendered)                          │
│  - Layout, Navigation, Content                          │
└─────────────────────────────────────────────────────────┘
```

## API Integration

### API Client Architecture

**Base Client** (`src/api/api.ts`):

```typescript
// API wrapper with authentication
export function api(context: APIContext) {
  const token = context.cookies.get(TOKEN_COOKIE_NAME)?.value;
  
  return {
    get: async (url, params) => {...},
    post: async (url, body) => {...},
    patch: async (url, body) => {...},
    put: async (url, body) => {...},
    delete: async (url) => {...},
  };
}
```

**Features**:
- Automatic JWT token injection from cookies
- Type-safe responses with generics
- Error handling and normalization
- Automatic 401 redirect (logout)

### Domain-Specific APIs

**Roadmap API** (`src/api/roadmap.ts`):
```typescript
export function roadmapApi(context: APIContext) {
  return {
    listShowcaseRoadmap: async () => {...},
    isShowcaseRoadmap: async (slug: string) => {...},
  };
}
```

**User API** (`src/api/user.ts`):
```typescript
export function userApi(context: APIContext) {
  return {
    getPublicProfile: async (username: string) => {...},
    // ... other methods
  };
}
```

### API Endpoints

**Base URL**: `https://api.roadmap.sh`

**Key Endpoints**:

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/v1-health` | Health check |
| GET | `/v1-roadmap-tree-mapping/:id` | Roadmap structure |
| POST | `/v1-update-resource-progress` | Update progress |
| GET | `/v1-get-user-resource-progress` | Get progress |
| POST | `/v1-generate-ai-roadmap` | Generate custom roadmap |
| POST | `/v1-chat-roadmap` | AI tutor chat |
| GET | `/v1-list-showcase-roadmap` | List public roadmaps |
| GET | `/v1-get-public-profile/:username` | User profile |

**Authentication**:
- JWT token in `Authorization: Bearer <token>` header
- Token stored in HTTP-only cookie (`__roadmapsh_jt__`)
- 24-hour expiration

**Error Responses**:
```typescript
interface AppError {
  status: number;
  message: string;
  errors?: {
    message: string;
    location: string;
  }[];
}
```

## Data Flow

### Progress Tracking Flow

```
User clicks topic → React component → 
  → useResourceProgress hook →
  → POST /v1-update-resource-progress →
  → Backend updates database →
  → Response with updated progress →
  → Update local state →
  → Re-render UI
```

**Implementation** (`src/lib/resource-progress.ts`):

```typescript
export async function updateResourceProgress(
  isCustomResource: boolean,
  progressType: ResourceProgressType,
): Promise<void> {
  await fetch(`${import.meta.env.PUBLIC_API_URL}/v1-update-resource-progress`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`,
    },
    body: JSON.stringify({
      resourceId,
      resourceType,
      topicId,
      progressType, // 'done', 'learning', 'skipped'
    }),
  });
}
```

### Content Rendering Flow

```
Request → Astro Router →
  → Page Component (.astro) →
  → Load Markdown Content →
  → Process with gray-matter →
  → Transform with remark/rehype →
  → Render to HTML →
  → Inject React Islands →
  → Send to Client
```

### AI Chat Flow

```
User types question →
  → ChatEditor component →
  → POST /v1-chat-roadmap (streaming) →
  → AI processes request →
  → Stream response chunks →
  → Display in ChatMessages →
  → Update conversation history
```

**Streaming Implementation** (`src/lib/ai.ts`):

```typescript
export async function chatWithRoadmap(
  roadmapId: string,
  message: string,
  onChunk: (chunk: string) => void,
) {
  const response = await fetch(`${API_URL}/v1-chat-roadmap`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`,
    },
    body: JSON.stringify({
      roadmapId,
      message,
      conversationHistory,
    }),
  });

  const reader = response.body?.getReader();
  // Stream and process chunks
}
```

## Component Patterns

### Astro Pages (SSR)

**File**: `src/pages/[roadmapId]/index.astro`

```astro
---
// Server-side code (runs during build/request)
import BaseLayout from '../../layouts/BaseLayout.astro';
import { RoadmapRenderer } from '../../components/Roadmaps/RoadmapRenderer';

const { roadmapId } = Astro.params;

// Fetch roadmap data server-side
const roadmapData = await getRoadmapData(roadmapId);

if (!roadmapData) {
  return Astro.redirect('/404');
}
---

<BaseLayout title={roadmapData.title}>
  <!-- Static HTML rendered server-side -->
  <h1>{roadmapData.title}</h1>
  
  <!-- Interactive React component (client-side) -->
  <RoadmapRenderer 
    client:load 
    roadmapId={roadmapId} 
    data={roadmapData} 
  />
</BaseLayout>
```

**Client Directives**:
- `client:load` - Hydrate immediately on page load
- `client:idle` - Hydrate when browser is idle
- `client:visible` - Hydrate when component is visible
- `client:only="react"` - Skip SSR, client-only render

### React Components (Interactive)

**File**: `src/components/Roadmaps/RoadmapRenderer.tsx`

```typescript
import { type FC, useEffect, useState } from 'react';
import { useStore } from '@nanostores/react';
import { $roadmapProgress } from '../../stores/roadmap';

interface RoadmapRendererProps {
  roadmapId: string;
  data: RoadmapData;
}

export const RoadmapRenderer: FC<RoadmapRendererProps> = ({
  roadmapId,
  data,
}) => {
  const progress = useStore($roadmapProgress);
  const [selectedTopic, setSelectedTopic] = useState<string | null>(null);

  useEffect(() => {
    // Fetch user progress on mount
    loadUserProgress(roadmapId);
  }, [roadmapId]);

  return (
    <div className="roadmap-renderer">
      {/* Render roadmap nodes */}
      {data.nodes.map((node) => (
        <RoadmapNode
          key={node.id}
          node={node}
          progress={progress[node.id]}
          onClick={() => setSelectedTopic(node.id)}
        />
      ))}
      
      {/* Topic detail modal */}
      {selectedTopic && (
        <TopicDetail 
          topicId={selectedTopic}
          onClose={() => setSelectedTopic(null)}
        />
      )}
    </div>
  );
};
```

### Custom Hooks

**Progress Tracking Hook**:

```typescript
// src/hooks/use-resource-progress.ts
import { useState, useCallback } from 'react';
import { updateResourceProgress } from '../lib/resource-progress';

export function useResourceProgress(resourceId: string, topicId: string) {
  const [loading, setLoading] = useState(false);
  const [progress, setProgress] = useState<ProgressType>('pending');

  const updateProgress = useCallback(async (newProgress: ProgressType) => {
    setLoading(true);
    try {
      await updateResourceProgress(resourceId, topicId, newProgress);
      setProgress(newProgress);
    } catch (error) {
      console.error('Failed to update progress:', error);
    } finally {
      setLoading(false);
    }
  }, [resourceId, topicId]);

  return { progress, loading, updateProgress };
}
```

### State Management

**Zustand Store**:

```typescript
// src/stores/toast.ts
import { create } from 'zustand';

interface ToastStore {
  message: string | null;
  type: 'success' | 'error' | 'info';
  show: (message: string, type: ToastStore['type']) => void;
  hide: () => void;
}

export const useToast = create<ToastStore>((set) => ({
  message: null,
  type: 'info',
  show: (message, type) => set({ message, type }),
  hide: () => set({ message: null }),
}));
```

**Nanostores** (Cross-Framework):

```typescript
// src/stores/page.ts
import { atom, map } from 'nanostores';

export const $currentPage = atom<string>('/');
export const $userAuth = map<{ isAuthenticated: boolean; userId?: string }>({
  isAuthenticated: false,
});

// Usage in React
import { useStore } from '@nanostores/react';
const currentPage = useStore($currentPage);

// Usage in Astro
import { $currentPage } from '../stores/page';
$currentPage.set('/dashboard');
```

## Content Management

### Markdown Processing

**Frontmatter Structure**:

```markdown
---
title: "React Hooks"
description: "Learn about React Hooks"
author: "roadmap.sh"
seo:
  title: "React Hooks - Complete Guide"
  description: "Comprehensive guide to React Hooks"
  keywords: ["react", "hooks", "useState", "useEffect"]
isNew: false
type: 'textual'
date: 2024-01-15
sitemap:
  priority: 0.7
  changefreq: 'weekly'
tags:
  - 'guide'
  - 'react'
  - 'beginner'
---

# React Hooks

Content goes here...
```

**Processing Pipeline**:

```typescript
// src/lib/markdown.ts
import matter from 'gray-matter';
import { remark } from 'remark';
import { rehype } from 'rehype';

export async function processMarkdown(filePath: string) {
  const fileContent = await fs.readFile(filePath, 'utf-8');
  
  // Parse frontmatter
  const { data: frontmatter, content } = matter(fileContent);
  
  // Process markdown to HTML
  const processedContent = await remark()
    .use(remarkGfm)  // GitHub Flavored Markdown
    .use(remarkMath) // Math support
    .process(content);
  
  const html = await rehype()
    .use(rehypeKatex)         // Render math
    .use(rehypeExternalLinks) // External link handling
    .process(processedContent);
  
  return {
    frontmatter,
    html: html.toString(),
  };
}
```

### Content Directory Structure

```
src/data/
├── roadmaps/
│   ├── frontend/
│   │   ├── frontend.md          # Roadmap metadata
│   │   ├── content/              # Topic content
│   │   │   ├── 100-internet/
│   │   │   │   ├── 100-how-does-the-internet-work.md
│   │   │   │   └── 101-what-is-http.md
│   │   │   └── 101-html/
│   │   │       ├── 100-learn-the-basics.md
│   │   │       └── ...
│   │   └── projects/             # Project-based learning
│   │       └── project-1.md
│   └── backend/
│       └── ...
├── best-practices/
│   └── code-review/
│       ├── code-review.md
│       └── content/
│           └── ...
├── guides/
│   └── guide-name.md
└── questions/
    └── javascript/
        └── question-1.md
```

### Dynamic Imports

```typescript
// Import all roadmap content files
const roadmapFiles = import.meta.glob('/src/data/roadmaps/**/*.md');

// Dynamic import specific file
const content = await roadmapFiles[`/src/data/roadmaps/${roadmapId}/content/${topicId}.md`]();
```

## Routing and Navigation

### File-Based Routing

```
src/pages/
├── index.astro                    → /
├── [roadmapId]/
│   ├── index.astro               → /frontend
│   ├── [...topicId].astro        → /frontend/react/hooks
│   └── projects.astro            → /frontend/projects
├── best-practices/
│   └── [bestPracticeId]/
│       └── index.astro           → /best-practices/code-review
├── u/
│   └── [username].astro          → /u/johndoe
└── 404.astro                     → 404 page
```

### Dynamic Routes

```astro
---
// src/pages/[roadmapId]/index.astro
const { roadmapId } = Astro.params;

// Validate roadmap exists
const roadmap = await getRoadmap(roadmapId);
if (!roadmap) {
  return Astro.redirect('/404');
}
---
```

### Redirects

Configured in `astro.config.mjs`:

```javascript
export default defineConfig({
  redirects: {
    '/devops/devops-engineer': {
      status: 301,
      destination: '/devops',
    },
    '/ai-tutor': {
      status: 301,
      destination: '/ai',
    },
  },
});
```

## Authentication

### JWT Token Flow

```
Login → Backend generates JWT →
  → Set HTTP-only cookie (__roadmapsh_jt__) →
  → Cookie sent with all requests →
  → Backend validates token →
  → Return user data / 401 if invalid
```

**Cookie Configuration**:
```typescript
// src/lib/jwt.ts
export const TOKEN_COOKIE_NAME = '__roadmapsh_jt__';

export function setAuthCookie(token: string) {
  document.cookie = `${TOKEN_COOKIE_NAME}=${token}; path=/; secure; httpOnly; sameSite=strict; max-age=86400`;
}
```

### OAuth Flow

**Google OAuth**:
```
User clicks "Sign in with Google" →
  → Redirect to Google OAuth →
  → User authorizes →
  → Google redirects to /auth/google/callback →
  → Backend creates/updates user →
  → Set JWT cookie →
  → Redirect to dashboard
```

**Providers**:
- Google: `GoogleButton.tsx`
- GitHub: `GitHubButton.tsx`
- LinkedIn: `LinkedInButton.tsx`

### Protected Routes

```astro
---
// src/pages/dashboard.astro
import { getUserFromCookie } from '../lib/jwt';

const user = await getUserFromCookie(Astro.cookies);

if (!user) {
  return Astro.redirect('/login?redirect=/dashboard');
}
---

<DashboardPage user={user} />
```

## Testing

### E2E Testing with Playwright

**Configuration**: `playwright.config.ts`

```typescript
export default defineConfig({
  testDir: './tests',
  fullyParallel: true,
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  webServer: {
    command: 'pnpm dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

**Test Example**:

```typescript
// tests/roadmap.spec.ts
import { test, expect } from '@playwright/test';

test('frontend roadmap loads correctly', async ({ page }) => {
  await page.goto('/frontend');
  
  // Wait for roadmap to render
  await page.waitForSelector('.roadmap-renderer');
  
  // Check title
  await expect(page.locator('h1')).toContainText('Frontend Developer');
  
  // Click on a topic
  await page.click('[data-topic-id="react"]');
  
  // Verify topic detail opens
  await expect(page.locator('.topic-detail')).toBeVisible();
});
```

### Visual Regression Testing

```typescript
// tests/roadmap.spec.ts
test('roadmap visual regression', async ({ page }) => {
  await page.goto('/frontend');
  await page.waitForSelector('.roadmap-renderer');
  
  // Take screenshot and compare
  await expect(page).toHaveScreenshot('roadmap-frontend.png');
});
```

## Performance Optimization

### Code Splitting

**Automatic Route-Based Splitting**:
- Each Astro page is a separate bundle
- React components imported dynamically

**Manual Code Splitting**:
```typescript
// Lazy load heavy component
const HeavyComponent = lazy(() => import('./HeavyComponent'));

<Suspense fallback={<Loading />}>
  <HeavyComponent />
</Suspense>
```

### Image Optimization

**Build-Time Compression**:
```bash
pnpm compress:images
```

**Runtime Optimization**:
```astro
---
import { Image } from 'astro:assets';
import myImage from '../images/photo.jpg';
---

<Image src={myImage} alt="Description" loading="lazy" />
```

### Caching Strategy

**Static Assets**:
- Fingerprinted filenames for cache busting
- Long-term caching (1 year)
- Served via CloudFront CDN

**API Responses**:
- Short-lived client-side cache (5 minutes)
- React Query for smart caching
- Stale-while-revalidate pattern

### Bundle Analysis

```bash
# Build and analyze bundle
pnpm build

# Check dist/ folder sizes
du -sh dist/*

# Analyze large bundles
# Look for:
# - Duplicate dependencies
# - Large vendor chunks
# - Unnecessary imports
```

## Debugging

### Development Tools

**Astro Dev Toolbar**:
- Component inspector
- Island visualization
- Audit tools

**React DevTools**:
- Component tree
- Props/state inspection
- Performance profiler

**Browser DevTools**:
- Network tab for API debugging
- Console for errors
- Performance tab for profiling

### Common Debugging Scenarios

**Component Not Hydrating**:
```astro
<!-- Check client directive is set -->
<MyComponent client:load />

<!-- Verify no SSR errors in terminal -->
```

**API Call Failing**:
```typescript
// Add logging
console.log('API URL:', import.meta.env.PUBLIC_API_URL);
console.log('Request:', { method, body });

// Check Network tab in DevTools
// Verify CORS headers
// Check authentication token
```

**Type Errors**:
```bash
# Regenerate Astro types
pnpm astro sync

# Check TypeScript errors
pnpm astro check
```

## Build and Deployment

### Production Build

```bash
# Build for production
pnpm build

# Output: dist/ directory
# - Server entry point for Node.js
# - Client assets (JS, CSS, images)
# - Pre-rendered static pages
```

### Environment Variables

**Build-Time Variables**:
```typescript
// Access in .astro and .ts files
import.meta.env.PUBLIC_API_URL
import.meta.env.PUBLIC_EDITOR_APP_URL
```

**Runtime Variables** (Server-Only):
```typescript
// Access in Astro components (server-side)
process.env.PRIVATE_API_KEY
```

### Deployment Checklist

- [ ] Run tests: `pnpm test:e2e`
- [ ] Build locally: `pnpm build`
- [ ] Check bundle size
- [ ] Verify environment variables
- [ ] Test preview: `pnpm preview`
- [ ] Update changelog
- [ ] Tag release in Git
- [ ] Deploy via GitHub Actions
- [ ] Invalidate CloudFront cache
- [ ] Verify production
- [ ] Monitor error rates

## Security Best Practices

### Input Sanitization

```typescript
import sanitizeHtml from 'sanitize-html';

// Sanitize user-generated content
const clean = sanitizeHtml(userInput, {
  allowedTags: ['b', 'i', 'em', 'strong', 'a', 'code'],
  allowedAttributes: {
    'a': ['href']
  },
});
```

### XSS Prevention

- Use React's built-in escaping
- Sanitize markdown before rendering
- Set Content Security Policy headers
- Validate user input

### CSRF Protection

- HTTP-only cookies for JWT tokens
- SameSite cookie attribute
- State parameter in OAuth flows

### Dependency Security

```bash
# Audit dependencies
pnpm audit

# Fix vulnerabilities
pnpm audit fix
```

## Contributing to Code

### Code Review Checklist

- [ ] TypeScript types are explicit
- [ ] Components are properly typed
- [ ] No console.log in production code
- [ ] Error handling implemented
- [ ] Loading states handled
- [ ] Accessibility attributes added
- [ ] Mobile-responsive design
- [ ] Tests added/updated
- [ ] Documentation updated

### Git Workflow

```bash
# Create feature branch
git checkout -b feature/my-feature

# Make changes and commit
git add .
git commit -m "feat: add new feature"

# Push to GitHub
git push origin feature/my-feature

# Create pull request
# Wait for review and CI checks
# Merge to main
```

---

For operational procedures, see [Operational Guide](../operational-guide.md).

For architectural overview, see [Architect Guide](./architect-guide.md).

For getting started, see [Developer Onboarding](../developer-onboarding.md).
