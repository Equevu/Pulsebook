# Pull Request Guidelines 

## Overview
Pull requests (PRs) are the primary mechanism for introducing changes to the Equevu fintech platform. We maintain separate repositories for our Django backend and Next.js frontend.

## Creating a Pull Request

### Before You Start

#### For Django Backend Repository
- Ensure your branch is up to date with the staging branch
- Test all changes locally first
- Document any new environment variables needed
- Document any database migrations required
- Verify all automated CI/CD checks will pass:
  - Unit tests
  - Performance analysis
  - Code linting
  - Security tests
- **Note**: PRs with failing CI/CD checks cannot be merged
- Ensure no sensitive data (API keys, credentials) in code

#### For Next.js Frontend Repository
- Ensure your branch is up to date with the main branch
- Test all changes locally first
- Document any new environment variables needed
- Run linting locally: `npm run lint`
- Run tests if available: `npm run test`
- Verify build: `npm run build`
- Ensure no API keys or secrets in code

## CI/CD Status

### For Django Backend Repository (Automated Checks)
The following checks run automatically and must pass:
- [ ] Unit tests passing
- [ ] Performance analysis passing
- [ ] Linting checks passing
- [ ] Security tests passing
- [ ] Code coverage meets threshold

**⚠️ PRs cannot be merged if any CI/CD check fails or Apidog tests are missing for API changes**

### For Next.js Frontend Repository
- [ ] Linting passed: `npm run lint`
- [ ] Build successful: `npm run build`
- [ ] Manual testing completed
- [ ] Tested on small and large screens

## Performance Impact
- [ ] No performance regressions introduced
- [ ] Database queries optimized (Django)
- [ ] Bundle size checked (Next.js)

## Deployment Notes

### Production Checklist
- [ ] All changes tested locally
- [ ] Environment variables documented and added to production checklist
- [ ] Database migrations added to production checklist (Django only)
- [ ] Production configuration changes documented
- [ ] Backward compatibility maintained
- [ ] Feature flags configured (if applicable)
- [ ] Rollback plan documented
- [ ] Coordination needed with other repository (specify if yes)
- [ ] API version compatibility verified (Frontend only)


## Screenshots (if applicable)
Include screenshots for UI changes

## Related Issues
- Closes #(issue number)
- Related Backend PR: #(PR number) - if applicable


## PR Size Guidelines
- Keep PRs small and focused (ideally under 400 lines)
- Large features should be broken into multiple PRs
- Each PR should represent one logical change

## Branch Naming Convention
- `feature/JIRA-123-description` - New features
- `fix/JIRA-456-description` - Bug fixes
- `hotfix/JIRA-789-description` - Urgent production fixes
- `security/JIRA-321-description` - Security patches
- `compliance/JIRA-654-description` - Compliance updates
- `refactor/JIRA-987-description` - Code improvements
- `test/JIRA-111-description` - Test additions or fixes
- `docs/JIRA-222-description` - Documentation updates
- `chore/JIRA-333-description` - Maintenance tasks
- `perf/JIRA-444-description` - Performance improvements
- `release/v1.2.3` - Release branches (builds Docker image)

Examples:
- `feature/JIRA-123-payment-gateway-integration`
- `fix/JIRA-456-transaction-validation-error`
- `hotfix/JIRA-789-critical-payment-bug`
- `test/JIRA-111-add-api-integration-tests`
- `chore/JIRA-333-update-dependencies`
- `release/v2.1.0` - Triggers Docker image build and tagging


## Merging Strategy

### Django Backend
- **Cannot merge if CI/CD checks fail**
- Use squash and merge for feature branches
- Keep commit history clean and meaningful
- Delete branches after merging
- Ensure all automated checks are green

### Next.js Frontend
- Ensure linting passes before merge
- Use squash and merge for feature branches
- Keep commit history clean and meaningful
- Delete branches after merging

## PR Best Practices for Fintech

### Before Submitting PR
- Ensure all tests pass with minimum 80% coverage (After Fixing)
- Run security scans and fix any vulnerabilities
- Verify no sensitive data in commits
- Document all environment variables for production checklist
- Document all migrations for production checklist
- Keep commits atomic and well-described
- Self-review for obvious issues

### PR Requirements
- Link to JIRA ticket in description
- Document any API changes and add Apidog tests with proof (screenshot/link)
- Add migration instructions if needed
- Describe what problem this PR solves
- List any breaking changes or dependencies
- For API changes: Apidog integration tests are mandatory with evidence



### Don'ts

#### Django Backend
- Don't merge with ANY failing CI/CD checks
- Don't bypass automated tests
- Don't ignore performance regression warnings
- Don't merge without security test approval
- Don't merge API changes without Apidog tests
- Don't merge your own PR without approval
- Don't ignore compliance-related feedback

#### Next.js Frontend
- Don't merge with failing lint checks
- Don't merge without testing on small screens and large screens
- Don't merge your own PR without approval
- Don't skip manual testing