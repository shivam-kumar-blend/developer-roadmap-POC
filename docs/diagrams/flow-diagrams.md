# Flow Diagrams

This document contains flow diagrams showing business logic, decision trees, and data transformation pipelines.

## 1. User Authentication Decision Flow

```mermaid
flowchart TD
    Start([User Visits Page]) --> CheckAuth{Authenticated?}
    
    CheckAuth -->|Yes| ValidateToken{Token Valid?}
    CheckAuth -->|No| ShowPublic[Show Public Content]
    
    ValidateToken -->|Yes| CheckPremium{Premium User?}
    ValidateToken -->|No| Logout[Clear Token]
    Logout --> ShowPublic
    
    CheckPremium -->|Yes| ShowPremium[Show Premium Features]
    CheckPremium -->|No| ShowFree[Show Free Features]
    
    ShowPublic --> End([Page Rendered])
    ShowPremium --> End
    ShowFree --> End
```

## 2. Progress Update Logic Flow

```mermaid
flowchart TD
    Start([User Clicks Topic]) --> ShowMenu[Show Progress Menu]
    ShowMenu --> UserSelect{User Selects}
    
    UserSelect -->|Learning| SetLearning[Set Status: learning]
    UserSelect -->|Completed| SetCompleted[Set Status: done]
    UserSelect -->|Skip| SetSkipped[Set Status: skipped]
    UserSelect -->|Remove| SetRemove[Set Status: pending]
    
    SetLearning --> UpdateUI[Update UI Optimistically]
    SetCompleted --> UpdateUI
    SetSkipped --> UpdateUI
    SetRemove --> UpdateUI
    
    UpdateUI --> CallAPI[POST /v1-update-resource-progress]
    
    CallAPI --> CheckResponse{API Success?}
    
    CheckResponse -->|Yes| UpdateStats[Update Dashboard Stats]
    CheckResponse -->|No| Rollback[Rollback UI Changes]
    
    UpdateStats --> ShowSuccess[Show Success Toast]
    Rollback --> ShowError[Show Error Message]
    
    ShowSuccess --> End([Complete])
    ShowError --> End
```

## 3. AI Feature Rate Limiting Flow

```mermaid
flowchart TD
    Start([User Asks AI Question]) --> CheckAuth{Authenticated?}
    
    CheckAuth -->|No| ShowLogin[Redirect to Login]
    CheckAuth -->|Yes| CheckPremium{Premium User?}
    
    CheckPremium -->|Yes| AllowUnlimited[Process Request]
    CheckPremium -->|No| CheckLimit{Check Daily Limit}
    
    CheckLimit -->|< 10 questions| IncrementCount[Increment Counter]
    CheckLimit -->|>= 10 questions| ShowUpgrade[Show Upgrade Modal]
    
    IncrementCount --> AllowUnlimited
    
    AllowUnlimited --> ProcessAI[Send to AI Service]
    ProcessAI --> StreamResponse[Stream Response]
    
    StreamResponse --> SaveHistory[Save to History]
    SaveHistory --> End([Complete])
    
    ShowUpgrade --> End
    ShowLogin --> End
```

## 4. Content Rendering Pipeline

```mermaid
flowchart TD
    Start([Request Page]) --> LoadFile[Load Markdown File]
    
    LoadFile --> ParseFrontmatter[Parse Frontmatter]
    ParseFrontmatter --> ExtractMeta[Extract Metadata]
    
    ExtractMeta --> ProcessMD[Process Markdown]
    ProcessMD --> RemarkGFM[Remark: GitHub Flavored Markdown]
    
    RemarkGFM --> RemarkMath[Remark: Math Support]
    RemarkMath --> RehypeKatex[Rehype: Render Math]
    
    RehypeKatex --> RehypeLinks[Rehype: External Links]
    RehypeLinks --> SyntaxHighlight[Shiki: Syntax Highlighting]
    
    SyntaxHighlight --> GenerateHTML[Generate HTML]
    GenerateHTML --> InjectComponents[Inject React Components]
    
    InjectComponents --> SSR[Server-Side Render]
    SSR --> SendToClient[Send to Browser]
    
    SendToClient --> Hydrate[Hydrate Interactive Components]
    Hydrate --> End([Page Rendered])
```

## 5. Team Member Invitation Flow

