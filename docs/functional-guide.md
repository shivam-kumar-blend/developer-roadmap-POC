# Functional Guide

## Overview

This guide provides detailed information about the features and functionality of roadmap.sh platform. It covers user-facing features, configuration options, and how different components interact.

## Core Features

### 1. Interactive Roadmaps

#### Description
Interactive, visual learning paths for 100+ technologies and career roles. Each roadmap is a node-based diagram showing topics and their relationships.

#### Key Capabilities

- **Visual Navigation**: Click nodes to explore topics
- **Progress Tracking**: Mark topics as "Not Started", "In Progress", "Completed", or "Skipped"
- **Resource Curation**: Each topic includes curated learning resources
- **Topic Details**: In-depth information for each node
- **Exportable**: Download as JSON or image

#### User Flow

1. Browse available roadmaps
2. Select a roadmap to view
3. Click on topic nodes to see details
4. Access curated resources for each topic
5. Mark progress as you learn
6. Track overall completion percentage

#### Configuration

**Available Roadmaps** (100+ total):
- **Role-based**: Frontend, Backend, DevOps, Full Stack, QA, etc.
- **Technology-specific**: React, Node.js, Python, Go, Kubernetes, etc.
- **Career-focused**: Software Architect, Engineering Manager, Product Manager
- **Domain-specific**: AI/ML, Data Science, Blockchain, Cyber Security

**Customization Options**:
- Filter by beginner/advanced levels
- Toggle between different roadmap variants
- Personalized view based on user progress
- Dark/light mode toggle

### 2. AI-Powered Features

#### AI Tutor

**Description**: Conversational AI assistant that helps users understand topics and answer questions.

**Capabilities**:
- Explain technical concepts in simple terms
- Answer follow-up questions
- Provide code examples
- Suggest learning resources
- Adapt to user's skill level

**Usage**:
```
Topic Detail Page → Ask AI Tutor → Enter question → Get response
```

**Rate Limits**: 
- Free users: 10 questions per day
- Premium users: Unlimited

#### AI Roadmap Generator

**Description**: Generate custom roadmaps based on learning goals.

**Capabilities**:
- Create personalized learning paths
- Adapt to skill level and time constraints
- Generate topic structure and resources
- Allow editing and refinement

**Input Parameters**:
- **Goal**: What you want to learn (e.g., "Become a full-stack developer")
- **Current Level**: Beginner, Intermediate, Advanced
- **Time Frame**: Optional timeline
- **Focus Areas**: Technologies or specializations

**Output**:
- Custom roadmap with nodes and relationships
- Curated resources for each topic
- Estimated learning timeline
- Progress tracking enabled

**Validation Rules**:
- Goal description: 10-500 characters
- Must specify current skill level
- Roadmap regeneration limited to 3 attempts

#### AI Quiz Generator

**Description**: Generate practice quizzes for any roadmap topic.

**Capabilities**:
- Multiple-choice questions
- Instant feedback
- Explanations for correct answers
- Difficulty adjustment

**Configuration**:
- Number of questions: 5-20
- Difficulty: Easy, Medium, Hard
- Topic selection: Single or multiple topics

### 3. Progress Tracking

#### Description
Track learning progress across multiple roadmaps with persistent storage and visualization.

#### Features

**Progress States**:
- **Not Started**: Default state
- **In Progress**: Currently learning
- **Completed**: Finished learning
- **Skipped**: Intentionally skipped

**Dashboard Views**:
- **Personal Dashboard**: Overview of all tracked roadmaps
- **Roadmap Progress**: Detailed progress per roadmap
- **Activity Heatmap**: Visual representation of daily activity
- **Streak Tracking**: Consecutive days of activity

**Data Persistence**:
- Auto-saved to backend API
- Synced across devices
- Exportable as JSON
- Publicly shareable (optional)

**Business Rules**:
- Progress tracking requires authentication
- Real-time sync with backend
- Progress visible on public profile if opted in
- Can reset progress per roadmap

### 4. Projects

#### Description
Hands-on projects to practice skills learned from roadmaps.

#### Project Structure

Each project includes:
- **Title and Description**: What to build
- **Difficulty Level**: Beginner, Intermediate, Advanced
- **Required Skills**: Technologies needed
- **Acceptance Criteria**: What defines project completion
- **Example Solutions**: Reference implementations (some projects)

