# Local Environment & Server Environments

## Local Development Environment

### Prerequisites
- Node.js (v18.x or higher)
- npm or yarn package manager
- Git
- Docker (for containerized services)
- IDE with appropriate extensions

### Initial Setup
1. **Clone the repository**
   ```bash
   git clone [repository-url]
   cd pulsebook
   ```

2. **Install dependencies**
   ```bash
   npm install
   # or
   yarn install
   ```

3. **Environment configuration**
   ```bash
   cp .env.example .env.local
   # Edit .env.local with your local settings
   ```

4. **Database setup**
   ```bash
   # Using Docker
   docker-compose up -d database
   
   # Run migrations
   npm run db:migrate
   
   # Seed development data
   npm run db:seed
   ```

5. **Start development server**
   ```bash
   npm run dev
   ```

### Local Development Tools
- **Hot Reload**: Automatic refresh on code changes
- **Debug Mode**: Enhanced error messages and logging
- **Mock Services**: Local mocks for external dependencies
- **Test Database**: Isolated database for testing

### Common Local Commands
```bash
npm run dev          # Start development server
npm run build        # Build for production
npm run test         # Run test suite
npm run lint         # Run linter
npm run format       # Format code
npm run db:reset     # Reset local database
```

## Server Environments

### Environment Overview

| Environment | Purpose | URL | Branch | Auto-Deploy |
|------------|---------|-----|--------|-------------|
| Development | Latest features, unstable | dev.pulsebook.com | develop | Yes |
| Staging | Pre-production testing | staging.pulsebook.com | staging | Yes |
| Production | Live application | app.pulsebook.com | main | Manual |

### Development Environment
- **Purpose**: Integration testing of new features
- **Data**: Synthetic test data
- **Access**: All developers
- **Deployment**: Automatic on merge to develop
- **Monitoring**: Basic logging

### Staging Environment
- **Purpose**: Final testing before production
- **Data**: Production-like data (sanitized)
- **Access**: Dev team + QA + Product
- **Deployment**: Automatic on merge to staging
- **Monitoring**: Full monitoring stack

### Production Environment
- **Purpose**: Live customer-facing application
- **Data**: Real production data
- **Access**: Limited to authorized personnel
- **Deployment**: Manual approval required
- **Monitoring**: Comprehensive monitoring and alerting

## Environment Variables

### Configuration Structure
```
.env.local          # Local development (git ignored)
.env.development    # Development server
.env.staging        # Staging server
.env.production     # Production server
```

### Required Variables
```bash
# Application
NODE_ENV=development|staging|production
PORT=3000
API_URL=https://api.example.com

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=pulsebook
DB_USER=username
DB_PASSWORD=password

# Authentication
JWT_SECRET=your-secret-key
SESSION_SECRET=your-session-secret

# External Services
AWS_ACCESS_KEY_ID=your-key
AWS_SECRET_ACCESS_KEY=your-secret
REDIS_URL=redis://localhost:6379

# Monitoring
SENTRY_DSN=your-sentry-dsn
LOG_LEVEL=debug|info|warn|error
```

## Deployment Process

### Development Deployment
1. Merge PR to develop branch
2. CI/CD pipeline triggers automatically
3. Tests run
4. Build created
5. Deploy to development server
6. Smoke tests run

### Staging Deployment
1. Create PR from develop to staging
2. Approval required from tech lead
3. Merge triggers deployment
4. Full test suite runs
5. Deploy to staging
6. QA verification

### Production Deployment
1. Create PR from staging to main
2. Approval from 2 senior developers required
3. Manual deployment trigger
4. Blue-green deployment strategy
5. Health checks
6. Rollback plan ready

## Access Management

### SSH Access
```bash
# Development
ssh user@dev.pulsebook.com

# Staging
ssh user@staging.pulsebook.com

# Production (restricted)
ssh user@prod.pulsebook.com
```

### Database Access
- Local: Direct connection
- Development: VPN required
- Staging: VPN + whitelist
- Production: Bastion host + MFA

### Logs Access
- CloudWatch for AWS environments
- Local logs in `./logs` directory
- Centralized logging via ELK stack

## Troubleshooting

### Common Issues

#### Port Already in Use
```bash
# Find process using port
lsof -i :3000
# Kill process
kill -9 [PID]
```

#### Database Connection Failed
- Check credentials in .env file
- Verify database is running
- Check network connectivity
- Verify firewall rules

#### Build Failures
- Clear node_modules and reinstall
- Check Node.js version compatibility
- Clear build cache
- Verify environment variables

### Health Checks
- Development: `/health`
- Staging: `/health` with detailed metrics
- Production: `/health` (limited info)

## Best Practices
1. Never commit `.env` files
2. Use environment-specific configurations
3. Test in staging before production
4. Keep local environment close to production
5. Document environment-specific behaviors
6. Regular cleanup of old deployments
7. Monitor resource usage
8. Implement proper logging levels