```mermaid
flowchart TD
    Start([Admin Creates Team]) --> EnterEmails[Enter Member Emails]
    
    EnterEmails --> ValidateEmails{Valid Emails?}
    ValidateEmails -->|No| ShowError[Show Validation Error]
    ValidateEmails -->|Yes| CheckPremium{Premium Account?}
    
    CheckPremium -->|No| ShowUpgrade[Show Upgrade Modal]
    CheckPremium -->|Yes| CreateTeam[Create Team Record]
    
    CreateTeam --> LoopEmails[For Each Email]
    
    LoopEmails --> CheckExists{User Exists?}
    CheckExists -->|Yes| AddDirectly[Add to Team Directly]
    CheckExists -->|No| CreateInvite[Create Invitation]
    
    AddDirectly --> SendNotif[Send Notification Email]
    CreateInvite --> SendInvite[Send Invitation Email]
    
    SendNotif --> NextEmail{More Emails?}
    SendInvite --> NextEmail
    
    NextEmail -->|Yes| LoopEmails
    NextEmail -->|No| ShowSuccess[Show Team Dashboard]
    
    ShowSuccess --> End([Complete])
    ShowUpgrade --> End
    ShowError --> End
```

## 6. Roadmap Generation Decision Tree

```mermaid
flowchart TD
    Start([Generate Roadmap Request]) --> ValidateInput{Valid Input?}
    
    ValidateInput -->|No| ReturnError[Return Validation Errors]
    ValidateInput -->|Yes| CheckAuth{Authenticated?}
    
    CheckAuth -->|No| RequireAuth[Return 401 Unauthorized]
    CheckAuth -->|Yes| CheckRateLimit{Rate Limit OK?}
    
    CheckRateLimit -->|No| ReturnLimit[Return 429 Rate Limit]
    CheckRateLimit -->|Yes| DetermineLevel{User Level?}
    
    DetermineLevel -->|Beginner| BeginnerPrompt[Use Beginner Template]
    DetermineLevel -->|Intermediate| IntermediatePrompt[Use Intermediate Template]
    DetermineLevel -->|Advanced| AdvancedPrompt[Use Advanced Template]
    
    BeginnerPrompt --> BuildPrompt[Build AI Prompt]
    IntermediatePrompt --> BuildPrompt
    AdvancedPrompt --> BuildPrompt
    
    BuildPrompt --> CallAI[Call AI Service]
    CallAI --> ParseResponse{Valid Response?}
    
    ParseResponse -->|No| Retry{Retry < 3?}
    ParseResponse -->|Yes| GenerateNodes[Generate Roadmap Nodes]
    
    Retry -->|Yes| CallAI
    Retry -->|No| ReturnError
    
    GenerateNodes --> GenerateEdges[Generate Relationships]
    GenerateEdges --> AddResources[Add Curated Resources]
    
    AddResources --> SaveDB[Save to Database]
    SaveDB --> ReturnRoadmap[Return Roadmap JSON]
    
    ReturnRoadmap --> End([Complete])
    ReturnError --> End
    RequireAuth --> End
    ReturnLimit --> End
```

## 7. Content Contribution Approval Flow

```mermaid
flowchart TD
    Start([PR Submitted]) --> AutoCheck[Run CI/CD Checks]
    
    AutoCheck --> Lint{Linting Pass?}
    Lint -->|No| FailCI[Fail CI]
    Lint -->|Yes| Build{Build Success?}
    
    Build -->|No| FailCI
    Build -->|Yes| Tests{Tests Pass?}
    
    Tests -->|No| FailCI
    Tests -->|Yes| PassCI[Pass CI]
    
    FailCI --> NotifyAuthor[Notify Author]
    NotifyAuthor --> WaitFix[Wait for Fixes]
    WaitFix --> AutoCheck
    
    PassCI --> ReviewerCheck[Assign Reviewer]
    ReviewerCheck --> ContentReview{Content Quality?}
    
    ContentReview -->|Poor| RequestChanges[Request Changes]
    ContentReview -->|Good| CheckType{Contribution Type?}
    
    RequestChanges --> NotifyAuthor
    
    CheckType -->|Typo/Minor| AutoApprove[Auto Approve]
    CheckType -->|New Content| ManualReview[Manual Review Required]
    CheckType -->|Major Change| ManualReview
    
    AutoApprove --> Merge[Merge PR]
    ManualReview --> ReviewDecision{Approved?}
    
    ReviewDecision -->|No| RequestChanges
    ReviewDecision -->|Yes| Merge
    
    Merge --> Deploy[Trigger Deployment]
    Deploy --> SyncContent[Sync to Database]
    
    SyncContent --> InvalidateCache[Invalidate CDN Cache]
    InvalidateCache --> End([Content Live])
```

## 8. Project Difficulty Recommendation

