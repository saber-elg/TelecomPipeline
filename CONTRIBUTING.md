# Contributing to Telecom CDR Pipeline

Thank you for your interest in contributing! This document provides guidelines for contributing to this project.

## Getting Started

1. Fork the repository
2. Clone your fork: `git clone https://github.com/yourusername/TelelecomPipeline.git`
3. Create a feature branch: `git checkout -b feature/your-feature-name`
4. Make your changes
5. Run tests to ensure nothing breaks
6. Commit your changes: `git commit -m "Add your feature"`
7. Push to your fork: `git push origin feature/your-feature-name`
8. Create a Pull Request

## Code Standards

### Python Style Guide
- Follow PEP 8 guidelines
- Use Black for code formatting
- Maximum line length: 88 characters
- Use type hints where applicable

### Naming Conventions
- Functions: `snake_case`
- Classes: `PascalCase`
- Constants: `UPPER_CASE`
- Variables: `snake_case`

### Documentation
- Add docstrings to all functions and classes
- Update README.md if adding new features
- Comment complex logic

## Testing

Before submitting a PR:
```bash
# Run linting
flake8 src/ scripts/

# Format code
black src/ scripts/

# Run tests (when available)
pytest tests/
```

## Pull Request Process

1. Update documentation for any new features
2. Ensure all tests pass
3. Update version number if applicable
4. Provide clear PR description:
   - What does this PR do?
   - Why is it needed?
   - How was it tested?

## Feature Requests & Bug Reports

Use GitHub Issues with appropriate labels:
- **Bug**: Something isn't working
- **Enhancement**: New feature request
- **Documentation**: Improvements to docs
- **Question**: General questions

## Code Review

All PRs require review before merging. Reviewers will check:
- Code quality and style
- Test coverage
- Documentation completeness
- Performance implications

## Areas for Contribution

- **Documentation**: Improve README, add examples
- **Testing**: Add unit tests, integration tests
- **Features**: Implement v1.0 roadmap items
- **Performance**: Optimize Spark jobs
- **Bug Fixes**: Fix reported issues

Thank you for contributing! 🎉
