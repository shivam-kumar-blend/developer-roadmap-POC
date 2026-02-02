# Functional Flow Documentation

## Business Context

roadmap.sh is an educational technology platform designed to help developers navigate their learning journey through curated, interactive roadmaps and resources. The platform addresses the challenge of information overload in tech education by providing structured learning paths.

## Core Business Objectives

1. **Democratize Developer Education**: Provide free, high-quality learning resources accessible to everyone
2. **Community-Driven Content**: Leverage collective knowledge through open-source contributions
3. **Personalized Learning**: Use AI to customize learning experiences based on individual goals
4. **Practical Skills**: Offer hands-on projects to reinforce learning
5. **Career Development**: Guide developers through career transitions and skill development

## User Personas

### 1. Aspiring Developers
- **Goal**: Learn programming from scratch
- **Needs**: Structured learning paths, beginner-friendly content
- **Journey**: Explore roadmaps → Track progress → Complete projects → Build portfolio

### 2. Professional Developers
- **Goal**: Learn new technologies or advance in current role
- **Needs**: Advanced topics, best practices, efficient learning
- **Journey**: Select roadmap → Study topics → Practice with questions → Apply in projects

### 3. Career Switchers
- **Goal**: Transition to new role (e.g., backend to DevOps)
- **Needs**: Role-specific roadmaps, skill gap identification
- **Journey**: Assess current skills → Follow transition roadmap → Track progress

### 4. Team Leaders
- **Goal**: Coordinate team learning and track progress
- **Needs**: Team dashboards, shared roadmaps, progress monitoring
- **Journey**: Create team → Assign roadmaps → Monitor progress → Provide feedback

### 5. Content Contributors
- **Goal**: Share knowledge and improve content
- **Needs**: Easy contribution process, community recognition
- **Journey**: Identify gaps → Submit contributions → Collaborate with community

## Core Functional Flows

### 1. User Registration and Onboarding

```
┌─────────────────────────────────────────────────────┐
│                  User Registration Flow              │
└─────────────────────────────────────────────────────┘

1. Landing Page Visit
   └─> Browse roadmaps (unauthenticated)
   └─> Click "Sign Up" or "Get Started"

2. Registration
   ├─> Email/Password signup
   ├─> Google OAuth
   ├─> GitHub OAuth
   └─> LinkedIn OAuth

3. Email Verification
   └─> Receive verification email
   └─> Click verification link
   └─> Account activated

4. Initial Setup (Optional)
   └─> Set user persona (role/goals)
   └─> Select areas of interest
   └─> Customize dashboard

5. Dashboard Access
   └─> View recommended roadmaps
   └─> Start tracking progress
```

**Business Rules**:
- Email verification required before full access
- Social OAuth creates account if email doesn't exist
- User persona helps personalize content recommendations
- Free tier has full access to roadmaps; premium adds team features

### 2. Roadmap Exploration and Learning

```
┌─────────────────────────────────────────────────────┐
│               Roadmap Learning Flow                  │
└─────────────────────────────────────────────────────┘

1. Discover Roadmap
   ├─> Browse all roadmaps
   ├─> Search by technology/role
   ├─> View recommendations
   └─> Explore trending roadmaps

2. View Interactive Roadmap
   └─> Visualize topics as interactive nodes
   └─> See topic relationships
   └─> Identify learning path

3. Study Individual Topic
   ├─> Click topic node
   ├─> View curated resources
   ├─> Access AI tutor for explanations
   ├─> Ask questions
   └─> Mark as completed/in-progress

4. Track Progress
   └─> Auto-save progress
   └─> View completion percentage
   └─> Update dashboard

5. Practice Knowledge
   ├─> Answer practice questions
   ├─> Take AI-generated quizzes
   └─> Complete related projects

6. Share & Collaborate
   └─> Share progress publicly
   └─> Export roadmap
   └─> Invite team members
```

**Business Rules**:
- Progress tracking available for registered users only
- Topics can be marked: Not Started, In Progress, Completed, Skipped
- AI features may have usage limits (rate limiting)
- Public profiles show anonymized progress unless user opts in

### 3. AI-Powered Custom Roadmap Generation

```
┌─────────────────────────────────────────────────────┐
│          AI Roadmap Generation Flow                  │
└─────────────────────────────────────────────────────┘

1. Initiate Custom Roadmap
   └─> Navigate to "AI Roadmap" section
   └─> Click "Generate Roadmap"

2. Provide Learning Context
   ├─> Enter goal (e.g., "Learn full-stack development")
   ├─> Specify current skill level
   ├─> Set time constraints (optional)
   └─> Add preferences (optional)

3. AI Processing
   └─> Generate personalized roadmap structure
   └─> Create topic nodes with relationships
   └─> Curate relevant resources

4. Review & Customize
   ├─> Preview generated roadmap
   ├─> Modify topics (add/remove/reorder)
   ├─> Request regeneration if needed
   └─> Save final version

5. Follow Custom Roadmap
   └─> Track progress like official roadmaps
   └─> Access AI tutor for guidance
   └─> Share with others (optional)
```