#### Project Workflow

1. **Browse Projects**: Filter by difficulty, technology, or roadmap
2. **Select Project**: Review requirements and criteria
3. **Build Solution**: Implement project independently
4. **Submit**: Share link or repository
5. **Get Feedback**: Optional community review
6. **Add to Portfolio**: Display on public profile

#### Available Project Categories

- **Frontend**: Build UI components, web apps
- **Backend**: APIs, databases, microservices
- **Full Stack**: End-to-end applications
- **DevOps**: CI/CD pipelines, infrastructure
- **Data Structures**: Algorithm implementations
- **System Design**: Scalable architectures

**Validation Rules**:
- Projects aligned with roadmap topics
- Difficulty progressively increases
- Acceptance criteria clearly defined
- Solutions can be private or public

### 5. Best Practices

#### Description
Curated collections of industry best practices for specific domains.

#### Available Best Practices

- **Backend Performance**: Optimization techniques, caching, database tuning
- **Frontend Performance**: Loading optimization, rendering, bundling
- **Code Review**: Effective review strategies, feedback techniques
- **API Security**: Authentication, authorization, data protection
- **AWS**: Cloud architecture, cost optimization, security

#### Interactive Format

Best practices use the same node-based visualization as roadmaps:
- Click nodes to view detailed explanations
- Code examples and anti-patterns
- Real-world scenarios
- Implementation checklists

### 6. Questions & Quizzes

#### Description
Practice questions to test and reinforce knowledge.

#### Features

**Question Types**:
- Multiple choice
- True/False
- Code-based questions
- Scenario-based questions

**Question Sets Available**:
- JavaScript
- React
- Node.js
- Backend Development
- Frontend Development
- System Design

**Quiz Mechanics**:
- Instant feedback
- Explanations for answers
- Score tracking
- Retry unlimited times

**Progress Tracking**:
- Questions attempted
- Correct answer rate
- Topics needing review

### 7. Team Features (Premium)

#### Description
Collaborative learning and progress tracking for teams.

#### Team Management

**Creating a Team**:
1. Navigate to Teams section
2. Click "Create Team"
3. Enter team name and description
4. Invite members via email

**Roles**:
- **Admin**: Full control over team
- **Member**: View assigned roadmaps and track progress

**Member Invitation**:
- Send invitation via email
- Pending invitations tracked
- Members can belong to multiple teams

#### Team Dashboard

**Features**:
- Team member list
- Overall team progress
- Individual member progress
- Active roadmaps
- Activity timeline

**Roadmap Assignment**:
- Assign official or custom roadmaps
- Set optional deadlines
- Track completion percentage
- Monitor blockers

#### Team Analytics

- Progress distribution (members at each completion level)
- Topic completion heatmap
- Time spent per roadmap
- Most active members

**Business Rules**:
- Teams feature requires premium subscription
- Admin can add/remove members
- Members can leave teams
- Deleted teams archive data for 30 days

### 8. User Profiles

#### Description
Public and private user profiles showing learning activity and achievements.

#### Profile Components

**Public Profile** (`/u/[username]`):
- Username and avatar
- Activity heatmap (GitHub-style)
- Roadmap progress (if public)
- Completed projects
- Learning streak
- Total topics completed

**Privacy Settings**:
- Make profile public/private
- Show/hide specific roadmaps
- Show/hide projects
- Anonymous mode (progress only)

**Profile Customization**:
- Upload avatar
- Set display name
- Add bio (optional)
- Link social accounts

**Shareable Cards**:
- Generate image cards showing progress
- Embed in blog posts or social media
- Auto-updated with latest progress

### 9. Content Contribution

#### Description
Community-driven content improvement and expansion.

#### Contribution Types

**1. Resource Suggestions**:
- Add new learning resources to topics
- Submit via in-app form
- Includes title, URL, description, type

**2. Content Updates**:
- Fix typos or outdated information
- Submit via GitHub pull request
- Markdown file editing

**3. New Roadmaps**:
- Propose new roadmap ideas via GitHub issues
- Community votes on proposals
- Approved roadmaps built collaboratively

**4. Translations**:
- Translate content to other languages
- Coordinate via GitHub

#### Contribution Workflow

