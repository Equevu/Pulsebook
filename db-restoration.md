# Database Restoration & Keys Rotation Policy

## Database Backup Strategy

### Backup Types

#### 1. Automated Backups
- **Frequency**: Every 6 hours
- **Retention**: 30 days
- **Type**: Incremental
- **Storage**: Cloud storage with encryption
- **Verification**: Automated integrity checks

#### 2. Daily Snapshots
- **Frequency**: Daily at 2 AM UTC
- **Retention**: 90 days
- **Type**: Full backup
- **Storage**: Cross-region replication
- **Purpose**: Point-in-time recovery

#### 3. Weekly Archives
- **Frequency**: Weekly (Sundays)
- **Retention**: 1 year
- **Type**: Full backup + transaction logs
- **Storage**: Cold storage
- **Purpose**: Compliance and long-term recovery

### Backup Locations
```
Primary:   AWS S3 - us-east-1
Secondary: AWS S3 - eu-west-1
Archive:   AWS Glacier
Local:     On-premise NAS (production only)
```

## Database Restoration Procedures

### 1. Point-in-Time Recovery (PITR)

#### Prerequisites
- Identify target recovery time
- Verify backup availability
- Notify stakeholders
- Prepare restoration environment

#### Steps
```bash
# 1. Stop application services
kubectl scale deployment app --replicas=0

# 2. Create restoration point
pg_dump production_db > backup_$(date +%Y%m%d_%H%M%S).sql

# 3. Restore to specific time
pg_restore --dbname=production_db \
          --time="2024-01-15 14:30:00" \
          --verbose \
          backup_file.sql

# 4. Verify restoration
psql -d production_db -c "SELECT COUNT(*) FROM critical_table;"

# 5. Restart services
kubectl scale deployment app --replicas=3
```

### 2. Full Database Recovery

#### From Latest Backup
```bash
# Download latest backup
aws s3 cp s3://backups/latest/db_backup.sql.gz .

# Decompress
gunzip db_backup.sql.gz

# Drop existing database (CAUTION!)
dropdb production_db

# Create new database
createdb production_db

# Restore
psql production_db < db_backup.sql

# Verify
./scripts/verify_restoration.sh
```

#### From Specific Date
```bash
# List available backups
aws s3 ls s3://backups/ --recursive | grep "2024-01-15"

# Select and restore specific backup
aws s3 cp s3://backups/2024-01-15/db_backup.sql.gz .
# Follow steps above
```

### 3. Partial Restoration

#### Single Table Recovery
```bash
# Extract specific table from backup
pg_restore --table=users \
          --data-only \
          --dbname=temp_db \
          backup_file.sql

# Copy to production
pg_dump --table=users temp_db | psql production_db
```

#### Schema-Only Recovery
```bash
pg_restore --schema-only \
          --dbname=production_db \
          backup_file.sql
```

## Restoration Testing

### Monthly Drill Schedule
- **1st Monday**: Development environment
- **2nd Monday**: Staging environment  
- **3rd Monday**: Disaster recovery site
- **4th Monday**: Review and update procedures

### Test Scenarios
1. Complete database failure
2. Corrupted tables
3. Accidental deletion
4. Ransomware attack
5. Point-in-time recovery

### Success Metrics
- Recovery Time Objective (RTO): < 4 hours
- Recovery Point Objective (RPO): < 1 hour
- Data integrity: 100%
- Application functionality: Full restoration

## Keys Rotation Policy

### Rotation Schedule

| Key Type | Rotation Frequency | Grace Period | Automation |
|----------|-------------------|--------------|------------|
| Database passwords | 90 days | 7 days | Yes |
| API keys | 180 days | 14 days | Yes |
| JWT secrets | 365 days | 30 days | Manual |
| Encryption keys | 365 days | 30 days | Manual |
| Service account keys | 90 days | 7 days | Yes |
| SSH keys | 180 days | 14 days | Manual |
| TLS certificates | 365 days | 30 days | Yes |

### Automated Rotation Process

#### Database Passwords
```bash
# Automated script runs via cron
0 0 1 */3 * /scripts/rotate_db_passwords.sh

# Script workflow
1. Generate new password
2. Update database user
3. Update application configs
4. Deploy new configs
5. Verify connectivity
6. Remove old password after grace period
```