**Business Rules**:
- AI roadmap generation may require authentication
- Rate limits apply to prevent abuse
- Custom roadmaps can be made public or private
- Users can fork and modify community roadmaps

### 4. Team Collaboration Flow

```
┌─────────────────────────────────────────────────────┐
│            Team Collaboration Flow                   │
└─────────────────────────────────────────────────────┘

1. Create Team (Premium Feature)
   └─> Set team name and details
   └─> Invite members via email

2. Member Onboarding
   ├─> Receive invitation email
   ├─> Accept invitation
   └─> Join team workspace

3. Assign Roadmaps
   ├─> Team admin selects roadmaps
   ├─> Assigns to team members
   └─> Sets optional deadlines

4. Track Team Progress
   └─> View team dashboard
   └─> Monitor individual progress
   └─> Identify blockers

5. Collaborate
   ├─> Share resources within team
   ├─> Discuss topics
   └─> Schedule learning sessions
```

**Business Rules**:
- Teams feature requires premium subscription
- Team admin can manage members and roadmaps
- Members can be part of multiple teams
- Progress is shared within team workspace

### 5. Content Contribution Flow

```
┌─────────────────────────────────────────────────────┐
│          Content Contribution Flow                   │
└─────────────────────────────────────────────────────┘

1. Identify Contribution Opportunity
   ├─> Notice missing/outdated content
   ├─> Click "Contribute" on topic
   └─> Or fork repository on GitHub

2. Submit Contribution
   ├─> Via GitHub pull request (code/content)
   ├─> Via in-app contribution form (resources)
   └─> Via GitHub issues (suggestions)

3. Review Process
   └─> Community/maintainer reviews
   └─> Provide feedback
   └─> Request changes if needed

4. Approval & Merge
   └─> Contribution accepted
   └─> Merged into main content
   └─> Contributor credited

5. Content Sync
   └─> Changes deployed to production
   └─> Database updated
   └─> Users see new content
```

**Business Rules**:
- All contributions must follow contribution guidelines
- Content must be accurate and up-to-date
- Resources should be freely accessible when possible
- Contributors are credited in the repository

### 6. Project-Based Learning Flow

```
┌─────────────────────────────────────────────────────┐
│          Project-Based Learning Flow                 │
└─────────────────────────────────────────────────────┘

1. Browse Projects
   └─> View projects by difficulty/technology
   └─> Filter by roadmap/skill level

2. Select Project
   └─> Read project requirements
   └─> Review acceptance criteria
   └─> View example solutions (if available)

3. Work on Project
   ├─> Build solution independently
   ├─> Refer to related roadmap topics
   └─> Use AI tutor for guidance

4. Submit Solution
   └─> Share project link/repository
   └─> Add to public profile (optional)

5. Review & Iterate
   ├─> Get community feedback
   ├─> Review other solutions
   └─> Improve implementation
```

**Business Rules**:
- Projects are categorized by difficulty: Beginner, Intermediate, Advanced
- Solutions can be kept private or made public
- Public solutions contribute to user's portfolio
- Projects align with roadmap topics

## Integration Points

### External Systems

1. **Authentication Providers**
   - Google OAuth
   - GitHub OAuth
   - LinkedIn OAuth

2. **API Backend**
   - Public API URL: `https://api.roadmap.sh`
   - Handles user data, progress, teams, content sync

3. **Content Delivery**
   - CloudFront CDN for static assets
   - S3 for image storage

4. **Payment Processing** (Premium Features)
   - Stripe integration for subscriptions
   - Individual monthly/yearly plans

5. **AI Services**
   - Google AI (Gemini) for content generation
   - OpenAI for chat/tutor features

6. **Analytics**
   - Microsoft Clarity for user behavior
   - Custom analytics for progress tracking

## Business Rules Summary

### Content Access
- All roadmaps and content are free to view
- Progress tracking requires registration
- Premium features include teams and advanced analytics

### Progress Tracking
- Progress synced in real-time when authenticated
- Three states: In Progress, Completed, Skipped
- Public profiles optional for sharing progress

### AI Features
- Rate limiting to prevent abuse
- Quality filters for generated content
- Human review for showcase content

### Team Features
- Requires premium subscription
- Admin controls for team management
- Member progress visible to team admins

### Content Contributions
- Open-source model via GitHub
- Community review process
- All contributions must follow guidelines
- Contributors receive attribution

## Success Metrics

1. **User Engagement**
   - Daily/Monthly active users
   - Progress completion rates
   - Time spent on platform

2. **Content Quality**
   - Community contributions per month
   - Content accuracy ratings
   - Update frequency

3. **Learning Outcomes**
   - Projects completed
   - Roadmaps completed
   - Quiz pass rates

4. **Community Growth**
   - New user registrations
   - GitHub stars/forks
   - Social media engagement

5. **Business Metrics**
   - Premium subscription conversion
   - Team account growth
   - Revenue per user
