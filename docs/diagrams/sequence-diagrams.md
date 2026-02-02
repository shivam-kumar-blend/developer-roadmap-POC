# Sequence Diagrams

This document contains sequence diagrams showing the interaction flows in the roadmap.sh application.

## 1. User Registration Flow

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant Database
    participant Email

    User->>Browser: Click "Sign Up"
    Browser->>Frontend: Navigate to /signup
    Frontend->>Browser: Display registration form
    
    User->>Browser: Enter email & password
    Browser->>Frontend: Submit form
    Frontend->>Backend: POST /v1-register
    
    Backend->>Database: Check if email exists
    Database-->>Backend: Email not found
    
    Backend->>Database: Create user account
    Database-->>Backend: User created
    
    Backend->>Email: Send verification email
    Email-->>User: Verification email
    
    Backend-->>Frontend: Success response
    Frontend->>Browser: Redirect to /verification-pending
    
    User->>Email: Click verification link
    Email->>Backend: GET /verify-account?token=xxx
    Backend->>Database: Activate user account
    Database-->>Backend: Account activated
    Backend-->>Browser: Redirect to /dashboard
```

## 2. OAuth Login Flow (Google)

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant Google
    participant Database

    User->>Browser: Click "Sign in with Google"
    Browser->>Frontend: Click GoogleButton
    Frontend->>Backend: GET /auth/google
    Backend->>Google: Redirect to Google OAuth
    
    Google->>User: Show consent screen
    User->>Google: Grant permission
    
    Google->>Backend: Redirect with auth code
    Backend->>Google: Exchange code for token
    Google-->>Backend: Access token + user info
    
    Backend->>Database: Find or create user
    Database-->>Backend: User record
    
    Backend->>Backend: Generate JWT token
    Backend->>Browser: Set HTTP-only cookie
    Backend->>Browser: Redirect to /dashboard
```

## 3. Roadmap Viewing Flow

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant CDN
    participant Backend

    User->>Browser: Visit /frontend
    Browser->>Frontend: GET /frontend
    
    Frontend->>Frontend: Server-side render page
    Frontend->>Frontend: Load roadmap data from file
    
    Frontend->>Browser: Return HTML with roadmap data
    Browser->>User: Display static roadmap
    
    Browser->>Browser: Hydrate React components
    
    alt User is authenticated
        Browser->>Backend: GET /v1-get-user-resource-progress
        Backend-->>Browser: User progress data
        Browser->>Browser: Update roadmap with progress
    end
    
    User->>Browser: Click topic node
    Browser->>Browser: Display TopicDetail modal
    Browser->>Frontend: Fetch topic content
    Frontend-->>Browser: Topic markdown content
    Browser->>User: Display topic details
```

## 4. Progress Tracking Flow

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant Database
    participant Cache

    User->>Browser: Click "Mark as Completed"
    Browser->>Browser: Update UI optimistically
    
    Browser->>Backend: POST /v1-update-resource-progress
    Note over Backend: {resourceId, topicId, status: "done"}
    
    Backend->>Backend: Validate JWT token
    Backend->>Database: Update progress record
    Database-->>Backend: Success
    
    Backend->>Cache: Invalidate user progress cache
    Cache-->>Backend: Cache cleared
    
    Backend-->>Browser: Success response
    Note over Backend: {completedCount, totalCount, percentage}
    
    Browser->>Browser: Update dashboard stats
    Browser->>User: Show success toast
    
    alt Update failed
        Browser->>Browser: Rollback optimistic update
        Browser->>User: Show error message
    end
```

## 5. AI Chat Flow (Streaming)

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant AI
    participant Database

    User->>Browser: Type question and send
    Browser->>Frontend: Submit chat message
    
    Frontend->>Backend: POST /v1-chat-roadmap (SSE)
    Note over Frontend: {roadmapId, message, history}
    
    Backend->>Backend: Check rate limit
    
    alt Rate limit exceeded
        Backend-->>Frontend: 429 Too Many Requests
        Frontend->>User: "Daily limit reached"
    else Rate limit OK
        Backend->>Database: Get conversation history
        Database-->>Backend: Previous messages
        
        Backend->>AI: Send prompt with context
        Note over AI: Google Gemini API
        
        loop Stream response chunks
            AI-->>Backend: Response chunk
            Backend-->>Frontend: Stream chunk (SSE)
            Frontend->>Browser: Append to chat
            Browser->>User: Display progressive response
        end
        
        AI-->>Backend: Stream complete
        Backend->>Database: Save conversation
        Database-->>Backend: Saved
        
        Backend-->>Frontend: Stream closed
    end
