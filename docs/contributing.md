# Contributing Guidelines

Thank you for your interest in contributing to the BugBounty KSP platform! This document provides guidelines and instructions for contributing.

## 🎯 Ways to Contribute

There are many ways to contribute to this project:

- 🐛 **Report bugs** - Help us identify and fix issues
- 💡 **Suggest features** - Share ideas for new functionality
- 📝 **Improve documentation** - Fix typos, add examples, clarify concepts
- 🔧 **Submit code** - Fix bugs, implement features, improve performance
- 🧪 **Write tests** - Increase test coverage
- 🎨 **Design improvements** - UI/UX enhancements
- 🌍 **Translations** - Help make the platform multilingual

## 🚀 Getting Started

### 1. Fork the Repository

Click the "Fork" button at the top right of the repository page to create your own copy.

### 2. Clone Your Fork

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
cd YOUR_REPOSITORY
```

### 3. Set Up Development Environment

Follow the [Getting Started Guide](getting-started.md) to set up your development environment.

### 4. Create a Branch

Create a new branch for your contribution:

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/your-bug-fix
```

Branch naming conventions:
- `feature/` - New features
- `fix/` - Bug fixes
- `docs/` - Documentation changes
- `refactor/` - Code refactoring
- `test/` - Test additions or changes
- `chore/` - Maintenance tasks

## 💻 Development Workflow

### Code Style

We use ESLint and Prettier to maintain consistent code style.

```bash
# Run linter
npm run lint

# Auto-fix issues
npm run lint:fix

# Format code
npm run format
```

#### Style Guidelines

**TypeScript/JavaScript:**
```typescript
// ✅ Good
export function calculateReward(severity: Severity): number {
  const rewardMap = {
    critical: 5000,
    high: 2000,
    medium: 500,
    low: 100,
  };
  return rewardMap[severity];
}

// ❌ Bad
export function calc_reward(s) {
  if (s === 'critical') return 5000;
  if (s === 'high') return 2000;
  if (s === 'medium') return 500;
  return 100;
}
```

**React Components:**
```tsx
// ✅ Good
interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  label,
  onClick,
  variant = 'primary',
  disabled = false,
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {label}
    </button>
  );
};
```

### Writing Tests

All new features and bug fixes should include tests.

```typescript
// Example unit test
describe('calculateReward', () => {
  it('should return correct reward for critical severity', () => {
    expect(calculateReward('critical')).toBe(5000);
  });

  it('should return correct reward for high severity', () => {
    expect(calculateReward('high')).toBe(2000);
  });

  it('should handle invalid severity gracefully', () => {
    expect(() => calculateReward('invalid')).toThrow();
  });
});

// Example integration test
describe('POST /api/reports', () => {
  it('should create a new report', async () => {
    const response = await request(app)
      .post('/api/reports')
      .set('Authorization', `Bearer ${token}`)
      .send({
        title: 'SQL Injection',
        severity: 'critical',
        description: 'Found SQL injection vulnerability',
      });

    expect(response.status).toBe(201);
    expect(response.body.data).toHaveProperty('id');
  });

  it('should require authentication', async () => {
    const response = await request(app)
      .post('/api/reports')
      .send({});

    expect(response.status).toBe(401);
  });
});
```

### Run Tests

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test file
npm test -- path/to/test.spec.ts

# Run in watch mode
npm run test:watch
```

### Commit Messages

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.

Format: `<type>(<scope>): <subject>`

**Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation changes
- `style` - Code style changes (formatting, etc.)
- `refactor` - Code refactoring
- `test` - Test additions or changes
- `chore` - Maintenance tasks
- `perf` - Performance improvements

**Examples:**
```bash
git commit -m "feat(api): add report filtering by severity"
git commit -m "fix(auth): resolve JWT token expiration issue"
git commit -m "docs(readme): update installation instructions"
git commit -m "test(reports): add unit tests for report service"
```

## 📝 Pull Request Process

### 1. Update Your Branch

Before submitting, ensure your branch is up to date:

```bash
git checkout main
git pull upstream main
git checkout your-branch
git rebase main
```

### 2. Push Your Changes

```bash
git push origin your-branch
```

### 3. Create Pull Request

1. Go to the original repository on GitHub
2. Click "New Pull Request"
3. Select your branch
4. Fill out the PR template

### Pull Request Template

```markdown
## Description
Brief description of the changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
Describe the tests you ran to verify your changes