1. **Identify Issue**: Missing or outdated content
2. **Open Issue/PR**: Describe the change
3. **Community Review**: Feedback and suggestions
4. **Approval**: Maintainer review
5. **Merge**: Content goes live
6. **Credit**: Contributor acknowledged

**Validation Rules**:
- Resources must be freely accessible (preferred)
- Content must be accurate and up-to-date
- Follow style guide
- Include references for facts

## Feature Dependencies

### Authentication-Required Features

- Progress tracking
- AI Tutor (limited to authenticated users)
- Custom roadmaps
- Project submissions
- Team features
- Public profiles

### Premium-Only Features

- Team creation and management
- Unlimited AI questions
- Advanced analytics
- Priority support

### Public (Unauthenticated) Features

- Browse all roadmaps
- View best practices
- Read guides and articles
- Access questions (view only)
- Browse projects

## Input/Output Specifications

### Progress Update

**Input**:
```json
{
  "roadmapId": "frontend",
  "topicId": "react-hooks",
  "status": "completed"
}
```

**Output**:
```json
{
  "success": true,
  "progress": {
    "completedCount": 45,
    "totalCount": 120,
    "percentage": 37.5
  }
}
```

### AI Roadmap Generation

**Input**:
```json
{
  "goal": "Learn full-stack web development",
  "currentLevel": "beginner",
  "timeFrame": "3 months",
  "focusAreas": ["JavaScript", "React", "Node.js"]
}
```

**Output**:
```json
{
  "roadmapId": "ai-generated-uuid",
  "title": "Full-Stack Web Development",
  "nodes": [...],
  "edges": [...],
  "estimatedHours": 120
}
```

### Team Creation

**Input**:
```json
{
  "name": "Engineering Team",
  "description": "Frontend learning group",
  "members": ["user1@example.com", "user2@example.com"]
}
```

**Output**:
```json
{
  "teamId": "team-uuid",
  "invitesSent": 2,
  "invitationLinks": ["...", "..."]
}
```

## Error Handling

### Common Error Scenarios

1. **Unauthenticated Access**: Redirect to login page
2. **Rate Limit Exceeded**: Show rate limit message with reset time
3. **Premium Feature Access**: Prompt to upgrade
4. **Invalid Input**: Display validation errors inline
5. **API Failure**: Show retry option with error message

### Validation Rules

**User Input**:
- Email: Valid email format
- Password: Minimum 8 characters
- Username: 3-20 characters, alphanumeric + underscores
- Roadmap title: 5-100 characters
- Resource URL: Valid HTTP/HTTPS URL

**Content Submission**:
- Markdown: Valid syntax
- Images: Max 2MB, JPG/PNG/GIF
- External links: Must be accessible

## Performance Considerations

### Caching Strategy

- **Static Content**: Cached via CDN (CloudFront)
- **Roadmap Data**: Cached locally in browser
- **User Progress**: Real-time sync, cached for offline
- **API Responses**: Short-lived cache (5 minutes)

### Optimization

- **Image Compression**: Automatic via build scripts
- **Code Splitting**: Per-route bundles
- **Lazy Loading**: Components load on-demand
- **SSR**: Server-side rendering for SEO

## Accessibility

- **Keyboard Navigation**: Full support
- **Screen Readers**: ARIA labels on interactive elements
- **Color Contrast**: WCAG AA compliant
- **Focus Indicators**: Visible focus states
- **Alt Text**: All images have descriptive alt text

## Browser Support

- **Modern Browsers**: Chrome, Firefox, Safari, Edge (latest 2 versions)
- **Mobile**: iOS Safari, Chrome Mobile
- **JavaScript Required**: Essential for interactive features
- **Progressive Enhancement**: Basic content accessible without JS

## Feature Roadmap

Upcoming features under development:

1. **Mobile App**: Native iOS/Android apps
2. **Offline Mode**: Full offline support for authenticated users
3. **Advanced Analytics**: Learning patterns and recommendations
4. **Social Features**: Follow other learners, share achievements
5. **Certification**: Verified completion certificates
6. **Video Content**: Integrated video tutorials
7. **Live Coding**: Interactive coding exercises
8. **Mentorship**: Connect with mentors
9. **Job Board**: Career opportunities based on completed roadmaps
10. **API Access**: Public API for third-party integrations

---

For technical implementation details, see [Developer Guide](./personas/developer-guide.md).

For architectural overview, see [Architect Guide](./personas/architect-guide.md).
