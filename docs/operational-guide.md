# Operational Guide

## Overview

This guide covers deployment procedures, monitoring, troubleshooting, maintenance, and operational best practices for roadmap.sh platform.

## Deployment Architecture

### Production Environment

**Infrastructure**:
- **Hosting**: AWS with Node.js SSR (Server-Side Rendering)
- **CDN**: Amazon CloudFront for static assets
- **Storage**: Amazon S3 for images and avatars
- **API Backend**: Separate backend service at `api.roadmap.sh`
- **Database**: Backend manages database (not in this repository)

**Deployment Strategy**:
- **Blue-Green Deployment**: Zero-downtime deployments
- **Automated Rollback**: Automatic rollback on failure
- **Canary Releases**: Gradual traffic shifting for major changes

### Environments

1. **Local Development**
   - URL: `http://localhost:3000`
   - API: `https://api.roadmap.sh` (production API)
   - Purpose: Local development and testing

2. **Production**
   - URL: `https://roadmap.sh`
   - API: `https://api.roadmap.sh`
   - CDN: CloudFront distribution
   - Purpose: Live production environment

## Deployment Procedures

### Automated Deployment (CI/CD)

The project uses GitHub Actions for automated deployments. Workflow: `.github/workflows/deployment.yml`

**Deployment Trigger**:
- Push to `main` branch
- Manual workflow dispatch

**Deployment Steps**:
1. Checkout code
2. Install dependencies (`pnpm install`)
3. Run build (`pnpm build`)
4. Run E2E tests (`pnpm test:e2e`)
5. Deploy to AWS
6. Invalidate CloudFront cache
7. Verify deployment health

**Environment Variables** (Set in GitHub Secrets):
```bash
AWS_ACCESS_KEY_ID          # AWS credentials
AWS_SECRET_ACCESS_KEY      # AWS credentials
AWS_REGION                 # AWS region
PUBLIC_API_URL             # Backend API URL
CLOUDFRONT_DISTRIBUTION_ID # CDN distribution
```

### Manual Deployment

If needed, you can deploy manually:

```bash
# 1. Build the application
pnpm build

# 2. Test the build locally
pnpm preview

# 3. Deploy to AWS (requires AWS CLI configured)
# (Specific deployment commands depend on your AWS setup)
```

### Cache Invalidation

After deployment, invalidate CloudFront caches:

**Frontend Cache**:
```bash
# Workflow: .github/workflows/cloudfront-fe-cache.yml
# Manually trigger via GitHub Actions UI
```

**API Cache**:
```bash
# Workflow: .github/workflows/cloudfront-api-cache.yml
# Manually trigger via GitHub Actions UI
```

**What to Invalidate**:
- HTML pages: Always invalidate
- JavaScript/CSS: Fingerprinted (auto-cache busted)
- Images: Only if modified
- JSON data: Invalidate if content changed

## Monitoring and Alerting

### Application Monitoring

**Microsoft Clarity**:
- **Purpose**: User behavior analytics, session recordings
- **Configuration**: `src/components/Analytics/`
- **Access**: Clarity dashboard
- **Metrics**:
  - Page views and sessions
  - User interactions (clicks, scrolls)
  - Error tracking
  - Performance metrics

**Custom Analytics**:
- Page visit tracking: `src/components/PageVisit/`
- Progress tracking events
- Feature usage metrics

### Performance Monitoring

**Key Metrics**:
- **Page Load Time**: < 3 seconds (target)
- **Time to Interactive (TTI)**: < 5 seconds (target)
- **First Contentful Paint (FCP)**: < 1.5 seconds (target)
- **Largest Contentful Paint (LCP)**: < 2.5 seconds (target)

**Monitoring Tools**:
- Lighthouse CI (automated in GitHub Actions)
- Browser DevTools Performance tab
- Microsoft Clarity performance insights

### Error Tracking

**Client-Side Errors**:
- Console errors logged to analytics
- User-reported issues via feedback form
- Session recordings in Clarity