```mermaid
flowchart TD
    Start([User Views Projects]) --> CheckProgress{Has Progress?}
    
    CheckProgress -->|No| ShowAll[Show All Projects]
    CheckProgress -->|Yes| AnalyzeProgress[Analyze Completed Topics]
    
    AnalyzeProgress --> CalcLevel{Calculate Level}
    
    CalcLevel -->|0-25% progress| RecommendBeginner[Recommend Beginner Projects]
    CalcLevel -->|26-70% progress| RecommendIntermediate[Recommend Intermediate Projects]
    CalcLevel -->|71-100% progress| RecommendAdvanced[Recommend Advanced Projects]
    
    RecommendBeginner --> FilterProjects[Filter by Completed Topics]
    RecommendIntermediate --> FilterProjects
    RecommendAdvanced --> FilterProjects
    
    FilterProjects --> SortRelevance[Sort by Relevance]
    SortRelevance --> DisplayProjects[Display Project List]
    
    ShowAll --> DisplayProjects
    DisplayProjects --> End([Projects Shown])
```

## 9. Deployment Pipeline

```mermaid
flowchart TD
    Start([Git Push to main]) --> TriggerCI[Trigger GitHub Actions]
    
    TriggerCI --> Checkout[Checkout Code]
    Checkout --> InstallDeps[pnpm install]
    
    InstallDeps --> TypeCheck[TypeScript Check]
    TypeCheck --> TypeOK{Types OK?}
    
    TypeOK -->|No| FailBuild[Fail Build]
    TypeOK -->|Yes| RunBuild[pnpm build]
    
    RunBuild --> BuildOK{Build Success?}
    BuildOK -->|No| FailBuild
    BuildOK -->|Yes| RunTests[pnpm test:e2e]
    
    RunTests --> TestsOK{Tests Pass?}
    TestsOK -->|No| FailBuild
    TestsOK -->|Yes| DeployAWS[Deploy to AWS]
    
    DeployAWS --> DeployOK{Deploy Success?}
    DeployOK -->|No| Rollback[Automatic Rollback]
    DeployOK -->|Yes| HealthCheck[Health Check]
    
    HealthCheck --> HealthOK{Health OK?}
    HealthOK -->|No| Rollback
    HealthOK -->|Yes| InvalidateCDN[Invalidate CloudFront]
    
    InvalidateCDN --> NotifySuccess[Notify Team]
    Rollback --> NotifyFailure[Notify Team - Failure]
    
    FailBuild --> NotifyFailure
    
    NotifySuccess --> End([Deployment Complete])
    NotifyFailure --> End
```

## 10. User Onboarding Flow

```mermaid
flowchart TD
    Start([New User Registers]) --> VerifyEmail{Email Verified?}
    
    VerifyEmail -->|No| SendVerification[Send Verification Email]
    VerifyEmail -->|Yes| ShowWelcome[Show Welcome Screen]
    
    SendVerification --> WaitVerify[Wait for Verification]
    WaitVerify --> CheckVerified{Verified?}
    CheckVerified -->|Yes| ShowWelcome
    CheckVerified -->|No| Timeout{24h Timeout?}
    
    Timeout -->|Yes| ResendEmail[Resend Verification]
    Timeout -->|No| WaitVerify
    ResendEmail --> WaitVerify
    
    ShowWelcome --> PersonaPrompt[Prompt: Select Persona]
    PersonaPrompt --> UserSelects{User Selects?}
    
    UserSelects -->|Skip| DefaultDashboard[Show Default Dashboard]
    UserSelects -->|Developer| SetDeveloper[Set Persona: Developer]
    UserSelects -->|Student| SetStudent[Set Persona: Student]
    UserSelects -->|Career Switcher| SetSwitcher[Set Persona: Switcher]
    
    SetDeveloper --> RecommendRoadmaps[Recommend Roadmaps]
    SetStudent --> RecommendRoadmaps
    SetSwitcher --> RecommendRoadmaps
    
    RecommendRoadmaps --> ShowDashboard[Show Personalized Dashboard]
    DefaultDashboard --> ShowDashboard
    
    ShowDashboard --> FirstRoadmap{User Clicks Roadmap?}
    FirstRoadmap -->|Yes| ShowTutorial[Show Interactive Tutorial]
    FirstRoadmap -->|No| BrowseRoadmaps[User Browses Freely]
    
    ShowTutorial --> TutorialSteps[Explain Features]
    TutorialSteps --> MarkTopic[Prompt: Mark First Topic]
    
    MarkTopic --> UserMarks{User Marks?}
    UserMarks -->|Yes| Congratulate[Show Congratulations]
    UserMarks -->|No| SkipTutorial[User Skips Tutorial]
    
    Congratulate --> OnboardingComplete[Onboarding Complete]
    SkipTutorial --> OnboardingComplete
    BrowseRoadmaps --> OnboardingComplete
    
    OnboardingComplete --> End([User Active])
```

