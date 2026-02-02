# Component Catalog

## Overview

This document catalogs all React components used in the roadmap.sh application. The project uses React components as "islands" within Astro pages for selective client-side interactivity.

## Component Architecture

### Component Types

1. **UI Components** - Reusable UI elements (buttons, inputs, tooltips)
2. **Feature Components** - Feature-specific components (roadmap viewer, progress tracker)
3. **Layout Components** - Page layouts and sections
4. **Icon Components** - SVG icon components

### State Management

Components use different state management approaches:
- **Nanostores** (`@nanostores/react`) - Global state across framework boundaries
- **Zustand** - React component state
- **React Query** (`@tanstack/react-query`) - Server state and caching
- **Local State** - Component-level useState/useReducer

### Context Dependencies

⚠️ **Important**: Many components depend on specific context providers:
- **Toast**: Uses `$toastMessage` nanostore
- **Auth-related**: Uses user authentication state
- **Progress components**: Require user session context

## Core UI Components

### Tooltip

**File**: `src/components/Tooltip.tsx`

**Purpose**: Display contextual information on hover

**Props**:
```typescript
interface TooltipProps {
  children: ReactNode;        // Tooltip content
  additionalClass?: string;   // Custom CSS classes
  position?: 'right-center' | 'right-top' | 'right-bottom' | 
             'left-center' | 'left-top' | 'left-bottom' |
             'top-center' | 'top-left' | 'top-right' |
             'bottom-center' | 'bottom-left' | 'bottom-right';
}
```

**Usage**:
```tsx
<div className="group relative">
  <button>Hover me</button>
  <Tooltip position="top-center">
    This is a tooltip
  </Tooltip>
</div>
```

**Context Requirements**: None

**State Management**: None (purely presentational)

**Notes**: Requires parent to have `group` and `relative` classes

---

### Toast/Toaster

**File**: `src/components/Toast.tsx`

**Purpose**: Display temporary notifications to users

**Props**:
```typescript
// No props - uses global store
```

**Context Requirements**: 
- ⚠️ **Nanostore**: `$toastMessage` from `src/stores/toast`

**Usage**:
```tsx
// Place once in layout
<Toaster client:load />

// Trigger from anywhere
import { $toastMessage } from '../stores/toast';

$toastMessage.set({
  type: 'success',
  message: 'Successfully saved!',
});
```