#### API Keys
```python
# Rotation script
def rotate_api_keys():
    # Generate new key
    new_key = generate_secure_key()
    
    # Store in vault
    vault.write('api/keys/new', new_key)
    
    # Update applications
    update_app_configs(new_key)
    
    # Set expiration for old key
    schedule_key_deletion(old_key, days=14)
    
    # Log rotation
    audit_log.record('API_KEY_ROTATED', metadata)
```

### Manual Rotation Procedures

#### JWT Secrets
1. Generate new secret
   ```bash
   openssl rand -base64 64
   ```

2. Update environment variables
   ```bash
   kubectl create secret generic jwt-secret \
     --from-literal=JWT_SECRET=new_secret_here \
     --dry-run=client -o yaml | kubectl apply -f -
   ```

3. Rolling deployment
   ```bash
   kubectl rollout restart deployment/api
   ```

4. Monitor for issues
   ```bash
   kubectl logs -f deployment/api
   ```

#### SSH Keys
1. Generate new key pair
   ```bash
   ssh-keygen -t ed25519 -f ~/.ssh/new_key -C "service@pulsebook.com"
   ```

2. Distribute public key
   ```bash
   ssh-copy-id -i ~/.ssh/new_key.pub user@server
   ```

3. Update automation scripts
4. Remove old keys after verification

### Emergency Rotation

#### Triggers for Immediate Rotation
- Security breach detected
- Employee termination (privileged access)
- Key exposure in logs/code
- Compliance audit finding
- Third-party compromise

#### Emergency Procedure
1. **Immediate Actions** (< 15 minutes)
   - Revoke compromised credentials
   - Generate new credentials
   - Update critical systems

2. **Follow-up** (< 1 hour)
   - Deploy to all environments
   - Verify all services operational
   - Document incident

3. **Post-Incident** (< 24 hours)
   - Root cause analysis
   - Update rotation policies
   - Security audit

## Secrets Management

### Storage Hierarchy
```
Production Secrets:  HashiCorp Vault
Staging Secrets:     AWS Secrets Manager
Development:         Local .env files (git-ignored)
CI/CD:              GitHub Secrets / Jenkins Credentials
```

### Access Control
- **Principle of Least Privilege**
- **Role-based access**
- **Audit logging enabled**
- **MFA required for production**

### Best Practices
1. Never commit secrets to version control
2. Use strong, unique passwords (min 32 chars)
3. Implement secret scanning in CI/CD
4. Regular audit of access logs
5. Automate rotation where possible
6. Document all manual procedures
7. Test rotation procedures regularly

## Compliance & Auditing

### Audit Requirements
- Log all backup operations
- Track restoration attempts
- Record key rotations
- Monitor access patterns
- Generate compliance reports

### Compliance Standards
- **SOC 2**: Annual audit
- **GDPR**: Data protection
- **HIPAA**: If applicable
- **PCI DSS**: Payment data

### Audit Log Format
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "action": "KEY_ROTATION",
  "resource": "database_password",
  "user": "automation@pulsebook.com",
  "status": "SUCCESS",
  "metadata": {
    "old_key_id": "key_123",
    "new_key_id": "key_456",
    "environment": "production"
  }
}
```

## Disaster Recovery Plan

### RTO/RPO Targets
- **Production**: RTO 4 hours, RPO 1 hour
- **Staging**: RTO 8 hours, RPO 4 hours
- **Development**: RTO 24 hours, RPO 24 hours

### Recovery Procedures
1. Assess damage scope
2. Activate incident response team
3. Execute restoration procedure
4. Verify data integrity
5. Resume operations
6. Post-incident review

### Contact Information
- **Primary DBA**: [Contact Info]
- **Backup DBA**: [Contact Info]
- **Security Team**: [Contact Info]
- **On-call Engineer**: [Pager Duty]

## Monitoring & Alerts

### Backup Monitoring
```yaml
alerts:
  - name: backup_failed
    condition: backup_status != "SUCCESS"
    severity: critical
    notify: [oncall, dba-team]
    
  - name: backup_delayed
    condition: backup_age > 8 hours
    severity: warning
    notify: [dba-team]
```

### Key Rotation Alerts
- Keys approaching expiration (30, 14, 7 days)
- Rotation failure
- Unauthorized access attempts
- Unusual activity patterns

## Documentation & Training

### Required Documentation
- Runbooks for each procedure
- Architecture diagrams
- Contact lists
- Escalation procedures
- Lessons learned

### Training Schedule
- Quarterly DR drills
- Annual security training
- New employee onboarding
- Post-incident reviews