**Server-Side Errors**:
- AWS CloudWatch logs
- Application logs (stdout/stderr)
- HTTP 5xx error rates

### Uptime Monitoring

**Health Check Endpoint**:
```bash
GET /v1-health
```

**Expected Response**:
```json
{
  "status": "ok",
  "timestamp": "2024-02-02T12:00:00Z"
}
```

**Monitoring**:
- External uptime monitor (recommended: UptimeRobot, Pingdom)
- Check interval: 5 minutes
- Alert on: 3 consecutive failures

## Troubleshooting Guide

### Common Issues

#### 1. Build Failures

**Symptom**: GitHub Actions build fails

**Diagnosis**:
```bash
# Check build logs in GitHub Actions
# Common causes:
# - TypeScript errors
# - Missing dependencies
# - Environment variable issues
```

**Resolution**:
```bash
# Fix TypeScript errors
pnpm astro sync
pnpm build

# Clear cache and rebuild
rm -rf node_modules .astro dist
pnpm install
pnpm build
```

#### 2. Deployment Failures

**Symptom**: Deployment workflow fails

**Diagnosis**:
- Check GitHub Actions logs
- Verify AWS credentials
- Check AWS CloudWatch logs

**Resolution**:
```bash
# Verify AWS credentials are valid
# Re-run deployment workflow
# Check for AWS service outages

# Manual rollback if needed:
# 1. Revert last commit
# 2. Push to main branch
# 3. Wait for automated deployment
```

#### 3. Page Not Loading / 404 Errors

**Symptom**: Users report pages not loading

**Diagnosis**:
```bash
# Check if route exists in src/pages/
# Verify build includes the page
# Check CloudFront distribution
```

**Resolution**:
```bash
# Invalidate CloudFront cache
# Verify routing in astro.config.mjs
# Check for redirect rules
```

#### 4. API Connection Errors

**Symptom**: "Failed to fetch" errors in console

**Diagnosis**:
```bash
# Check PUBLIC_API_URL environment variable
# Verify API backend is running
# Check CORS configuration
# Verify network connectivity
```

**Resolution**:
```bash
# Verify backend API health:
curl https://api.roadmap.sh/v1-health

# Check environment variables in deployment
# Verify API credentials (JWT token)
```

#### 5. Slow Page Load

**Symptom**: Pages load slowly

**Diagnosis**:
```bash
# Run Lighthouse audit
pnpm playwright test --project=chromium --headed

# Check Network tab in DevTools
# Identify large resources
```

**Resolution**:
```bash
# Compress images
pnpm compress:images

# Check bundle size
pnpm build
# Analyze dist/ folder sizes

# Optimize components:
# - Add lazy loading
# - Code split large bundles
# - Enable caching
```

#### 6. Progress Not Saving

**Symptom**: User progress not persisting

**Diagnosis**:
```bash
# Check browser console for errors
# Verify authentication (JWT token)
# Check API response in Network tab
```

**Resolution**:
```bash
# Clear browser localStorage
# Re-login
# Verify API endpoint is working
# Check backend logs
```

### Emergency Procedures

#### Rollback to Previous Version

```bash
# 1. Identify last working commit
git log --oneline

# 2. Revert to that commit
git revert <commit-hash>

# 3. Push to trigger deployment
git push origin main

# 4. Monitor deployment
# GitHub Actions will automatically deploy
```

#### Enable Maintenance Mode

If you need to take the site offline for maintenance:

```bash
# 1. Create maintenance page in src/pages/
# 2. Update Astro config to redirect all routes
# 3. Deploy
# 4. Invalidate cache
```

## Maintenance Procedures

### Regular Maintenance Tasks

#### Daily
- ✅ Monitor error rates in Clarity
- ✅ Check uptime status
- ✅ Review user feedback

#### Weekly
- ✅ Review GitHub Actions workflow runs
- ✅ Check for failed deployments
- ✅ Monitor CloudFront cache hit rates
- ✅ Review performance metrics

