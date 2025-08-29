---
title: Branch Naming Convention
layout: default
---

# Branch Naming Convention

All branches must follow our standardized naming convention that includes the ticket number and a descriptive name.

## Branch Types

| Branch Type | Format | Purpose |
|------------|--------|---------|
| **feature/** | `feature/EQ-123-description` | New features or enhancements |
| **fix/** | `fix/EQ-456-description` | Bug fixes for existing functionality |
| **hotfix/** | `hotfix/EQ-789-description` | Critical fixes that need immediate deployment to production |
| **security/** | `security/EQ-321-description` | Security-related updates and patches |
| **compliance/** | `compliance/EQ-654-description` | Compliance and regulatory requirement implementations |
| **refactor/** | `refactor/EQ-987-description` | Code refactoring without changing functionality |
| **test/** | `test/EQ-111-description` | Test additions or modifications |
| **docs/** | `docs/EQ-222-description` | Documentation updates |
| **chore/** | `chore/EQ-333-description` | Maintenance tasks, dependency updates, configuration changes |
| **perf/** | `perf/EQ-444-description` | Performance improvements and optimizations |
| **release/** | `release/v1.2.3` | Release preparation branches (triggers automated Docker build) |

## Naming Rules

1. **Always include ticket number** - Format: `EQ-XXX` where XXX is the JIRA ticket number
2. **Use lowercase** - All branch names must be lowercase
3. **Use hyphens** - Replace spaces with hyphens in descriptions
4. **Be descriptive** - Branch names should clearly indicate their purpose
5. **Keep it concise** - Use brief but meaningful descriptions

## Examples

### Good Branch Names
```bash
feature/EQ-1234-user-authentication
fix/EQ-5678-login-validation
hotfix/EQ-9012-critical-payment-bug
security/EQ-3456-xss-vulnerability
compliance/EQ-7890-gdpr-updates
refactor/EQ-2345-payment-service
test/EQ-6789-integration-tests
docs/EQ-3210-api-documentation
chore/EQ-9876-dependency-updates
perf/EQ-5432-database-optimization
release/v2.1.0
```

### Bad Branch Names
```bash
feature/new-feature          # Missing ticket number
EQ-1234                       # Missing branch type and description
Feature/EQ-1234-Login         # Incorrect capitalization
fix/login-bug                 # Missing ticket number
feature_EQ_1234_user_auth     # Using underscores instead of hyphens
```

## Special Branches

### Protected Branches
- **main** - Production branch (never push directly)
- **staging** - Protected staging branch for pre-production validation

### Release Branches
Release branches follow a special format: `release/v{MAJOR}.{MINOR}.{PATCH}`
- Example: `release/v1.2.3`
- These branches automatically trigger Docker image builds when created
- Used for preparing and finalizing releases

## Best Practices

1. **One ticket per branch** - Each branch should address a single JIRA ticket
2. **Delete after merge** - Remove branches after successful merge to keep repository clean
3. **Update regularly** - Rebase or merge from main/staging frequently to avoid conflicts
4. **Short-lived branches** - Merge branches as soon as possible to minimize divergence