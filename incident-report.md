# Incident Report Template

## Purpose
This template ensures consistent documentation of incidents, facilitates learning from failures, and helps prevent recurrence. All P1 and P2 incidents require a report within 48 hours of resolution.

## 🚨 Incident Report Template

### 1. Incident Summary

**Date & Time (Start – End):**

**Reported By:**

**Incident ID / Ticket #:**

**Severity Level:** (P1, P2, etc.)

**Status:** (Resolved / Ongoing)

---

### 2. Impact

**Affected Systems/Services:**

**Users Impacted:** (e.g. "20% of customers could not log in")

**Business Impact:** (e.g. downtime, data loss, revenue loss, SLA breach)

---

### 3. Incident Timeline

| Time (UTC) | Event / Action | Responsible |
|------------|---------------|-------------|
| 10:05 | Monitoring alert triggered | System |
| 10:07 | Engineer paged | On-call |
| 10:15 | Database restarted | Ops Team |
| 10:45 | Service restored | Ops Team |

---

### 4. Root Cause

**Technical Cause:** (e.g. "Redis memory exhaustion due to unbounded queue growth")

**Contributing Factors:** (e.g. "Alert thresholds were too high, missing early warnings")

---

### 5. Resolution

**Actions Taken:** (step-by-step how it was resolved)

**Verification:** (tests/checks proving system is stable again)

---

### 6. Follow-Up / Preventive Actions

**Short-Term Fixes:** (e.g. increase Redis memory limit, restart policy)

**Long-Term Fixes:** (e.g. implement backpressure, improve monitoring)

**Owner + Deadline:**

| Action Item | Owner | Deadline | Status |
|------------|-------|----------|--------|
| | | | |

---

### 7. Lessons Learned

**What went well:**

**What could be improved:**

---

## Severity Levels

### P1 - Critical
- Complete service outage
- Payment processing down
- Data breach or security incident
- > 50% users affected
- **Response Time:** Immediate
- **Report Due:** 24 hours

### P2 - High
- Partial service degradation
- < 50% users affected
- Key features unavailable
- Performance severely degraded
- **Response Time:** < 30 minutes
- **Report Due:** 48 hours

### P3 - Medium
- Minor feature issues
- Workaround available
- < 10% users affected
- **Response Time:** < 4 hours
- **Report Due:** 1 week

### P4 - Low
- Cosmetic issues
- Documentation errors
- No user impact
- **Response Time:** Next business day
- **Report Due:** Optional

## Best Practices

### During the Incident
1. **Communicate early and often** - Even if you don't have all the answers
2. **Document everything** - Times, actions, decisions
3. **Focus on resolution first** - Analysis comes later
4. **Escalate when needed** - Don't hesitate to wake people up for P1s

### Writing the Report
1. **Be blameless** - Focus on systems and processes, not individuals
2. **Be specific** - Include exact error messages, metrics, and timestamps
3. **Be honest** - Document what actually happened, not what should have happened
4. **Be actionable** - Every lesson learned should have a corresponding action

### Follow-Up
1. **Share widely** - Send to all engineering teams
2. **Track actions** - Ensure preventive measures are implemented
3. **Review quarterly** - Look for patterns across incidents
4. **Update runbooks** - Incorporate learnings into documentation

## Distribution

### Who Gets the Report
- Engineering team
- Product team
- CEO (P1 incidents)
- Operations Manager (P1 and P2 incidents)
- Customer Support (if customer-facing)

### Where to Store
- Shared drive: `/incidents/YYYY/MM/`
- Teams: Post summary in Incidents channel
- JIRA: Link to incident ticket

## Example Report

### 1. Incident Summary
**Date & Time:** 2024-01-15 10:05 UTC – 10:45 UTC

**Reported By:** Monitoring System / John Doe

**Incident ID:** INC-2024-001

**Severity Level:** P1

**Status:** Resolved

### 2. Impact
**Affected Systems/Services:** Payment API, Transaction Processing

**Users Impacted:** 100% of users unable to complete payments

**Business Impact:** $15,000 in lost transactions, 40 minutes downtime

### 3. Incident Timeline
| Time (UTC) | Event / Action | Responsible |
|------------|---------------|-------------|
| 10:05 | Payment API alerts - response time > 10s | Monitoring |
| 10:07 | On-call engineer paged | PagerDuty |
| 10:10 | Database connection pool exhausted identified | John Doe |
| 10:15 | Database connections increased from 100 to 500 | John Doe |
| 10:20 | Partial recovery, 50% success rate | System |
| 10:30 | Application servers restarted | Jane Smith |
| 10:45 | Full service restored, monitoring normal | System |

### 4. Root Cause
**Technical Cause:** Database connection pool exhausted due to connection leak in new payment validation code deployed at 09:00 UTC

**Contributing Factors:** 
- Load testing didn't simulate production payment patterns
- Connection pool metrics not included in deployment checklist
- Gradual rollout not implemented for payment service

### 5. Resolution
**Actions Taken:**
1. Increased connection pool size as immediate mitigation
2. Identified connection leak in payment validation module
3. Rolled back to previous version
4. Verified all connections properly closed
5. Re-deployed fixed version with connection management

**Verification:**
- Connection pool usage stable at 40%
- Payment success rate at 99.9%
- Response times < 200ms

### 6. Follow-Up / Preventive Actions
**Short-Term Fixes:**
- Add connection pool monitoring to dashboard
- Implement connection timeout (48 hours)

**Long-Term Fixes:**
- Implement connection pooling best practices guide
- Add connection leak detection to CI/CD pipeline
- Implement canary deployments for payment service

| Action Item | Owner | Deadline | Status |
|------------|-------|----------|--------|
| Add pool monitoring | John Doe | 2024-01-20 | In Progress |
| Connection timeout | Jane Smith | 2024-01-22 | Pending |
| Canary deployment | Tech Lead | 2024-02-01 | Planned |

### 7. Lessons Learned
**What went well:**
- Monitoring detected issue quickly
- Team responded within SLA
- Rollback procedure worked smoothly

**What could be improved:**
- Need better load testing for payment flows
- Connection pool metrics should be part of standard monitoring
- Gradual rollout would have limited impact