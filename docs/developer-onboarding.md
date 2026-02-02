# Developer Onboarding Guide

Welcome to the roadmap.sh development team! This guide will help you set up your development environment and understand the development workflow.

## Prerequisites

Before you begin, ensure you have the following installed:

### Required Tools

- **Node.js**: v20.x or higher ([Download](https://nodejs.org/))
- **pnpm**: v8.x or higher
  ```bash
  npm install -g pnpm
  ```
- **Git**: Latest version ([Download](https://git-scm.com/))
- **Code Editor**: VS Code recommended (with extensions listed below)

### Recommended VS Code Extensions

The project includes `.vscode/extensions.json` with recommended extensions:
- **Astro** - Official Astro language support
- **Prettier** - Code formatter
- **Tailwind CSS IntelliSense** - Tailwind class autocomplete
- **ESLint** - JavaScript/TypeScript linting

## Getting Started

### 1. Clone the Repository

```bash
git clone git@github.com:kamranahmedse/developer-roadmap.git --depth 1
cd developer-roadmap
```

### 2. Install Dependencies

The project uses pnpm workspaces. Install dependencies with:

```bash
# IMPORTANT: Use dummy editor package during development
pnpm add @roadmapsh/editor@npm:@roadmapsh/dummy-editor -w

# Install all dependencies
pnpm install
```

**Why the dummy editor?**
The production editor package is proprietary. The dummy editor provides the same interface for development without requiring access to the actual editor.

### 3. Set Up Environment Variables

Create a `.env` file in the root directory:

```bash
cp .env.example .env
```

Edit `.env` with appropriate values:

```env
# API Configuration
PUBLIC_API_URL=https://api.roadmap.sh
PUBLIC_AVATAR_BASE_URL=https://dodrc8eu8m09s.cloudfront.net/avatars
PUBLIC_EDITOR_APP_URL=https://draw.roadmap.sh
PUBLIC_COURSE_APP_URL=http://localhost:5173

# Stripe (Optional for local development)
PUBLIC_STRIPE_INDIVIDUAL_MONTHLY_PRICE_ID=
PUBLIC_STRIPE_INDIVIDUAL_YEARLY_PRICE_ID=
PUBLIC_STRIPE_INDIVIDUAL_MONTHLY_PRICE_AMOUNT=10
PUBLIC_STRIPE_INDIVIDUAL_YEARLY_PRICE_AMOUNT=100

# API Key (Optional for content scripts)
ROADMAP_API_KEY=
```

**Note**: Most features work with default values. You only need to configure Stripe and API keys if working on payment or content sync features.

### 4. Start Development Server

```bash
pnpm dev
```

The application will be available at:
- **Local**: http://localhost:3000
- **Network**: Check terminal output for network URL

### 5. Verify Installation

Open http://localhost:3000 in your browser. You should see:
- The roadmap.sh homepage
- Interactive roadmaps loading correctly
- No console errors

## Project Structure

Understanding the project structure is crucial for effective development:

```
roadmap.sh/
├── src/
│   ├── components/          # React components
│   │   ├── AccountStreak/   # User activity tracking components
│   │   ├── AIChat/          # AI chatbot components
│   │   ├── Dashboard/       # User dashboard components
│   │   ├── Navigation/      # Header, footer, navigation
│   │   ├── Roadmaps/        # Roadmap viewer components
│   │   └── ...              # 50+ component directories
│   │
│   ├── pages/               # Astro pages (file-based routing)
│   │   ├── [roadmapId]/     # Dynamic roadmap pages
│   │   ├── best-practices/  # Best practices pages
│   │   ├── projects/        # Project pages
│   │   ├── index.astro      # Homepage
│   │   └── ...              # Other routes
│   │
│   ├── data/                # Content (Markdown files)
│   │   ├── roadmaps/        # Roadmap content
│   │   ├── best-practices/  # Best practice guides
│   │   ├── guides/          # Articles and guides
│   │   └── questions/       # Practice questions
│   │
│   ├── api/                 # API client utilities
│   │   ├── api.ts           # Base API client
│   │   ├── roadmap.ts       # Roadmap API
│   │   ├── user.ts          # User API
│   │   └── ...
│   │
│   ├── lib/                 # Utility functions
│   │   ├── jwt.ts           # Authentication utilities
│   │   ├── resource-progress.ts  # Progress tracking
│   │   ├── ai.ts            # AI integration
│   │   └── ...
│   │
│   ├── hooks/               # React hooks
│   │   ├── use-toast.ts     # Toast notifications
│   │   ├── use-auth.ts      # Authentication
│   │   └── ...
│   │
│   ├── stores/              # State management (Zustand, Nanostores)
│   │   ├── page.ts          # Page-level state
│   │   └── ...
│   │
│   ├── layouts/             # Astro layouts
│   │   ├── BaseLayout.astro # Base HTML layout
│   │   └── ...
│   │
│   └── styles/              # Global styles
│       └── global.css       # Global CSS and Tailwind imports
│
├── public/                  # Static assets
│   ├── img/                 # Images
│   ├── fonts/               # Web fonts
│   └── roadmap-data/        # Roadmap JSON data
│
├── scripts/                 # Build and utility scripts
│   ├── roadmap-content.cjs  # Generate roadmap content
│   ├── compress-images.ts   # Image optimization
│   ├── generate-og-images.mjs  # Open Graph images
│   └── ...
│
├── tests/                   # E2E tests (Playwright)
│   ├── index.spec.ts        # Homepage tests
│   ├── roadmap.spec.ts      # Roadmap tests
│   └── ...
│
├── .github/                 # GitHub configuration
│   └── workflows/           # CI/CD workflows
│
├── astro.config.mjs         # Astro configuration
├── tailwind.config.cjs      # Tailwind CSS configuration
├── tsconfig.json            # TypeScript configuration
├── playwright.config.ts     # Playwright test configuration
└── pnpm-workspace.yaml      # pnpm workspace configuration
```

## Development Workflow

### Working with Content

#### Adding Content to Roadmaps

Roadmap content is stored in `src/data/roadmaps/[roadmap-id]/content/`:

1. Navigate to the roadmap directory:
   ```bash
   cd src/data/roadmaps/frontend/content
   ```

2. Add/edit Markdown files for topics

3. Use frontmatter for metadata:
   ```markdown
   ---
   title: "React Hooks"
   description: "Learn about React Hooks and how to use them"
   ---
   
   # React Hooks
   
   Content goes here...
   ```

4. The changes will be hot-reloaded automatically

#### Creating a New Roadmap

Use the roadmap scripts to scaffold a new roadmap:

```bash
# Run the roadmap dirs script
pnpm roadmap-dirs

# Or use editor roadmap tools
pnpm editor-roadmap-dirs
```

### Working with Components

#### Creating a New Component

1. Create a new directory in `src/components/`:
   ```bash
   mkdir src/components/MyFeature
   ```

2. Create your component file:
   ```tsx
   // src/components/MyFeature/MyFeature.tsx
   import { type FC } from 'react';
   
   interface MyFeatureProps {
     title: string;
   }
   
   export const MyFeature: FC<MyFeatureProps> = ({ title }) => {
     return (
       <div className="my-feature">
         <h2>{title}</h2>
       </div>
     );
   };
   ```

3. Export from index if needed:
   ```tsx
   // src/components/MyFeature/index.tsx
   export { MyFeature } from './MyFeature';
   ```

#### Using Components in Astro Pages

Components can be used in Astro files with the `client:*` directive:

```astro
---
import { MyFeature } from '../components/MyFeature/MyFeature';
---

<Layout>
  <MyFeature client:load title="Hello World" />
</Layout>
```

**Client directives**:
- `client:load` - Load immediately on page load
- `client:idle` - Load when browser is idle
- `client:visible` - Load when component enters viewport
- `client:only="react"` - Only render on client (no SSR)

### Styling with Tailwind

The project uses Tailwind CSS v4 with custom configuration:

```tsx
// Example component with Tailwind classes
export const Button = ({ children }) => {
  return (
    <button className="rounded-md bg-blue-600 px-4 py-2 text-white hover:bg-blue-700">
      {children}
    </button>
  );
};
```

**Custom utilities** are defined in `src/styles/global.css`.

### State Management

#### Using Zustand (Component State)

```tsx
import { create } from 'zustand';

interface MyStore {
  count: number;
  increment: () => void;
}

export const useMyStore = create<MyStore>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));

// In component
const { count, increment } = useMyStore();
```

#### Using Nanostores (Cross-Framework State)

```tsx
import { atom } from 'nanostores';
import { useStore } from '@nanostores/react';

export const $myValue = atom<string>('initial');

// In React component
const myValue = useStore($myValue);

// Update
$myValue.set('new value');
```

### API Integration

Use the API client utilities in `src/api/`:

```tsx
import { api } from '../api/api';

export async function getRoadmapData(context: APIContext) {
  const { response, error } = await api(context).get(
    `${import.meta.env.PUBLIC_API_URL}/v1-roadmap-data`,
  );
  
  if (error) {
    console.error('Failed to fetch roadmap:', error);
    return null;
  }
  
  return response;
}
```

## Common Development Tasks

### Running Tests

```bash
# Run all E2E tests
pnpm test:e2e

# Run tests in UI mode
pnpm playwright test --ui

# Run specific test file
pnpm playwright test tests/roadmap.spec.ts
```

### Building for Production

```bash
# Build the application
pnpm build

# Preview production build
pnpm preview
```

### Formatting Code

```bash
# Format all files
pnpm format

# Format will run Prettier with project configuration
```

### Working with Scripts

The `scripts/` directory contains utilities for content management:

```bash
# Compress images
pnpm compress:images

# Generate OG images
pnpm generate:og

# Sync content to repository
pnpm sync:content-to-repo

# Sync repository to database
pnpm sync:repo-to-database
```

## Debugging Tips

### Enable Debug Logs

Astro provides detailed logs in development mode. Check the terminal for:
- Route compilations
- Component hydration
- API calls

### Browser DevTools

- **React DevTools**: Install extension for component inspection
- **Network Tab**: Monitor API calls to `api.roadmap.sh`
- **Console**: Check for client-side errors

### Common Issues

#### Port Already in Use

```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Or use a different port
pnpm dev --port 3001
```

#### Module Not Found

```bash
# Clear node_modules and reinstall
rm -rf node_modules
pnpm install
```

#### Type Errors

```bash
# Regenerate TypeScript types
pnpm astro sync
```

## Code Style Guidelines

### TypeScript

- Use explicit types for function parameters and returns
- Prefer interfaces over types for object shapes
- Use type imports: `import type { MyType } from '...'`

### React Components

- Use functional components with TypeScript
- Define prop interfaces explicitly
- Use named exports for components

```tsx
interface MyComponentProps {
  title: string;
  onClose?: () => void;
}

export function MyComponent({ title, onClose }: MyComponentProps) {
  return <div>{title}</div>;
}
```

### File Naming

- Components: `PascalCase.tsx` (e.g., `MyComponent.tsx`)
- Utilities: `kebab-case.ts` (e.g., `my-util.ts`)
- Pages: `kebab-case.astro` (e.g., `my-page.astro`)
- Hooks: `use-*` prefix (e.g., `use-toast.ts`)

### CSS/Tailwind

- Use Tailwind utility classes first
- Create custom utilities in `global.css` if needed
- Follow mobile-first responsive design

## Getting Help

### Resources

- **Documentation**: Check `/docs` directory
- **Contributing Guide**: See `contributing.md` in root
- **GitHub Issues**: Search existing issues or create new ones
- **Community**: Join Discord for discussions

### Team Communication

- **GitHub Issues**: For bug reports and feature requests
- **Pull Requests**: For code contributions
- **Discord**: For real-time help and discussions

## Next Steps

Now that your environment is set up:

1. **Explore the Codebase**: Browse `src/components/` and `src/pages/`
2. **Read Technical Docs**: Review [Developer Guide](./personas/developer-guide.md)
3. **Pick a Task**: Check GitHub issues labeled "good first issue"
4. **Make Your First Contribution**: Start with documentation or simple bug fixes

Happy coding! 🚀