```

## 6. AI Roadmap Generation Flow

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant AI
    participant Database

    User->>Browser: Fill roadmap generation form
    Note over User: Goal, level, timeframe
    Browser->>Frontend: Submit form
    
    Frontend->>Browser: Show loading state
    Frontend->>Backend: POST /v1-generate-ai-roadmap
    
    Backend->>Backend: Validate input
    Backend->>Backend: Check rate limit
    
    Backend->>AI: Generate roadmap structure
    Note over AI: "Create roadmap for: [goal]"
    AI-->>Backend: Roadmap JSON structure
    
    Backend->>AI: Generate topic resources
    AI-->>Backend: Curated resources
    
    Backend->>Database: Save custom roadmap
    Database-->>Backend: Roadmap ID
    
    Backend-->>Frontend: Roadmap data + ID
    Frontend->>Browser: Display generated roadmap
    Browser->>User: Show roadmap
    
    User->>Browser: Click "Regenerate"
    Note over User: If not satisfied
    Browser->>Frontend: Regenerate request
    Frontend->>Backend: POST /v1-regenerate-ai-roadmap
    Note over Backend: Repeat generation process
```

## 7. Team Creation and Invitation Flow

```mermaid
sequenceDiagram
    participant Admin
    participant Browser
    participant Frontend
    participant Backend
    participant Database
    participant Email
    participant Member

    Admin->>Browser: Click "Create Team"
    Browser->>Frontend: Show team creation form
    
    Admin->>Browser: Enter team details + emails
    Browser->>Frontend: Submit form
    Frontend->>Backend: POST /v1-create-team
    
    Backend->>Backend: Verify premium subscription
    
    alt Not premium
        Backend-->>Frontend: 403 Forbidden
        Frontend->>Browser: Show upgrade modal
    else Is premium
        Backend->>Database: Create team record
        Database-->>Backend: Team ID
        
        loop For each member email
            Backend->>Database: Create invitation
            Database-->>Backend: Invitation token
            Backend->>Email: Send invitation email
        end
        
        Backend-->>Frontend: Team created
        Frontend->>Browser: Redirect to team dashboard
        
        Email-->>Member: Invitation email
        Member->>Email: Click invite link
        Email->>Backend: GET /accept-invite?token=xxx
        
        Backend->>Database: Verify invitation token
        Database-->>Backend: Valid invitation
        
        Backend->>Database: Add member to team
        Database-->>Backend: Member added
        
        Backend-->>Browser: Redirect to team dashboard
    end
```

## 8. Project Submission Flow

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant Database

    User->>Browser: Browse projects
    Browser->>Frontend: GET /projects
    Frontend->>Frontend: Load project list
    Frontend-->>Browser: Display projects
    
    User->>Browser: Click project
    Browser->>Frontend: GET /projects/[projectId]
    Frontend-->>Browser: Project details
    
    User->>Browser: Work on project externally
    Note over User: Build solution
    
    User->>Browser: Submit solution link
    Browser->>Frontend: Submit project
    Frontend->>Backend: POST /v1-submit-project
    Note over Frontend: {projectId, solutionUrl, isPublic}
    
    Backend->>Backend: Validate URL
    Backend->>Database: Save submission
    Database-->>Backend: Submission ID
    
    alt isPublic = true
        Backend->>Database: Add to public profile
        Database-->>Backend: Updated profile
    end
    
    Backend-->>Frontend: Success
    Frontend->>Browser: Show success message
    Browser->>User: "Project submitted!"