#### Monthly
- ✅ Update dependencies
- ✅ Review and compress large images
- ✅ Audit accessibility compliance
- ✅ Review and update content
- ✅ Database backup verification (backend team)

#### Quarterly
- ✅ Security audit
- ✅ Performance optimization review
- ✅ Infrastructure cost analysis
- ✅ Disaster recovery drill

### Dependency Updates

**Automated Updates**:
- GitHub workflow: `.github/workflows/upgrade-dependencies.yml`
- Runs weekly
- Creates PR with dependency updates

**Manual Updates**:
```bash
# Check for updates
pnpm upgrade

# Or use npm-check-updates
pnpm upgrade-deps
# This runs: ncu -u

# Test updates
pnpm install
pnpm build
pnpm test:e2e

# Commit and push
git add package.json pnpm-lock.yaml
git commit -m "chore: update dependencies"
git push
```

**Critical Dependencies**:
- **Astro**: Major version updates require testing
- **React**: Review breaking changes
- **TailwindCSS**: Check for design system impacts
- **Playwright**: Update test snapshots if needed

### Image Optimization

```bash
# Compress all images in public/
pnpm compress:images

# This will:
# - Optimize PNG/JPG images
# - Generate WebP versions
# - Update compressed-images.json
# - Reduce bundle size
```

### Content Sync

**Sync Content to Repository**:
```bash
pnpm sync:content-to-repo
# Workflow: .github/workflows/sync-content-to-repo.yml
```

**Sync Repository to Database**:
```bash
pnpm sync:repo-to-database
# Workflow: .github/workflows/sync-repo-to-database.yml
```

**When to Sync**:
- After major content updates
- After merging community contributions
- Before major releases

## Backup and Recovery

### Backup Strategy

**Code and Configuration**:
- **Source**: GitHub repository (primary backup)
- **Frequency**: Continuous (version control)
- **Retention**: Indefinite

**Content**:
- **Source**: Markdown files in `/src/data/`
- **Backup**: Git repository + database backup (backend)
- **Frequency**: Continuous (version control)
- **Retention**: Indefinite

**User Data** (Backend Responsibility):
- User accounts, progress, teams
- Backup handled by backend service
- Daily automated backups
- Point-in-time recovery available

**Static Assets**:
- **Source**: `/public/` directory and S3
- **Backup**: S3 versioning enabled
- **Frequency**: Continuous
- **Retention**: 30 days

### Disaster Recovery

**Scenario 1: Complete Infrastructure Failure**

1. **Immediate Actions**:
   - Verify scope of outage
   - Communicate with users (status page)
   - Assess AWS service health

2. **Recovery Steps**:
   - Deploy to backup AWS account/region
   - Update DNS to point to new infrastructure
   - Restore database from latest backup (backend team)
   - Verify functionality

3. **Validation**:
   - Test critical user flows
   - Verify data integrity
   - Monitor error rates

**Recovery Time Objective (RTO)**: < 4 hours
**Recovery Point Objective (RPO)**: < 1 hour

**Scenario 2: Data Corruption**

1. **Immediate Actions**:
   - Identify affected data scope
   - Disable write operations if needed

2. **Recovery Steps**:
   - Restore from git history (content)
   - Restore from database backup (user data)
   - Re-run content sync scripts
   - Validate data integrity

3. **Validation**:
   - Verify restored data
   - Test affected features
   - Communicate with affected users

## Performance Tuning

### Frontend Optimization

**Code Splitting**:
```astro
---
// Lazy load heavy components
const MyHeavyComponent = () => import('../components/MyHeavyComponent');
---

<MyHeavyComponent client:visible />
```

**Image Optimization**:
- Use WebP format with fallbacks
- Implement lazy loading
- Use responsive images
- Compress images via build script

**Caching Strategy**:
```javascript
// Service worker caching (if implemented)
// Cache static assets aggressively
// Cache API responses with TTL
```

### Backend Optimization (API)

