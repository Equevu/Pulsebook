# Repository Pattern Architecture for Django Project

## Table of Contents
- [Project Structure](#project-structure)
- [Implementation Examples](#implementation-examples)
  - [1. Minimal Models](#1-minimal-modelspy)
  - [2. Repository Layer](#2-repository-layer-data-access-only)
  - [3. Service Layer](#3-service-layer-business-logic)
  - [4. Minimal API Views](#4-minimal-api-views)
  - [5. External Services](#5-external-services)
  - [6. Utils](#6-utils-helpers-only)
  - [7. Shared Base Classes](#7-shared-base-classes)
- [Key Principles](#key-principles)
- [Benefits](#benefits)
- [Migration Strategy](#migration-strategy)

## Project Structure

```
Equevu/
├── apps/
│   ├── companies/
│   │   ├── models.py                    # Minimal - only Django ORM models
│   │   ├── repository/
│   │   │   ├── __init__.py
│   │   │   ├── company_repository.py
│   │   │   ├── document_repository.py
│   │   │   └── contribution_repository.py
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── company_service.py       # Business logic for companies
│   │   │   ├── contribution_service.py  # Business logic for contributions
│   │   │   ├── document_service.py      # Business logic for documents
│   │   │   └── kyc_service.py          # Business logic for KYC
│   │   ├── external/
│   │   │   ├── __init__.py
│   │   │   ├── s3_client.py            # AWS S3 integration
│   │   │   ├── kyc_provider.py         # Onfido integration
│   │   │   └── notification_client.py   # Teams/Email notifications
│   │   ├── utils/
│   │   │   ├── __init__.py
│   │   │   ├── validators.py           # Input validators
│   │   │   ├── formatters.py           # Data formatters
│   │   │   └── constants.py            # App-specific constants
│   │   ├── api_views.py                # Minimal - only request/response handling
│   │   ├── serializers.py
│   │   ├── urls.py
│   │   ├── admin.py
│   │   └── migrations/
│   │
│   ├── contributors/
│   │   ├── models.py
│   │   ├── repository/
│   │   │   ├── __init__.py
│   │   │   ├── contribution_repository.py
│   │   │   ├── order_repository.py
│   │   │   └── withdrawal_repository.py
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── contribution_service.py
│   │   │   ├── gratuity_service.py
│   │   │   ├── order_service.py
│   │   │   └── withdrawal_service.py
│   │   ├── external/
│   │   │   ├── __init__.py
│   │   │   └── allfunds_client.py
│   │   ├── utils/
│   │   │   ├── __init__.py
│   │   │   ├── calculators.py
│   │   │   └── date_helpers.py
│   │   ├── api_views.py
│   │   ├── serializers.py
│   │   └── migrations/
│   │
│   ├── users/
│   │   ├── models.py
│   │   ├── repository/
│   │   │   ├── __init__.py
│   │   │   ├── user_repository.py
│   │   │   ├── employee_repository.py
│   │   │   └── employer_repository.py
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── user_service.py
│   │   │   ├── employee_service.py
│   │   │   ├── auth_service.py
│   │   │   └── otp_service.py
│   │   ├── external/
│   │   │   ├── __init__.py
│   │   │   └── twilio_client.py
│   │   ├── utils/
│   │   │   ├── __init__.py
│   │   │   └── password_helpers.py
│   │   ├── api_views.py
│   │   └── migrations/
│   │
│   ├── core/
│   │   ├── models.py
│   │   ├── repository/
│   │   │   ├── __init__.py
│   │   │   ├── investment_repository.py
│   │   │   └── settings_repository.py
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── investment_service.py
│   │   │   ├── fee_service.py
│   │   │   └── email_service.py
│   │   ├── external/
│   │   │   ├── __init__.py
│   │   │   └── currency_api_client.py
│   │   ├── utils/
│   │   │   ├── __init__.py
│   │   │   ├── excel_generator.py
│   │   │   └── csv_generator.py
│   │   ├── api_views.py
│   │   └── migrations/
│   │
│   ├── payments_management/
│   │   ├── models.py
│   │   ├── repository/
│   │   │   ├── __init__.py
│   │   │   └── payment_repository.py
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── payment_service.py
│   │   │   └── transaction_service.py
│   │   ├── external/
│   │   │   ├── __init__.py
│   │   │   ├── bank_api_client.py
│   │   │   └── sftp_client.py
│   │   ├── utils/
│   │   │   ├── __init__.py
│   │   │   └── payment_validators.py
│   │   ├── api_views.py
│   │   └── migrations/
│   │
│   └── trustee/
│       ├── models.py
│       ├── repository/
│       │   ├── __init__.py
│       │   ├── trustee_repository.py
│       │   └── reconciliation_repository.py
│       ├── services/
│       │   ├── __init__.py
│       │   ├── trustee_service.py
│       │   ├── reconciliation_service.py
│       │   └── report_service.py
│       ├── external/
│       │   ├── __init__.py
│       │   └── trustee_notification_client.py
│       ├── utils/
│       │   ├── __init__.py
│       │   └── report_generators.py
│       ├── api_views.py
│       └── migrations/
│
└── shared/
    ├── base_repository.py              # Base repository class
    ├── base_service.py                 # Base service class
    └── exceptions.py                   # Custom exceptions
```

## Implementation Examples

### 1. Minimal models.py

```python
# apps/companies/models.py
from django.db import models
from django.contrib.auth import get_user_model

User = get_user_model()

class Company(models.Model):
    """Pure data model - no business logic"""
    name = models.CharField(max_length=255)
    email = models.EmailField()
    phone_number = models.CharField(max_length=20)
    address = models.TextField()
    is_active = models.BooleanField(default=False)
    trustee_entity = models.ForeignKey('trustee.TrusteeEntity', on_delete=models.SET_NULL, null=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        db_table = 'companies'
        verbose_name_plural = 'Companies'

class CompanyDocument(models.Model):
    """Pure data model for company documents"""
    company = models.ForeignKey(Company, on_delete=models.CASCADE)
    document_type = models.CharField(max_length=50)
    file_url = models.URLField()
    is_verified = models.BooleanField(default=False)
    uploaded_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        db_table = 'company_documents'
```

### 2. Repository Layer (Data Access Only)

```python
# apps/companies/repository/company_repository.py
from typing import Optional, List
from django.db.models import Q, QuerySet
from apps.companies.models import Company

class CompanyRepository:
    """Data access layer - no business logic"""
    
    def get_by_id(self, company_id: int) -> Optional[Company]:
        try:
            return Company.objects.get(id=company_id)
        except Company.DoesNotExist:
            return None
    
    def get_active_companies(self) -> QuerySet:
        return Company.objects.filter(is_active=True)
    
    def get_by_email(self, email: str) -> Optional[Company]:
        return Company.objects.filter(email__iexact=email).first()
    
    def create(self, **kwargs) -> Company:
        return Company.objects.create(**kwargs)
    
    def update(self, company_id: int, **kwargs) -> bool:
        return Company.objects.filter(id=company_id).update(**kwargs) > 0
    
    def delete(self, company_id: int) -> bool:
        return Company.objects.filter(id=company_id).delete()[0] > 0
    
    def get_by_trustee_entity(self, trustee_entity_id: int) -> QuerySet:
        return Company.objects.filter(trustee_entity_id=trustee_entity_id)
    
    def search(self, query: str) -> QuerySet:
        return Company.objects.filter(
            Q(name__icontains=query) | 
            Q(email__icontains=query)
        )
    
    def bulk_create(self, companies: List[dict]) -> List[Company]:
        company_objects = [Company(**data) for data in companies]
        return Company.objects.bulk_create(company_objects)
```

```python
# apps/companies/repository/document_repository.py
from typing import Optional, List
from apps.companies.models import CompanyDocument

class DocumentRepository:
    """Repository for document operations"""
    
    def create_initial_documents(self, company_id: int) -> List[CompanyDocument]:
        """Create initial document placeholders for a company"""
        document_types = [
            'certificate_of_incorporation',
            'articles_of_association',
            'bank_account_details',
            'kyc_documents'
        ]
        
        documents = []
        for doc_type in document_types:
            doc = CompanyDocument.objects.create(
                company_id=company_id,
                document_type=doc_type,
                file_url='',
                is_verified=False
            )
            documents.append(doc)
        
        return documents
    
    def get_company_documents(self, company_id: int) -> QuerySet:
        return CompanyDocument.objects.filter(company_id=company_id)
    
    def update_document_url(self, document_id: int, file_url: str) -> bool:
        return CompanyDocument.objects.filter(id=document_id).update(
            file_url=file_url,
            is_verified=False
        ) > 0
```

### 3. Service Layer (Business Logic)

```python
# apps/companies/services/company_service.py
from decimal import Decimal
from typing import Dict, Optional, List
from django.db import transaction
from apps.companies.repository.company_repository import CompanyRepository
from apps.companies.repository.document_repository import DocumentRepository
from apps.companies.external.s3_client import S3Client
from apps.companies.external.notification_client import NotificationClient
from apps.users.repository.employer_repository import EmployerRepository

class CompanyService:
    """Business logic layer for company operations"""
    
    def __init__(self):
        self.company_repo = CompanyRepository()
        self.document_repo = DocumentRepository()
        self.employer_repo = EmployerRepository()
        self.s3_client = S3Client()
        self.notification_client = NotificationClient()
    
    @transaction.atomic
    def create_company(self, data: Dict) -> Dict:
        """
        Create company with all related setup
        Business Rules:
        - Email must be unique
        - Company gets default trustee entity
        - Initial documents are created
        - Head employer is created
        - Notifications are sent
        """
        # Validation
        if self.company_repo.get_by_email(data['email']):
            raise ValueError("Company with this email already exists")
        
        # Create company
        company = self.company_repo.create(
            name=data['name'],
            email=data['email'].lower(),
            phone_number=data['phone_number'],
            address=data['address'],
            trustee_entity_id=self._get_trustee_entity_id()
        )
        
        # Create related records
        self.document_repo.create_initial_documents(company.id)
        employer = self.employer_repo.create_head_employer(company.id)
        
        # Send notifications
        self.notification_client.send_company_created(company)
        
        return {
            'company_id': company.id,
            'employer_id': employer.id,
            'status': 'created'
        }
    
    def calculate_contributions(self, company_id: int) -> Dict:
        """
        Calculate total contributions for a company
        Business Rules:
        - Only active employees are included
        - Contribution = 5% of (basic_salary + allowance)
        - Native residents may have different rates
        """
        company = self.company_repo.get_by_id(company_id)
        if not company:
            raise ValueError("Company not found")
        
        employees = self.employer_repo.get_active_employees(company_id)
        
        contribution_details = []
        total_contributions = Decimal('0')
        
        for employee in employees:
            contribution = self._calculate_employee_contribution(employee)
            total_contributions += contribution
            
            contribution_details.append({
                'employee_id': employee.id,
                'employee_name': employee.full_name,
                'contribution_amount': contribution
            })
        
        return {
            'company_id': company_id,
            'company_name': company.name,
            'total_contributions': total_contributions,
            'employee_count': len(employees),
            'details': contribution_details
        }
    
    def process_bulk_upload(self, company_id: int, file_data: bytes) -> Dict:
        """
        Process bulk employee upload
        Business Rules:
        - Validate file format
        - Check for duplicates
        - Create employees in batch
        """
        # Implementation would go here
        pass
    
    def _calculate_employee_contribution(self, employee) -> Decimal:
        """Calculate individual employee contribution"""
        base_salary = employee.basic_salary or Decimal('0')
        allowance = employee.allowance or Decimal('0')
        total_salary = base_salary + allowance
        
        # Different rates for native residents
        if employee.is_native_resident:
            return total_salary * Decimal('0.125')  # 12.5% for nationals
        
        return total_salary * Decimal('0.05')  # 5% for expats
    
    def _get_trustee_entity_id(self) -> int:
        """Get default trustee entity based on business rules"""
        # In production, this would have actual logic
        return 1
```

```python
# apps/companies/services/contribution_service.py
from decimal import Decimal
from typing import Dict, List
from datetime import datetime, date
from django.db import transaction
from apps.companies.repository.contribution_repository import ContributionRepository
from apps.companies.repository.company_repository import CompanyRepository
from apps.contributors.repository.order_repository import OrderRepository

class ContributionService:
    """Service for handling contribution business logic"""
    
    def __init__(self):
        self.contribution_repo = ContributionRepository()
        self.company_repo = CompanyRepository()
        self.order_repo = OrderRepository()
    
    @transaction.atomic
    def process_monthly_contributions(self, company_id: int) -> Dict:
        """
        Process monthly contributions for a company
        Business Rules:
        - Contributions are processed on the 1st of each month
        - Must check for active contribution plans
        - Generate orders for investment
        """
        company = self.company_repo.get_by_id(company_id)
        if not company or not company.is_active:
            raise ValueError("Invalid or inactive company")
        
        # Get active contribution plans
        plans = self.contribution_repo.get_active_plans(company_id)
        if not plans:
            return {'status': 'no_active_plans'}
        
        results = []
        for plan in plans:
            result = self._process_plan_contribution(plan)
            results.append(result)
        
        return {
            'company_id': company_id,
            'processed_date': datetime.now(),
            'plans_processed': len(results),
            'results': results
        }
    
    def _process_plan_contribution(self, plan) -> Dict:
        """Process individual contribution plan"""
        # Business logic implementation
        pass
```

### 4. Minimal API Views

```python
# apps/companies/api_views.py
from rest_framework import status
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from apps.companies.services.company_service import CompanyService
from apps.companies.services.contribution_service import ContributionService
from apps.companies.serializers import (
    CompanySerializer, 
    CreateCompanySerializer,
    ContributionSerializer
)

# Initialize services
company_service = CompanyService()
contribution_service = ContributionService()

@api_view(['POST'])
@permission_classes([IsAuthenticated])
def create_company(request):
    """
    Create a new company
    Minimal view - only handles HTTP concerns
    """
    serializer = CreateCompanySerializer(data=request.data)
    serializer.is_valid(raise_exception=True)
    
    try:
        result = company_service.create_company(serializer.validated_data)
        return Response(result, status=status.HTTP_201_CREATED)
    except ValueError as e:
        return Response({'error': str(e)}, status=status.HTTP_400_BAD_REQUEST)

@api_view(['GET'])
@permission_classes([IsAuthenticated])
def get_company_contributions(request, company_id):
    """
    Get company contributions
    Delegates all logic to service layer
    """
    try:
        result = company_service.calculate_contributions(company_id)
        return Response(result)
    except ValueError as e:
        return Response({'error': str(e)}, status=status.HTTP_404_NOT_FOUND)

@api_view(['POST'])
@permission_classes([IsAuthenticated])
def process_contributions(request, company_id):
    """Process monthly contributions"""
    try:
        result = contribution_service.process_monthly_contributions(company_id)
        return Response(result)
    except Exception as e:
        return Response({'error': str(e)}, status=status.HTTP_400_BAD_REQUEST)

@api_view(['GET'])
@permission_classes([IsAuthenticated])
def list_companies(request):
    """List all active companies"""
    # Even listing is delegated to service for business rules
    companies = company_service.get_active_companies_for_user(request.user)
    serializer = CompanySerializer(companies, many=True)
    return Response(serializer.data)
```

### 5. External Services

```python
# apps/companies/external/s3_client.py
import boto3
from typing import Optional
from django.conf import settings

class S3Client:
    """
    AWS S3 integration
    Handles all S3 operations
    """
    
    def __init__(self):
        self.client = boto3.client(
            's3',
            aws_access_key_id=settings.AWS_ACCESS_KEY_ID,
            aws_secret_access_key=settings.AWS_SECRET_ACCESS_KEY,
            region_name=settings.AWS_S3_REGION_NAME
        )
        self.bucket = settings.AWS_STORAGE_BUCKET_NAME
    
    def upload_file(self, file_obj, key: str) -> str:
        """Upload file to S3 and return URL"""
        try:
            self.client.upload_fileobj(file_obj, self.bucket, key)
            return f"https://{self.bucket}.s3.amazonaws.com/{key}"
        except Exception as e:
            raise Exception(f"Failed to upload file: {str(e)}")
    
    def delete_file(self, key: str) -> bool:
        """Delete file from S3"""
        try:
            self.client.delete_object(Bucket=self.bucket, Key=key)
            return True
        except Exception:
            return False
    
    def generate_presigned_url(self, key: str, expiration: int = 3600) -> str:
        """Generate presigned URL for temporary access"""
        return self.client.generate_presigned_url(
            'get_object',
            Params={'Bucket': self.bucket, 'Key': key},
            ExpiresIn=expiration
        )
```

```python
# apps/companies/external/notification_client.py
import requests
from django.conf import settings
from typing import Dict

class NotificationClient:
    """
    External notification service
    Handles Teams, Email, SMS notifications
    """
    
    def __init__(self):
        self.teams_webhook = settings.TEAMS_WEBHOOK_URL
        self.email_service = self._init_email_service()
    
    def send_company_created(self, company) -> None:
        """Send notification when company is created"""
        self._send_teams_notification({
            'title': 'New Company Created',
            'text': f'Company: {company.name}',
            'facts': [
                {'name': 'Email', 'value': company.email},
                {'name': 'ID', 'value': str(company.id)}
            ]
        })
        
        self._send_email_notification(
            to=company.email,
            subject='Welcome to the platform',
            template='company_welcome'
        )
    
    def _send_teams_notification(self, data: Dict) -> None:
        """Send Microsoft Teams notification"""
        if not self.teams_webhook:
            return
        
        payload = {
            '@type': 'MessageCard',
            '@context': 'http://schema.org/extensions',
            'title': data['title'],
            'text': data['text'],
            'sections': [{'facts': data.get('facts', [])}]
        }
        
        requests.post(self.teams_webhook, json=payload)
    
    def _send_email_notification(self, to: str, subject: str, template: str) -> None:
        """Send email notification"""
        # Email implementation
        pass
    
    def _init_email_service(self):
        """Initialize email service"""
        # Could be SendGrid, AWS SES, etc.
        pass
```

### 6. Utils (Helpers Only)

```python
# apps/companies/utils/formatters.py
from decimal import Decimal
from typing import Optional

def format_currency(amount: Decimal, currency: str = 'AED') -> str:
    """Format currency for display"""
    return f"{amount:,.2f} {currency}"

def format_phone_number(phone: str) -> str:
    """Format phone number for display"""
    digits = ''.join(filter(str.isdigit, phone))
    if len(digits) == 10:
        return f"({digits[:3]}) {digits[3:6]}-{digits[6:]}"
    return phone

def format_company_name(name: str) -> str:
    """Format company name"""
    return name.strip().title()
```

```python
# apps/companies/utils/validators.py
import re
from typing import bool

def validate_email(email: str) -> bool:
    """Validate email format"""
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

def validate_phone(phone: str) -> bool:
    """Validate phone number"""
    digits = ''.join(filter(str.isdigit, phone))
    return len(digits) >= 10

def validate_company_name(name: str) -> bool:
    """Validate company name"""
    return len(name.strip()) >= 3
```

### 7. Shared Base Classes

```python
# shared/base_repository.py
from typing import Generic, TypeVar, Optional, List
from django.db.models import Model, QuerySet

T = TypeVar('T', bound=Model)

class BaseRepository(Generic[T]):
    """
    Base repository with common CRUD operations
    All repositories should extend this
    """
    model_class: T = None
    
    def get_by_id(self, id: int) -> Optional[T]:
        """Get single record by ID"""
        try:
            return self.model_class.objects.get(id=id)
        except self.model_class.DoesNotExist:
            return None
    
    def get_all(self) -> QuerySet:
        """Get all records"""
        return self.model_class.objects.all()
    
    def filter(self, **kwargs) -> QuerySet:
        """Filter records"""
        return self.model_class.objects.filter(**kwargs)
    
    def create(self, **kwargs) -> T:
        """Create new record"""
        return self.model_class.objects.create(**kwargs)
    
    def update(self, id: int, **kwargs) -> bool:
        """Update existing record"""
        return self.model_class.objects.filter(id=id).update(**kwargs) > 0
    
    def delete(self, id: int) -> bool:
        """Delete record"""
        return self.model_class.objects.filter(id=id).delete()[0] > 0
    
    def exists(self, **kwargs) -> bool:
        """Check if record exists"""
        return self.model_class.objects.filter(**kwargs).exists()
    
    def count(self, **kwargs) -> int:
        """Count records"""
        return self.model_class.objects.filter(**kwargs).count()
```

```python
# shared/base_service.py
from typing import Optional
from django.contrib.auth.models import User

class BaseService:
    """
    Base service class with common functionality
    All services should extend this
    """
    
    def __init__(self, user: Optional[User] = None):
        self.current_user = user
    
    def has_permission(self, action: str, resource: any) -> bool:
        """Check if current user has permission"""
        # Implement permission logic
        return True
    
    def log_action(self, action: str, details: dict) -> None:
        """Log service actions for audit"""
        # Implement logging
        pass
    
    def validate_business_rules(self, data: dict) -> bool:
        """Validate business rules"""
        # Override in child classes
        return True
```

```python
# shared/exceptions.py
class BusinessRuleViolation(Exception):
    """Raised when business rule is violated"""
    pass

class ResourceNotFound(Exception):
    """Raised when resource is not found"""
    pass

class DuplicateResource(Exception):
    """Raised when trying to create duplicate resource"""
    pass

class InsufficientPermissions(Exception):
    """Raised when user lacks permissions"""
    pass

class ExternalServiceError(Exception):
    """Raised when external service fails"""
    pass
```

## Key Principles

1. **Models.py**
   - Only Django ORM model definitions
   - No business logic
   - No complex queries
   - Keep it minimal and clean

2. **Repository Layer**
   - Only data access operations (CRUD)
   - No business logic
   - Returns model instances or QuerySets
   - Handles database exceptions

3. **Service Layer**
   - All business logic and rules
   - Orchestrates between repositories
   - Handles transactions
   - Validates business rules
   - Calls external services

4. **External Services**
   - Third-party API integrations
   - External system communications
   - Isolated from business logic
   - Easy to mock for testing

5. **Utils**
   - Pure helper functions
   - No side effects
   - Formatters and validators
   - Reusable across services

6. **API Views**
   - Minimal HTTP handling
   - Request/Response processing
   - Authentication/Permission checks
   - Delegates all logic to services

## Benefits

### Testability
- Easy to unit test each layer independently
- Services can be tested without HTTP context
- Repositories can be mocked for service tests
- External services can be mocked

### Maintainability
- Clear separation of concerns
- Easy to locate and fix bugs
- Business logic in one place
- Database queries isolated

### Scalability
- Easy to add new features
- Can swap data sources (repositories)
- Can change external services
- Services can be moved to microservices

### Code Reusability
- Services can be used by multiple views
- Repositories can be shared
- Utils are pure functions
- Base classes reduce duplication

## Migration Strategy

### Phase 1: Setup Structure
1. Create folder structure for one app
2. Create base classes in shared/
3. Set up dependency injection

### Phase 2: Move Logic
1. Extract data access to repositories
2. Move business logic from utils to services
3. Clean up models.py
4. Simplify views

### Phase 3: Refactor
1. Remove duplication
2. Standardize patterns
3. Add proper error handling
4. Improve test coverage

### Phase 4: Optimize
1. Add caching where needed
2. Optimize database queries
3. Async operations for external calls
4. Performance monitoring

## Example Migration

### Before (Mixed Concerns)
```python
# models.py
class Company(models.Model):
    # ... fields ...
    
    def calculate_contribution(self):
        # Business logic in model
        employees = self.employees.filter(active=True)
        total = sum(e.salary * 0.05 for e in employees)
        return total
    
    def send_notification(self):
        # External service in model
        send_email(self.email, "notification")

# views.py
def create_company(request):
    # Business logic in view
    if Company.objects.filter(email=request.data['email']).exists():
        return Response({'error': 'Exists'})
    
    company = Company.objects.create(**request.data)
    # Direct external service call
    send_teams_notification(company)
    
    return Response({'id': company.id})
```

### After (Separated Concerns)
```python
# models.py - Clean
class Company(models.Model):
    # Only fields and meta
    name = models.CharField(max_length=255)
    email = models.EmailField()

# repository/company_repository.py - Data Access
class CompanyRepository:
    def create(self, **kwargs):
        return Company.objects.create(**kwargs)
    
    def get_by_email(self, email):
        return Company.objects.filter(email=email).first()

# services/company_service.py - Business Logic
class CompanyService:
    def create_company(self, data):
        if self.repo.get_by_email(data['email']):
            raise ValueError("Company exists")
        
        company = self.repo.create(**data)
        self.notifier.send_created(company)
        return company

# api_views.py - Minimal HTTP
@api_view(['POST'])
def create_company(request):
    try:
        company = company_service.create_company(request.data)
        return Response({'id': company.id})
    except ValueError as e:
        return Response({'error': str(e)}, status=400)
```

This architecture ensures clean, maintainable, and scalable code that follows best practices and SOLID principles.