```

## 9. Content Contribution Flow

```mermaid
sequenceDiagram
    participant Contributor
    participant GitHub
    participant CI
    participant Reviewer
    participant Database
    participant Frontend

    Contributor->>GitHub: Fork repository
    Contributor->>GitHub: Create branch
    Contributor->>GitHub: Edit markdown files
    Contributor->>GitHub: Commit changes
    Contributor->>GitHub: Create pull request
    
    GitHub->>CI: Trigger CI/CD
    CI->>CI: Run linting
    CI->>CI: Build application
    CI->>CI: Run tests
    CI-->>GitHub: CI status
    
    GitHub->>Reviewer: Notify reviewer
    Reviewer->>GitHub: Review changes
    
    alt Changes requested
        Reviewer->>Contributor: Request changes
        Contributor->>GitHub: Push updates
        GitHub->>CI: Re-run CI
    else Approved
        Reviewer->>GitHub: Approve PR
        Reviewer->>GitHub: Merge to main
        
        GitHub->>CI: Trigger deployment
        CI->>CI: Build production
        CI->>Frontend: Deploy to production
        
        Frontend->>Database: Sync content
        Database-->>Frontend: Content synced
        
        Frontend->>Frontend: Invalidate cache
        Note over Frontend: Users see new content
    end
```

## 10. Payment Flow (Premium Subscription)

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant Backend
    participant Stripe
    participant Database

    User->>Browser: Click "Upgrade to Premium"
    Browser->>Frontend: Navigate to /premium
    
    User->>Browser: Select plan (monthly/yearly)
    Browser->>Frontend: Click "Subscribe"
    
    Frontend->>Backend: POST /v1-create-checkout-session
    Note over Frontend: {priceId, planType}
    
    Backend->>Stripe: Create checkout session
    Stripe-->>Backend: Session URL
    
    Backend-->>Frontend: Checkout URL
    Frontend->>Browser: Redirect to Stripe
    
    Browser->>Stripe: Stripe hosted checkout
    User->>Stripe: Enter payment details
    User->>Stripe: Confirm payment
    
    Stripe->>Stripe: Process payment
    
    alt Payment successful
        Stripe->>Backend: Webhook: checkout.session.completed
        Backend->>Database: Update user to premium
        Database-->>Backend: User updated
        
        Stripe->>Browser: Redirect to success page
        Browser->>Frontend: GET /thank-you
        Frontend->>Browser: Show success message
    else Payment failed
        Stripe->>Backend: Webhook: checkout.session.failed
        Backend->>Database: Log failed payment
        
        Stripe->>Browser: Redirect to failure page
        Browser->>User: "Payment failed"
    end
```

## 11. Public Profile Viewing Flow

```mermaid
sequenceDiagram
    participant Visitor
    participant Browser
    participant Frontend
    participant Backend
    participant Database
    participant CDN

    Visitor->>Browser: Visit /u/johndoe
    Browser->>Frontend: GET /u/johndoe
    
    Frontend->>Backend: GET /v1-get-public-profile/johndoe
    Backend->>Database: Query user profile
    
    alt User not found or profile private
        Database-->>Backend: Not found
        Backend-->>Frontend: 404
        Frontend->>Browser: Show 404 page
    else Profile is public
        Database-->>Backend: User data
        Note over Database: username, avatar, progress, projects
        
        Backend-->>Frontend: Profile data
        Frontend->>Frontend: Server-side render
        
        Frontend->>CDN: Request avatar image
        CDN-->>Frontend: Avatar image
        
        Frontend-->>Browser: Rendered profile page
        Browser->>Visitor: Display public profile
        
        Browser->>Browser: Hydrate React components
        Browser->>Browser: Render activity heatmap
        Browser->>Browser: Render progress cards
    end
```

## 12. Search and Discovery Flow

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant SearchIndex

    User->>Browser: Press "Ctrl+K" or "/"
    Browser->>Browser: Open command menu
    
    User->>Browser: Type search query
    Browser->>Frontend: Search request
    
    Frontend->>SearchIndex: Query index
    Note over SearchIndex: In-memory search
    SearchIndex-->>Frontend: Matching results
    Note over SearchIndex: Roadmaps, guides, topics
    
    Frontend->>Browser: Display results
    Browser->>User: Show ranked results
    
    User->>Browser: Select result
    Browser->>Frontend: Navigate to selected page
    Frontend-->>Browser: Load page
```

---

For flow diagrams, see [Flow Diagrams](./flow-diagrams.md).

For architecture details, see [Architect Guide](../personas/architect-guide.md).