**Message Types**:
- `success` - Green checkmark
- `error` - Red error icon
- `warning` - Orange warning icon
- `info` - Blue info icon
- `loading` - Spinner (won't auto-dismiss)

**Auto-dismiss**: 2.5 seconds (except loading type)

---

### Select Components

**File**: `src/components/Select.tsx`

**Purpose**: Dropdown select input built on Radix UI

**Components**:
- `Select` - Root container
- `SelectTrigger` - Button to open dropdown
- `SelectValue` - Displays selected value
- `SelectContent` - Dropdown content container
- `SelectItem` - Individual option
- `SelectGroup` - Group of options
- `SelectLabel` - Label for group
- `SelectSeparator` - Visual separator

**Usage**:
```tsx
import { 
  Select, 
  SelectTrigger, 
  SelectValue, 
  SelectContent, 
  SelectItem 
} from './Select';

<Select onValueChange={(value) => console.log(value)}>
  <SelectTrigger size="default">
    <SelectValue placeholder="Select option" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="option1">Option 1</SelectItem>
    <SelectItem value="option2">Option 2</SelectItem>
  </SelectContent>
</Select>
```

**Context Requirements**: None

**Props (SelectTrigger)**:
```typescript
{
  size?: 'sm' | 'default';
  className?: string;
  // ...plus all Radix SelectTrigger props
}
```

---

### SelectNative

**File**: `src/components/SelectNative.tsx`

**Purpose**: Native HTML select for better mobile experience

**Props**:
```typescript
interface SelectNativeProps {
  options: { value: string; label: string }[];
  value?: string;
  onChange: (value: string) => void;
  placeholder?: string;
}
```

---

### Stepper

**File**: `src/components/Stepper.tsx`

**Purpose**: Display multi-step progress indicator

**Props**:
```typescript
interface StepperProps {
  steps: string[];        // Step labels
  currentStep: number;    // 0-indexed current step
}
```

**Usage**:
```tsx
<Stepper 
  steps={['Account', 'Profile', 'Preferences']}
  currentStep={1}
/>
```

---

## Feature Components

### Roadmap Renderer

**Directory**: `src/components/EditorRoadmap/`

**Purpose**: Render interactive roadmap visualization

**Key Files**:
- `EditorRoadmap.tsx` - Main roadmap component
- `EditorRoadmap.css` - Roadmap-specific styles

**Context Requirements**:
- User authentication state (for progress tracking)
- Roadmap data loaded from file system

**Props**:
```typescript
interface EditorRoadmapProps {
  roadmapId: string;
  // Roadmap data passed from Astro
}
```

**Features**:
- Interactive node clicking
- Progress state visualization
- Topic detail modal
- Resource listings

---

### Progress Tracking Components

**Directory**: `src/components/UserProgress/`

**Components**:
- `UserProgressModal.tsx` - Modal showing user progress
- `UserProgressModalHeader.tsx` - Modal header
- `ProgressShareButton.tsx` - Share progress button
- `UserCustomProgressModal.tsx` - Custom roadmap progress
- `ModalLoader.tsx` - Loading state

**Context Requirements**:
- ⚠️ **Authentication**: User must be logged in
- Progress data from backend API

---

### AI Components

#### AI Chat (`src/components/AIChat/`)

**Purpose**: Conversational AI assistant

**Key Components**:
- `AIChat.tsx` - Main chat interface
- `AIChatMessages.tsx` - Message history
- `AIChatInput.tsx` - Input field

**Context Requirements**:
- User authentication
- Rate limiting state
- Conversation history

#### AI Roadmap Generator (`src/components/AIRoadmap/`)

**Purpose**: Generate custom roadmaps with AI

**Key Components**:
- `GenerateAIRoadmap.tsx` - Generation form
- `AIRoadmap.tsx` - Display generated roadmap
- `AIRoadmapActions.tsx` - Action buttons (regenerate, edit)

---

### Team Components

**Directory**: `src/components/Team*/`

**Components**:
- `TeamDashboard` - Team overview page
- `TeamProgressPage` - Team progress tracking
- `TeamMembers/InviteMemberPopup` - Invite modal
- `TeamSidebar` - Team navigation
- `TeamDropdown` - Team switcher

**Context Requirements**:
- Premium subscription
- Team membership data
- Admin permissions (for some actions)

---

### Dashboard Components

**Directory**: `src/components/Dashboard/`

**Purpose**: User dashboard showing progress and recommendations

**Key Components**:
- `DashboardPage.tsx` - Main dashboard container
- `PersonalDashboard.tsx` - Individual user dashboard
- `TeamDashboard.tsx` - Team dashboard
- `ProgressStack.tsx` - Progress cards stack
- `DashboardProgressCard.tsx` - Individual roadmap card
- `DashboardProjectCard.tsx` - Project card
- `RecommendedRoadmaps.tsx` - Recommendations

**Context Requirements**:
- User authentication
- User progress data
- Team data (if in team)

---

### Topic Detail Components

**Directory**: `src/components/TopicDetail/`

**Purpose**: Display detailed information about a roadmap topic

**Key Components**:
- `TopicDetail.tsx` - Main topic modal
- `TopicDetailsTabs.tsx` - Tab navigation
- `TopicProgressButton.tsx` - Mark progress
- `TopicDetailAI.tsx` - AI chat for topic
- `ContributionForm.tsx` - Submit resources

**Context Requirements**:
- Topic data (from markdown files)
- User progress state
- AI chat state

---

### Project Components

**Directory**: `src/components/Projects/`

**Purpose**: Project-based learning features

**Key Components**:
- `ProjectsList.tsx` - Browse projects
- `ProjectDetailPage.tsx` - Individual project
- `ProjectSubmissionForm.tsx` - Submit solution
- `ProjectProgress.tsx` - Track project completion

---

## Icon Components

**Directory**: `src/components/ReactIcons/`

**Purpose**: SVG icon components

**Usage Pattern**:
```tsx
import { CheckIcon } from './ReactIcons/CheckIcon';

<CheckIcon additionalClasses="h-5 w-5 text-green-500" />
```

**Available Icons** (44+ icons):
- `CheckIcon` - Checkmark
- `ErrorIcon` - Error/X icon
- `WarningIcon` - Warning triangle
- `InfoIcon` - Info circle
- `Spinner` - Loading spinner
- `UsersIcon` - Users/team icon
- `VideoIcon` - Video player icon
- And many more...

**Common Props**:
```typescript
interface IconProps {
  additionalClasses?: string;  // Tailwind classes
}
```

---

## Authentication Components

**Directory**: `src/components/AuthenticationFlow/`

**Components**:
- `EmailLoginForm.tsx` - Email/password login
- `EmailSignupForm.tsx` - Email/password signup
- `GitHubButton.tsx` - GitHub OAuth
- `GoogleButton.tsx` - Google OAuth
- `LinkedInButton.tsx` - LinkedIn OAuth
- `ForgotPasswordForm.tsx` - Password reset
- `ResetPasswordForm.tsx` - New password entry

**Context Requirements**: None (self-contained)

**Usage**: Imported in `/login`, `/signup` pages

---

## Premium/Billing Components

**Directory**: `src/components/Billing/`

**Components**:
- `BillingPage.tsx` - Billing management page
- `UpgradeAccountModal.tsx` - Upgrade prompt
- `GlobalUpgradeModal.tsx` - Global upgrade CTA

**Context Requirements**:
- User subscription status
- Stripe integration

---

## Navigation Components

**Directory**: `src/components/Navigation/`

**Components**:
- `Navigation.astro` - Main header (Astro component)
- `AccountDropdown.tsx` - User menu dropdown
- `CommandMenu.tsx` - Global search (Ctrl+K)

**Context Requirements**:
- User authentication state
- Search index data

---

## Component Guidelines

### Creating New Components

1. **File Naming**: PascalCase (e.g., `MyComponent.tsx`)
2. **Props Interface**: Define explicitly above component
3. **Type Safety**: Use TypeScript, no `any` types
4. **Exports**: Use named exports

**Template**:
```tsx
import { type FC } from 'react';

interface MyComponentProps {
  title: string;
  onClose?: () => void;
}

export function MyComponent({ title, onClose }: MyComponentProps) {
  return (
    <div className="my-component">
      <h2>{title}</h2>
      {onClose && <button onClick={onClose}>Close</button>}
    </div>
  );
}
```

### Styling Guidelines

1. **Use Tailwind**: Utility-first approach
2. **Component-specific CSS**: Only when necessary (`.css` file)
3. **Responsive**: Mobile-first with `sm:`, `md:`, `lg:` prefixes
4. **Dark Mode**: Not currently implemented

### Client Directives in Astro

When using React components in Astro pages:

```astro
---
import { MyComponent } from '../components/MyComponent';
---

<!-- Load immediately -->
<MyComponent client:load prop="value" />

<!-- Load when visible -->
<MyComponent client:visible prop="value" />

<!-- Load when idle -->
<MyComponent client:idle prop="value" />

<!-- Client-only (no SSR) -->
<MyComponent client:only="react" prop="value" />
```

### Accessibility

- Use semantic HTML
- Add ARIA labels where needed
- Ensure keyboard navigation
- Maintain color contrast
- Provide alt text for images

### Performance

- Keep components small and focused
- Use `memo` for expensive renders (sparingly)
- Lazy load heavy components
- Avoid unnecessary re-renders

---

## Testing Components

### E2E Tests (Playwright)

Located in `/tests/`:

```typescript
// tests/roadmap.spec.ts
test('topic detail opens on click', async ({ page }) => {
  await page.goto('/frontend');
  await page.click('[data-topic-id="react"]');
  await expect(page.locator('.topic-detail')).toBeVisible();
});
```

### Component Testing

Currently, no unit tests for components. Consider adding:
- React Testing Library
- Vitest for unit tests

---

## Future Enhancements

### Storybook Integration

While not currently set up, Storybook could be added for:
- Component development in isolation
- Visual regression testing
- Documentation of component states

**Setup Required**:
1. Install Storybook for React
2. Configure for Astro compatibility
3. Create stories for key components
4. Set up chromatic for visual testing

### Component Library

Consider extracting common components into a shared library:
- `@roadmap/ui` - Core UI components
- Reusable across projects
- Versioned independently

---

For design system details, see [Design System](../design-system.md).

For development guide, see [Developer Guide](../personas/developer-guide.md).