## 11. Search and Indexing Flow

```mermaid
flowchart TD
    Start([Build Process]) --> GatherContent[Gather All Content]
    
    GatherContent --> ParseRoadmaps[Parse Roadmap Files]
    ParseRoadmaps --> ParseGuides[Parse Guide Files]
    ParseGuides --> ParseBestPractices[Parse Best Practices]
    ParseBestPractices --> ParseProjects[Parse Projects]
    
    ParseProjects --> BuildIndex[Build Search Index]
    BuildIndex --> TokenizeText[Tokenize Text]
    TokenizeText --> RemoveStopWords[Remove Stop Words]
    RemoveStopWords --> StemWords[Stem Words]
    
    StemWords --> CreateInvertedIndex[Create Inverted Index]
    CreateInvertedIndex --> CalculateScores[Calculate TF-IDF Scores]
    
    CalculateScores --> SerializeIndex[Serialize Index to JSON]
    SerializeIndex --> CompressIndex[Compress Index]
    
    CompressIndex --> BundleWithApp[Bundle with Application]
    BundleWithApp --> DeployIndex[Deploy to CDN]
    
    DeployIndex --> RuntimeSearch[Runtime: User Searches]
    RuntimeSearch --> LoadIndex[Load Index in Browser]
    
    LoadIndex --> QueryIndex[Query Index]
    QueryIndex --> RankResults[Rank Results by Score]
    RankResults --> DisplayResults[Display to User]
    
    DisplayResults --> End([Search Complete])
```

## 12. Error Handling and Retry Logic

```mermaid
flowchart TD
    Start([API Call Initiated]) --> MakeRequest[Make HTTP Request]
    
    MakeRequest --> CheckResponse{Response Status}
    
    CheckResponse -->|200 OK| ParseData[Parse Response Data]
    CheckResponse -->|401 Unauthorized| ClearAuth[Clear Auth Token]
    CheckResponse -->|403 Forbidden| ShowUpgrade[Show Upgrade Modal]
    CheckResponse -->|404 Not Found| Show404[Show Not Found Error]
    CheckResponse -->|429 Rate Limit| ShowRateLimit[Show Rate Limit Message]
    CheckResponse -->|500-599 Server Error| CheckRetry{Retry Count?}
    CheckResponse -->|Network Error| CheckRetry
    
    ClearAuth --> RedirectLogin[Redirect to Login]
    
    CheckRetry -->|< 3 retries| WaitBackoff[Exponential Backoff]
    CheckRetry -->|>= 3 retries| ShowError[Show Error Message]
    
    WaitBackoff --> CalculateDelay{Calculate Delay}
    CalculateDelay -->|1st retry| Wait2s[Wait 2 seconds]
    CalculateDelay -->|2nd retry| Wait4s[Wait 4 seconds]
    CalculateDelay -->|3rd retry| Wait8s[Wait 8 seconds]
    
    Wait2s --> MakeRequest
    Wait4s --> MakeRequest
    Wait8s --> MakeRequest
    
    ParseData --> Success[Return Data]
    
    Success --> End([Complete])
    ShowError --> End
    Show404 --> End
    ShowUpgrade --> End
    ShowRateLimit --> End
    RedirectLogin --> End
```

## 13. Progressive Web App Update Flow

```mermaid
flowchart TD
    Start([User Visits Site]) --> CheckSW{Service Worker?}
    
    CheckSW -->|Not Installed| InstallSW[Install Service Worker]
    CheckSW -->|Installed| CheckUpdate{Update Available?}
    
    InstallSW --> CacheAssets[Cache Static Assets]
    CacheAssets --> ActivateSW[Activate Service Worker]
    ActivateSW --> LoadPage[Load Page]
    
    CheckUpdate -->|No| LoadFromCache[Load from Cache]
    CheckUpdate -->|Yes| ShowUpdateBanner[Show Update Banner]
    
    LoadFromCache --> LoadPage
    
    ShowUpdateBanner --> UserAction{User Action}
    UserAction -->|Click Refresh| UpdateSW[Update Service Worker]
    UserAction -->|Dismiss| LoadFromCache
    
    UpdateSW --> FetchNew[Fetch New Assets]
    FetchNew --> ClearOldCache[Clear Old Cache]
    ClearOldCache --> InstallNew[Install New Cache]
    InstallNew --> ReloadPage[Reload Page]
    
    LoadPage --> End([Page Loaded])
    ReloadPage --> End
```

---

For sequence diagrams, see [Sequence Diagrams](./sequence-diagrams.md).

For architecture details, see [Architect Guide](../personas/architect-guide.md).
