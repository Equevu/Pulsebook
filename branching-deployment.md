---
title: Branching & Deployment Strategy
layout: default
---

# Branching & Deployment Strategy

## Branch Naming Convention

All branches must follow our standardized naming convention that includes the ticket number and a descriptive name:

### Branch Types

- **feature/EQ-123-description** - New features or enhancements
- **fix/EQ-456-description** - Bug fixes for existing functionality
- **hotfix/EQ-789-description** - Critical fixes that need immediate deployment to production
- **security/EQ-321-description** - Security-related updates and patches
- **compliance/EQ-654-description** - Compliance and regulatory requirement implementations
- **refactor/EQ-987-description** - Code refactoring without changing functionality
- **test/EQ-111-description** - Test additions or modifications
- **docs/EQ-222-description** - Documentation updates
- **chore/EQ-333-description** - Maintenance tasks, dependency updates, configuration changes
- **perf/EQ-444-description** - Performance improvements and optimizations
- **release/v1.2.3** - Release preparation branches (triggers automated Docker build)

## Branch Workflow

### Protected Branches
- **main** - Production branch
- **staging** - Protected staging branch for pre-production validation

### Working with Feature Branches
1. Create a new branch from `main` using the appropriate prefix
2. Include the JIRA/ticket number (EQ-XXX) in the branch name
3. Add a brief, descriptive suffix after the ticket number
4. Keep branch names lowercase, use hyphens for spaces

### Example Branch Names
```bash
git checkout -b feature/EQ-1234-user-authentication
git checkout -b fix/EQ-5678-login-validation
git checkout -b hotfix/EQ-9012-critical-payment-bug
git checkout -b security/EQ-3456-xss-vulnerability
git checkout -b release/v1.2.3
```

## Deployment Strategy

### Docker-Based Deployment

All deployments are triggered through Docker image builds with specific Git tags:

#### Release Process

1. **Feature Development** - Developers create feature branches and submit pull requests

2. **Staging Branch** - Approved PRs are merged into the protected `staging` branch

3. **Release Branch** - Create `release/v1.2.3` branch which automatically triggers Docker build (once only)

4. **Staging Validation** - Create prerelease tag (e.g., `v1.2.3-rc.1`) to deploy the Docker image to staging

5. **Production Release** - After validation, create release tag (e.g., `v1.2.3`) to deploy the same Docker image to production

6. **Finalization** - Merge release branch to `main` to maintain production history

### Deployment Flow

```
┌─────────────────┐
│ Feature Branch  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Pull Request   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Staging Branch  │
└────────┬────────┘
         │
         ▼
┌──────────────────────────┐
│     Release Branch       │
│     release/v1.2.3       │
│  ┌────────────────────┐  │
│  │ 🐳 Docker Build    │  │ ← Automatic on branch creation
│  │   (Once only)      │  │
│  └────────────────────┘  │
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────┐
│  Prerelease Tag      │
│  v1.2.3-rc.1         │ ← Uses Docker image from release branch
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Deploy to Staging   │
└──────────┬───────────┘
           │
           ▼
    ┌──────────────┐
    │  Validation  │
    │     Pass?    │
    └──────┬───────┘
           │
           ▼
┌──────────────────────┐
│   Release Tag        │
│   v1.2.3             │ ← Uses same Docker image
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Deploy to Production │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Merge to Main       │
└──────────────────────┘
```

## Hotfix Process

For critical production issues:

```
┌─────────────────┐
│   Main Branch   │
└────────┬────────┘
         │
         ▼
┌──────────────────────┐
│   Checkout Main      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────────┐
│    Create Hotfix Branch  │
│  hotfix/EQ-XXX-critical  │
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────────┐
│     Release Branch       │
│   release/v1.2.4-hotfix  │
│  ┌────────────────────┐  │
│  │ 🐳 Docker Build    │  │ ← Automatic on branch creation
│  │   (Once only)      │  │
│  └────────────────────┘  │
└───────────┬──────────────┘
            │
            ▼
┌──────────────────────┐
│  Prerelease Tag      │
│  v1.2.4-rc.1         │ ← Uses Docker image from release branch
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  🚀 Auto Deploy      │
│    to Staging        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Staging Test        │
│    & Confirm         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Release Tag        │
│   v1.2.4             │ ← Uses same Docker image
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 🚨 Production Deploy │
└──────────────────────┘
```

## CI/CD Pipeline Integration

### Build Triggers

| Event | Action | Environment |
|-------|--------|-------------|
| Push to feature branch | Run tests, linting | Development |
| PR to staging | Run full test suite | CI environment |
| Push to release/v*.*.* branch | Build Docker image automatically | - |
| Prerelease tag (v*.*.`*`-*) | Build Docker image, deploy | Staging |
| Release tag (v*.*.`*`) | Build Docker image, deploy | Production |

## Best Practices

1. **Always test in staging first** - Use prerelease tags for staging validation
2. **Semantic versioning** - Follow MAJOR.MINOR.PATCH convention
3. **Clean tag history** - Use meaningful version numbers
4. **Protected branches** - Never push directly to `staging` or `main`
5. **Docker image scanning** - All images scanned for vulnerabilities before deployment
6. **Rollback strategy** - Keep previous Docker images for quick rollback
7. **Tag immutability** - Never reuse or move tags

## Rollback Process

If issues are discovered after deployment:

1. **Staging Rollback**:
   - Navigate to GitHub Actions deployment history
   - Find the previous successful staging deployment
   - Click "Re-run all jobs" to redeploy the previous version

2. **Production Rollback**:
   - Navigate to GitHub Actions deployment history
   - Find the previous successful production deployment
   - Click "Re-run all jobs" to redeploy the previous version
   - Optionally create a rollback tag for tracking: `git tag v1.2.3-rollback`

## Security Considerations

- All Docker images are scanned for vulnerabilities
- Container registry requires authentication