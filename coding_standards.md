# Coding Standards

## Overview
This document defines the coding standards and conventions for the Equevu fintech platform. All code must adhere to these standards to ensure consistency, maintainability, and alignment with our repository pattern architecture.

## Table of Contents

### Coding Standards
- [Python Style Guide](#python-style-guide)
- [Repository Pattern Standards](#repository-pattern-standards)
- [Django Conventions](#django-conventions)
- [Code Organization](#code-organization)
- [Testing Requirements](#testing-requirements)
- [Documentation Standards](#documentation-standards)
- [Security Guidelines](#security-guidelines)

## Python Style Guide

### Basic Formatting
- Follow PEP 8 guidelines strictly
- Use 4 spaces for indentation (no tabs)
- Maximum line length: 120 characters
- Use snake_case for functions and variables
- Use PascalCase for classes
- Use UPPER_CASE for constants

### Import Organization
```python
# Standard library imports
import os
import sys
from datetime import datetime

# Third-party imports
import requests
from django.db import models
from rest_framework import serializers

# Local application imports
from apps.companies.repository.company_repository import CompanyRepository
from apps.companies.services.company_service import CompanyService
```

### Naming Conventions
- Repository classes: `{Model}Repository` (e.g., `CompanyRepository`)
- Service classes: `{Domain}Service` (e.g., `CompanyService`, `PaymentService`)
- External clients: `{Service}Client` (e.g., `S3Client`, `TwilioClient`)
- Test files: `test_{module}.py`
- Test classes: `Test{ClassName}`
- Test methods: `test_{method_description}`

### Type Hints
- Always use type hints for function parameters and return values
- Use `Optional` for nullable values
- Use `List`, `Dict`, `Tuple` from typing module

```python
from typing import Optional, List, Dict
from decimal import Decimal

def calculate_contribution(
    employee_id: int, 
    salary: Decimal, 
    is_native: bool = False
) -> Dict[str, Decimal]:
    pass
```

## Repository Pattern Standards

All code must follow the repository pattern architecture as defined in [architecture.md](./architecture.md). Key principles:

- **Models**: Pure Django ORM definitions only, no business logic
- **Repository Layer**: Data access operations only (CRUD), no business logic
- **Service Layer**: All business logic, orchestration, and validation
- **API Views**: Minimal HTTP handling, delegates to services
- **External Services**: Isolated third-party integrations
- **Utils**: Pure helper functions without side effects

Refer to the architecture document for detailed implementation examples and migration strategies.

## Django Conventions

### App Structure
Each Django app must follow this structure:
```
app_name/
├── models.py           # Minimal models only
├── repository/         # Data access layer
├── services/          # Business logic layer
├── external/          # External service integrations
├── utils/             # Helper functions
├── api_views.py       # API endpoints
├── serializers.py     # DRF serializers
├── urls.py
└── migrations/
```

### Django-Specific Rules
- Never use `get()` without handling `DoesNotExist`
- Always use `select_related()` and `prefetch_related()` for optimization
- Use Django's `@transaction.atomic` for multi-step operations, but be mindful of what's inside:
  - Keep transactions as short as possible
  - Don't include slow logic or external API calls inside atomic blocks
  - Prepare data and queries beforehand when possible
  - Only wrap the actual database writes that need atomicity
- Never commit sensitive data to migrations
- Use Django's built-in validators where appropriate

```python
# BAD - Slow operations inside transaction
@transaction.atomic
def process_payment(company_id, amount):
    company = Company.objects.select_for_update().get(id=company_id)
    
    # External API call inside transaction - BLOCKS OTHER OPERATIONS!
    payment_result = external_payment_api.process(amount)  # Could take 30+ seconds
    
    if payment_result.success:
        company.balance += amount
        company.save()

# GOOD - Only critical operations inside transaction
def process_payment(company_id, amount):
    # Prepare data outside transaction
    company = Company.objects.get(id=company_id)
    
    # External call outside transaction
    payment_result = external_payment_api.process(amount)
    
    if payment_result.success:
        # Only the atomic database operations
        with transaction.atomic():
            Company.objects.filter(id=company_id).update(
                balance=F('balance') + amount,
                last_payment=timezone.now()
            )
            PaymentLog.objects.create(
                company_id=company_id,
                amount=amount,
                reference=payment_result.reference
            )
```

### Serializer Guidelines
- Keep serializers simple - validation only
- Complex business validation belongs in services
- Use separate serializers for input and output when needed

```python
class CreateCompanySerializer(serializers.Serializer):
    name = serializers.CharField(max_length=255)
    email = serializers.EmailField()
    
    def validate_email(self, value):
        return value.lower()  # Simple transformation only

class CompanyOutputSerializer(serializers.ModelSerializer):
    class Meta:
        model = Company
        fields = ['id', 'name', 'email', 'created_at']
```

## Code Organization

### File Naming
- Use lowercase with underscores for Python files
- Repository files: `{model}_repository.py`
- Service files: `{domain}_service.py`
- External service files: `{service}_client.py`
- Utility files: descriptive names like `validators.py`, `formatters.py`

### Method Organization
Within classes, organize methods in this order:
1. `__init__` and other special methods
2. Public methods (business operations)
3. Private helper methods (prefixed with `_`)

### Dependency Injection
- Services should receive dependencies via constructor
- Avoid global instances
- Makes testing easier

```python
class CompanyService:
    def __init__(
        self, 
        company_repo: CompanyRepository = None,
        notification_client: NotificationClient = None
    ):
        self.company_repo = company_repo or CompanyRepository()
        self.notification_client = notification_client or NotificationClient()
```

## Testing Requirements

### Test Coverage
- Critical business features must have unit tests
- API endpoints should have integration tests unless inapplicable
- Financial calculations and payment flows require comprehensive testing
- Mock external services to ensure test reliability

### Test Structure
```python
class TestCompanyService(TestCase):
    def setUp(self):
        self.service = CompanyService()
        self.mock_repo = Mock(spec=CompanyRepository)
        self.service.company_repo = self.mock_repo
    
    def test_create_company_success(self):
        # Arrange
        data = {'name': 'Test Corp', 'email': 'test@example.com'}
        self.mock_repo.get_by_email.return_value = None
        
        # Act
        result = self.service.create_company(data)
        
        # Assert
        self.assertEqual(result['status'], 'created')
        self.mock_repo.create.assert_called_once()
```

### API Integration Tests
- All API endpoints must have Apidog integration tests
- Include proof of Apidog tests in PR (screenshot or link)
- Tests should cover success cases, error cases, and edge cases
- Apidog collections should be organized by feature/module

Example Apidog test scenarios:
- Authentication flows
- CRUD operations for all endpoints
- Error responses (400, 401, 403, 404, 500)
- Pagination and filtering
- Business rule validations
- Rate limiting behavior

### Testing Best Practices
- Use descriptive test names
- Follow AAA pattern (Arrange, Act, Assert)
- One assertion per test when possible
- Mock external dependencies
- Test edge cases and error conditions
- Prioritize testing critical paths: payments, contributions, withdrawals

## Documentation Standards

### Docstrings
Every public class and method must have docstrings:

```python
class CompanyService:
    """
    Service layer for company-related business operations.
    
    Handles company creation, updates, and business rule validation.
    """
    
    def create_company(self, data: Dict) -> Dict:
        """
        Create a new company with initial setup.
        
        Args:
            data: Dictionary containing company details
                - name: Company name
                - email: Company email
                - phone_number: Contact number
        
        Returns:
            Dictionary with company_id and status
        
        Raises:
            ValueError: If company with email already exists
            ValidationError: If data validation fails
        """
        pass
```

### Inline Comments
- Use sparingly, code should be self-documenting
- Explain "why", not "what"
- Keep comments up-to-date with code changes

### Business Rules Documentation
- Document complex business rules in service methods
- Use clear variable names that express intent
- Add comments for non-obvious calculations

```python
def calculate_contribution(self, employee) -> Decimal:
    """Calculate employee contribution based on residency status."""
    base_salary = employee.basic_salary or Decimal('0')
    allowance = employee.allowance or Decimal('0')
    total_salary = base_salary + allowance
    
    # UAE nationals have higher contribution rate per government regulation
    if employee.is_native_resident:
        return total_salary * Decimal('0.125')  # 12.5% for nationals
    
    return total_salary * Decimal('0.05')  # 5% for expats
```

## Security Guidelines

### Never Commit
- API keys or secrets
- Database credentials
- Private keys or certificates
- Customer data or PII
- Internal URLs or IP addresses

### Input Validation
- Always validate input at the service layer
- Use Django's built-in validators
- Sanitize user input before processing
- Never trust client-side validation alone

### Database Security
- Use parameterized queries (Django ORM handles this)
- Never build raw SQL with string concatenation
- Validate all inputs before database operations
- Use least privilege principle for database users

### API Security
- Always use authentication and permissions
- Rate limit sensitive endpoints
- Log security-relevant events
- Never expose internal error details to clients

```python
# GOOD - Generic error message
except Exception as e:
    logger.error(f"Company creation failed: {str(e)}")
    return Response(
        {'error': 'Failed to create company'}, 
        status=status.HTTP_500_INTERNAL_SERVER_ERROR
    )

# BAD - Exposing internal details
except Exception as e:
    return Response(
        {'error': str(e), 'stack_trace': traceback.format_exc()}, 
        status=status.HTTP_500_INTERNAL_SERVER_ERROR
    )
```

## Code Review Checklist

Before submitting a PR, ensure:

- [ ] Code follows PEP 8 standards
- [ ] Repository pattern is correctly implemented
- [ ] Business logic is in service layer only
- [ ] Models contain no business logic
- [ ] API views are minimal
- [ ] All functions have type hints
- [ ] Public methods have docstrings
- [ ] Tests cover new functionality
- [ ] No sensitive data in code
- [ ] Dependencies are injected properly
- [ ] Error handling is appropriate
- [ ] Transactions are used where needed
- [ ] Database queries are optimized

## Enforcement

- All PRs must pass automated linting checks
- Code review must verify adherence to these standards
- Non-compliant code will not be merged
- Regular codebase audits to ensure compliance

Remember: These standards exist to ensure our codebase remains maintainable, secure, and scalable as our fintech platform grows.