## Screenshots (if applicable)
Add screenshots for UI changes

## Checklist
- [ ] My code follows the project's style guidelines
- [ ] I have performed a self-review of my code
- [ ] I have commented my code where necessary
- [ ] I have updated the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix/feature works
- [ ] New and existing unit tests pass locally
- [ ] Any dependent changes have been merged
```

### Code Review Process

1. **Automated Checks** - CI/CD pipeline runs tests and linters
2. **Peer Review** - At least one maintainer reviews your code
3. **Address Feedback** - Make requested changes
4. **Approval** - Once approved, your PR will be merged

### Review Criteria

Reviewers will check for:
- ✅ Code quality and style
- ✅ Test coverage
- ✅ Documentation
- ✅ Performance implications
- ✅ Security considerations
- ✅ Breaking changes

## 🐛 Bug Reports

### Before Submitting

1. **Search existing issues** - Your bug might already be reported
2. **Test on latest version** - Ensure the bug exists in the latest release
3. **Check documentation** - Make sure it's actually a bug

### Bug Report Template

```markdown
## Bug Description
Clear and concise description of the bug

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

## Expected Behavior
What you expected to happen

## Actual Behavior
What actually happened

## Screenshots
If applicable, add screenshots

## Environment
- OS: [e.g. Ubuntu 22.04]
- Browser: [e.g. Chrome 120]
- Node version: [e.g. 18.19.0]
- Application version: [e.g. 1.2.3]

## Additional Context
Any other relevant information
```

## 💡 Feature Requests

### Feature Request Template

```markdown
## Feature Description
Clear and concise description of the feature

## Problem Statement
What problem does this solve?

## Proposed Solution
Describe how you envision this working

## Alternatives Considered
What other solutions did you consider?

## Additional Context
Mockups, examples, or references

## Priority
- [ ] Critical
- [ ] High
- [ ] Medium
- [ ] Low
```

## 📚 Documentation Contributions

Documentation is as important as code! Here's how to contribute:

### Types of Documentation

1. **Code Comments** - Explain complex logic
2. **API Documentation** - Document endpoints and parameters
3. **User Guides** - Help users understand features
4. **Developer Guides** - Help developers contribute

### Documentation Style Guide

- Use clear, concise language
- Include code examples
- Add screenshots for UI features
- Keep it up to date
- Use proper markdown formatting

### Updating Documentation

```bash
# Clone the documentation repository
git clone https://github.com/BugBounty-MockingBird/Documentation.git
cd Documentation

# Make changes in docs/ folder
# Submit PR following the same process as code contributions
```

## 🔒 Security Issues

**DO NOT** open public issues for security vulnerabilities.

Instead, please email: **security@bugbounty-ksp.com**

Include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

We will respond within 48 hours and work with you to address the issue.

## 🤝 Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive environment for all contributors.

### Expected Behavior

- ✅ Be respectful and considerate
- ✅ Welcome newcomers
- ✅ Be collaborative
- ✅ Accept constructive criticism gracefully
- ✅ Focus on what's best for the community

### Unacceptable Behavior

- ❌ Harassment or discriminatory language
- ❌ Personal attacks
- ❌ Trolling or inflammatory comments
- ❌ Publishing others' private information
- ❌ Other unprofessional conduct

### Enforcement

Violations may result in:
1. Warning
2. Temporary ban
3. Permanent ban

Report violations to: **conduct@bugbounty-ksp.com**

## 🎓 Learning Resources

### For New Contributors

- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [React Documentation](https://react.dev/)

### Project Resources

- [Architecture Documentation](architecture.md)
- [API Reference](api-reference.md)
- [Frontend Components](frontend-components.md)

## 🏆 Recognition

Contributors will be:
- Listed in CONTRIBUTORS.md
- Mentioned in release notes
- Credited in documentation
- Featured in community highlights

## 📞 Getting Help

If you need help:

1. **Documentation** - Check our docs first
2. **Discord** - Join our community server
3. **Discussions** - Use GitHub Discussions
4. **Issues** - Create an issue with the "question" label

## 📋 Contributor License Agreement

By contributing, you agree that:

1. Your contributions are your original work
2. You have the right to submit your contributions
3. Your contributions are licensed under the project's MIT License
4. You grant the project maintainers the right to use your contributions

## 🎉 Thank You!

Every contribution, no matter how small, makes a difference. Thank you for helping make BugBounty KSP better!

---

[← Deployment](deployment.md) | [Home](index.md)