**Rate Limiting**:
- Prevent abuse of AI features
- Throttle by user/IP
- Return 429 Too Many Requests

**Database Query Optimization**:
- Index frequently queried fields
- Use pagination for large result sets
- Cache expensive queries

**CDN Configuration**:
- Cache static assets: 1 year
- Cache HTML: 5 minutes
- Cache API responses: 1-5 minutes
- Use compression (gzip/brotli)

## Security Considerations

### Authentication

**JWT Token Management**:
- Tokens stored in HTTP-only cookies
- Token expiration: 24 hours
- Refresh token rotation
- Logout clears cookies

**OAuth Integration**:
- Google, GitHub, LinkedIn
- HTTPS only for OAuth callbacks
- State parameter for CSRF protection

### API Security

**CORS Configuration**:
```javascript
// Allow only roadmap.sh domains
Access-Control-Allow-Origin: https://roadmap.sh
Access-Control-Allow-Credentials: true
```

**Input Validation**:
- Sanitize user input
- Validate email formats
- Escape HTML in user-generated content
- Use parameterized queries

**Rate Limiting**:
- Per-user limits on AI features
- IP-based rate limiting
- Progressive backoff

### Content Security

**Content Security Policy (CSP)**:
```http
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' 'unsafe-inline' https://clarity.microsoft.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
```

**Sanitization**:
- Use `sanitize-html` library
- Sanitize markdown content
- Remove dangerous HTML tags
- Escape user input

## Cost Optimization

### AWS Cost Management

**Workflow**: `.github/workflows/aws-costs.yml`

**Cost Monitoring**:
- Track monthly AWS spending
- Alert on unusual spikes
- Review CloudFront data transfer costs
- Optimize S3 storage classes

**Optimization Strategies**:
- Use CloudFront caching effectively
- Compress static assets
- Use S3 Intelligent-Tiering
- Right-size compute resources
- Clean up unused resources

### CDN Optimization

**Cache Hit Ratio**:
- Target: > 90% cache hit ratio
- Monitor CloudFront metrics
- Optimize cache headers

**Data Transfer Costs**:
- Compress responses (gzip/brotli)
- Optimize images
- Minimize API calls
- Use edge caching

## Logging

### Application Logs

**Server-Side Logs**:
```bash
# AWS CloudWatch Logs
# Log groups:
# - /aws/lambda/roadmap-sh-production
# - /ecs/roadmap-sh-production
```

**Log Levels**:
- **ERROR**: Critical failures requiring immediate attention
- **WARN**: Issues that should be investigated
- **INFO**: Normal operations, important events
- **DEBUG**: Detailed debugging information (dev only)

**What to Log**:
- API requests (method, path, status, duration)
- Authentication events (login, logout, failures)
- Progress updates
- AI feature usage
- Errors with stack traces

### Log Retention

- **Production**: 30 days
- **Development**: 7 days
- **Archive**: Long-term storage in S3 (optional)

## Contact and Escalation

### On-Call Responsibilities

**Primary Contact**: Development team lead
**Secondary Contact**: DevOps engineer
**Escalation**: CTO / Technical leadership

### Incident Response

**Severity Levels**:
- **P0 (Critical)**: Site down, data loss
  - Response time: 15 minutes
  - Resolution target: 2 hours

- **P1 (High)**: Major feature broken, severe performance degradation
  - Response time: 1 hour
  - Resolution target: 8 hours

- **P2 (Medium)**: Minor feature issues, moderate performance issues
  - Response time: 4 hours
  - Resolution target: 48 hours

- **P3 (Low)**: Minor bugs, cosmetic issues
  - Response time: 1 business day
  - Resolution target: 1 week

### Communication Channels

- **Slack/Discord**: Real-time team communication
- **GitHub Issues**: Bug tracking and feature requests
- **Email**: Status updates to stakeholders
- **Status Page**: Public status updates (if available)

---

For development workflows, see [Developer Guide](./personas/developer-guide.md).

For system architecture, see [Architect Guide](./personas/architect-guide.md).
