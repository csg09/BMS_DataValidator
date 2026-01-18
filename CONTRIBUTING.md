# Contributing to BMS Validator

First off, thank you for considering contributing to BMS Validator! It's people like you that make this tool better for everyone in the building automation industry.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Style Guidelines](#style-guidelines)
- [Commit Messages](#commit-messages)
- [Pull Request Process](#pull-request-process)

## Code of Conduct

This project and everyone participating in it is governed by our commitment to creating a welcoming and inclusive environment. Please be respectful and constructive in all interactions.

## Getting Started

### Issues

- **Bug Reports**: Use the bug report template to describe the issue
- **Feature Requests**: Use the feature request template to propose new functionality
- **Questions**: Open a discussion for general questions

### Good First Issues

Look for issues labeled `good first issue` - these are specifically curated for new contributors.

## How Can I Contribute?

### Reporting Bugs

Before creating a bug report, please check existing issues to avoid duplicates.

When creating a bug report, include:
- **Clear title** describing the issue
- **Steps to reproduce** the behavior
- **Expected behavior** vs actual behavior
- **Screenshots** if applicable
- **Environment details** (OS, Python version, browser)
- **Sample file** (if the bug involves file processing - anonymize sensitive data)

### Suggesting Features

Feature suggestions are welcome! Please include:
- **Use case**: Why is this feature needed?
- **Proposed solution**: How should it work?
- **Alternatives considered**: What other approaches did you think of?
- **Additional context**: Mockups, examples, etc.

### Code Contributions

1. Fork the repository
2. Create a feature branch from `main`
3. Make your changes
4. Write/update tests
5. Submit a pull request

## Development Setup

### Prerequisites

- Python 3.11+
- Node.js 18+
- PostgreSQL 14+
- Redis 7+
- Poetry (Python package manager)

### Backend Setup

```bash
cd backend

# Install dependencies
poetry install

# Set up pre-commit hooks
poetry run pre-commit install

# Copy and configure environment
cp .env.example .env

# Run migrations
poetry run alembic upgrade head

# Start development server
poetry run uvicorn app.main:app --reload
```

### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

### Running Tests

```bash
# Backend tests
cd backend
poetry run pytest
poetry run pytest --cov=app  # with coverage

# Frontend tests
cd frontend
npm test
```

## Style Guidelines

### Python (Backend)

We follow PEP 8 with some modifications:
- Line length: 100 characters
- Use type hints for all function signatures
- Docstrings: Google style

```python
def validate_file(
    file_path: str | Path,
    upload_id: str,
    vendor_id: int | None = None,
) -> dict[str, Any]:
    """
    Validate a BMS data file.

    Args:
        file_path: Path to the file to validate
        upload_id: Unique identifier for the upload
        vendor_id: Optional vendor ID for vendor-specific rules

    Returns:
        Dictionary containing validation results

    Raises:
        FileNotFoundError: If file doesn't exist
        ValueError: If file format is unsupported
    """
```

**Tools:**
- `black` - Code formatting
- `ruff` - Linting
- `mypy` - Type checking

### TypeScript (Frontend)

- Use functional components with hooks
- Prefer named exports
- Use TypeScript strict mode

```typescript
interface ValidationResultProps {
  uploadId: string;
  score: number;
  issues: Issue[];
}

export const ValidationResult: React.FC<ValidationResultProps> = ({
  uploadId,
  score,
  issues,
}) => {
  // Component implementation
};
```

**Tools:**
- `eslint` - Linting
- `prettier` - Code formatting

### Database

- Use Alembic for all schema changes
- Descriptive migration messages
- Never modify existing migrations

```bash
# Create a new migration
poetry run alembic revision --autogenerate -m "Add validation_results table"
```

## Commit Messages

We follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

### Examples

```
feat(validators): add rate-of-change spike detection

fix(api): handle empty file uploads gracefully

docs(readme): update installation instructions

refactor(services): extract report generation to separate module
```

## Pull Request Process

### Before Submitting

1. ✅ Code follows style guidelines
2. ✅ All tests pass
3. ✅ New features have tests
4. ✅ Documentation is updated
5. ✅ Commit messages follow conventions
6. ✅ Branch is up to date with `main`

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## How Has This Been Tested?
Describe testing approach

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings
- [ ] Tests added/updated
- [ ] All tests pass
```

### Review Process

1. Automated checks must pass (linting, tests)
2. At least one maintainer review required
3. Address all review comments
4. Squash commits if requested
5. Maintainer will merge when ready

## Adding New Validators

To add a new validation type:

1. Create validator class in `backend/app/services/validators/`
2. Follow existing pattern (see `spike_detector.py` as example)
3. Register in `ValidationService`
4. Add tests in `backend/tests/services/validators/`
5. Update documentation

```python
# Example validator structure
class MyValidator:
    def __init__(self, threshold: float = 0.5):
        self.threshold = threshold
    
    def validate(self, df: pd.DataFrame) -> list[dict[str, Any]]:
        """Run validation and return list of issues."""
        issues = []
        # Validation logic
        return issues
```

## Questions?

Feel free to open a discussion or reach out to the maintainers.

Thank you for contributing! 🎉
