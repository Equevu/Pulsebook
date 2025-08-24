# Code Review Guidelines

## Purpose
Code reviews ensure code quality, knowledge sharing, and maintain consistency across our codebase. Every change should be reviewed before merging.

## For Reviewers

### Review Checklist
- [ ] **Functionality**: Does the code do what it's supposed to do?
- [ ] **Design**: Is the code well-designed and appropriate for our system?
- [ ] **Complexity**: Could the code be simpler?
- [ ] **Tests**: Does the code have appropriate test coverage?
- [ ] **Naming**: Are variables, methods, and classes named clearly?
- [ ] **Comments**: Are comments clear and useful?
- [ ] **Documentation**: Is documentation updated if needed?
- [ ] **Style**: Does the code follow our style guidelines?

### What to Look For

#### Critical Issues
- Security vulnerabilities
- Performance problems
- Memory leaks
- Race conditions
- Error handling gaps
- Breaking changes

#### Code Quality
- DRY (Don't Repeat Yourself) violations
- SOLID principle violations
- Unnecessary complexity
- Magic numbers or strings
- Poor error messages
- Missing edge case handling

### Providing Feedback

#### Comment Types
- **[BLOCKING]**: Must be fixed before merge
- **[SUGGESTION]**: Consider this improvement
- **[QUESTION]**: Seeking clarification
- **[NIT]**: Minor style issue (optional fix)
- **[PRAISE]**: Highlighting good practices

#### Constructive Feedback Examples
❌ "This is wrong"
✅ "This could cause a null pointer exception. Consider adding a null check here."

❌ "Bad variable name"
✅ "Consider renaming 'data' to 'userProfileData' for clarity"

## For Authors

### Preparing for Review
- Provide context in PR description
- Highlight areas needing special attention
- Run linters and formatters
- Ensure all tests pass
- Self-review before requesting others

### Responding to Feedback
- Respond to all comments
- Mark resolved conversations
- Explain your reasoning when disagreeing
- Ask for clarification when needed
- Thank reviewers for their time

### Making Changes
- Create new commits for review feedback (don't force-push during review)
- Reference reviewer comments in commit messages when applicable
- Re-request review after addressing feedback

## Review Timeline
- **Initial review**: Within 24 hours of request
- **Follow-up reviews**: Within 12 hours
- **Urgent fixes**: Within 2 hours (mark as urgent)

## Review Tools
- Use inline comments for specific line feedback
- Use general PR comments for overall feedback
- Approve, Request Changes, or Comment appropriately
- Use suggested changes feature when possible

## Best Practices
1. **Be respectful and constructive**
2. **Focus on the code, not the person**
3. **Explain the "why" behind your suggestions**
4. **Acknowledge good solutions**
5. **Learn from the code you review**
6. **Don't approve if you don't understand**
7. **Consider the bigger picture**
8. **Balance perfectionism with pragmatism**

## Common Anti-patterns to Avoid
- Approving without thorough review
- Nitpicking on style when auto-formatters exist
- Blocking on personal preferences
- Reviewing only your area of expertise
- Delaying reviews unnecessarily
- Being overly critical or harsh

## Escalation Process
If consensus cannot be reached:
1. Discuss in team channel
2. Involve tech lead for tie-breaking
3. Document decision for future reference