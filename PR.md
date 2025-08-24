# Pull Request Guidelines

## Overview
Pull requests (PRs) are the primary mechanism for introducing changes to our codebase. This document outlines our standards and best practices for creating effective PRs.

## Creating a Pull Request

### Before You Start
- Ensure your branch is up to date with the main branch
- Run all tests locally to verify your changes work as expected
- Review your own code for any obvious issues

### PR Title Format
Use clear, descriptive titles following this pattern:
- `feat:` New feature implementation
- `fix:` Bug fixes
- `docs:` Documentation updates
- `refactor:` Code refactoring without functional changes
- `test:` Adding or updating tests
- `chore:` Maintenance tasks

Example: `feat: Add user authentication module`

### PR Description Template
```markdown
## Description
Brief summary of what this PR accomplishes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- List key changes
- Include relevant technical details
- Mention any dependencies affected

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
Include screenshots for UI changes

## Related Issues
Closes #(issue number)
```

## PR Size Guidelines
- Keep PRs small and focused (ideally under 400 lines)
- Large features should be broken into multiple PRs
- Each PR should represent one logical change

## Branch Naming Convention
- `feature/description` - New features
- `fix/description` - Bug fixes
- `hotfix/description` - Urgent production fixes
- `refactor/description` - Code improvements

## Review Process
1. Self-review your code before requesting reviews
2. Request reviews from at least 2 team members
3. Address all feedback constructively
4. Ensure CI/CD checks pass before merging

## Merging Strategy
- Use squash and merge for feature branches
- Keep commit history clean and meaningful
- Delete branches after merging

## Best Practices
- Keep commits atomic and well-described
- Update documentation alongside code changes
- Include tests for new functionality
- Respond to review comments within 24 hours
- Don't merge your own PR without approval