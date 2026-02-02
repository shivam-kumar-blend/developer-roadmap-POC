# roadmap.sh Documentation

> Community-driven roadmaps, articles, and resources for developers

## Overview

**roadmap.sh** is an open-source educational platform that provides interactive roadmaps, best practices, and learning resources for developers. The platform helps developers navigate their learning journey through curated, community-driven content covering various technologies, roles, and career paths.

### Key Features

- **Interactive Roadmaps**: 100+ interactive learning paths covering frontend, backend, DevOps, AI/ML, and more
- **AI-Powered Features**: 
  - AI Tutor for personalized learning assistance
  - AI-generated custom roadmaps based on user goals
  - AI-powered quiz generation
  - Interactive topic discussions
- **Progress Tracking**: Personal dashboard to track learning progress across multiple roadmaps
- **Best Practices**: Curated best practices for backend performance, frontend performance, code reviews, and API security
- **Projects**: Hands-on projects to practice skills learned from roadmaps
- **Questions**: Practice questions to test knowledge
- **Team Features**: Collaborative learning with team progress tracking and roadmap sharing
- **Community Content**: Open-source content maintained by thousands of contributors

### Technology Stack

- **Frontend Framework**: Astro 5.x with SSR (Server-Side Rendering)
- **UI Framework**: React 19.x for interactive components
- **Styling**: TailwindCSS 4.x with custom design system
- **Language**: TypeScript
- **State Management**: Zustand, Nanostores
- **API Integration**: Custom API client with JWT authentication
- **Testing**: Playwright for E2E testing
- **Build Tools**: Vite, pnpm workspace
- **Content**: 9,700+ Markdown files for roadmaps, guides, and articles
- **Deployment**: AWS with GitHub Actions CI/CD

### Live Site

🌐 **Production**: https://roadmap.sh

### Repository

📦 **GitHub**: https://github.com/kamranahmedse/developer-roadmap

## Quick Links

### For Developers
- [Developer Onboarding Guide](./developer-onboarding.md) - Get started with development
- [Developer Guide](./personas/developer-guide.md) - Technical implementation details
- [Functional Guide](./functional-guide.md) - Feature documentation

### For Architects
- [Architect Guide](./personas/architect-guide.md) - System architecture and design decisions
- [Sequence Diagrams](./diagrams/sequence-diagrams.md) - Interaction flows
- [Flow Diagrams](./diagrams/flow-diagrams.md) - Business logic flows

### For Product Owners
- [Product Owner Guide](./personas/product-owner-guide.md) - Business overview and features
- [Functional Flow](./functional-flow.md) - Business processes and user journeys

### Operations
- [Operational Guide](./operational-guide.md) - Deployment, monitoring, and troubleshooting

### Components
- [Component Catalog](./components/README.md) - UI component library
- [Design System](./design-system.md) - Design tokens and guidelines

## Project Structure

```
roadmap.sh/
├── src/
│   ├── components/        # React components (372+ components)
│   ├── pages/            # Astro pages and API routes
│   ├── data/             # Content (9,700+ markdown files)
│   ├── lib/              # Utilities and helpers
│   ├── hooks/            # React hooks
│   ├── stores/           # State management
│   ├── api/              # API client utilities
│   └── styles/           # Global styles
├── public/               # Static assets
├── scripts/              # Build and content management scripts
├── tests/                # E2E tests
└── docs/                 # Documentation (this folder)
```

## Key Statistics

- **100+** interactive roadmaps
- **9,700+** markdown content files
- **372+** React components
- **Thousands** of contributors worldwide
- **Millions** of developers reached

## Support

- **Discord**: [Join Community](https://roadmap.sh/discord)
- **Twitter/X**: [@roadmapsh](https://roadmap.sh/x)
- **YouTube**: [roadmap.sh Channel](https://roadmap.sh/youtube)
- **GitHub Issues**: [Report bugs or suggest features](https://github.com/kamranahmedse/developer-roadmap/issues)

## Contributing

We welcome contributions! Please see [contributing.md](../contributing.md) in the root directory for guidelines on:
- Adding content to roadmaps
- Creating new roadmaps
- Suggesting changes
- Code contributions

## License

See the [license file](../license) for details.
