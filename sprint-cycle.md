# Sprint Cycle

## Overview
We follow a 2-week sprint cycle using Agile/Scrum methodology. This document outlines our sprint process, ceremonies, and best practices.

## Sprint Timeline

### Week 1
| Day | Activity | Time | Duration |
|-----|----------|------|----------|
| Monday | Sprint Planning | 10:00 AM | 2 hours |
| Monday | Sprint Kickoff | 2:00 PM | 30 min |
| Wednesday | Mid-sprint Check-in | 11:00 AM | 30 min |
| Friday | Technical Review | 3:00 PM | 1 hour |
| Daily | Stand-up | 10:00 AM | 15 min |

### Week 2
| Day | Activity | Time | Duration |
|-----|----------|------|----------|
| Monday | Sprint Progress Review | 2:00 PM | 30 min |
| Wednesday | Pre-demo Preparation | 3:00 PM | 1 hour |
| Thursday | Sprint Review/Demo | 2:00 PM | 1 hour |
| Friday | Sprint Retrospective | 10:00 AM | 1 hour |
| Friday | Backlog Grooming | 2:00 PM | 1.5 hours |
| Daily | Stand-up | 10:00 AM | 15 min |

## Sprint Ceremonies

### 1. Sprint Planning

#### Purpose
Define sprint goals and commit to deliverables for the upcoming sprint.

#### Participants
- Product Owner
- Scrum Master
- Development Team
- Stakeholders (optional)

#### Agenda
1. **Sprint Goal Setting** (30 min)
   - Review product roadmap
   - Define sprint objectives
   - Align with business priorities

2. **Backlog Review** (45 min)
   - Review refined user stories
   - Clarify acceptance criteria
   - Identify dependencies

3. **Capacity Planning** (15 min)
   - Account for holidays/PTO
   - Consider meetings/ceremonies
   - Factor in support duties

4. **Story Selection** (30 min)
   - Team pulls stories into sprint
   - Estimate story points
   - Commit to sprint backlog

#### Output
- Sprint goal documented
- Sprint backlog created
- Team commitment confirmed

### 2. Daily Stand-up

#### Format
Each team member answers:
1. What did I complete yesterday?
2. What will I work on today?
3. Are there any blockers?

#### Rules
- Timebox to 15 minutes
- Stand up (if in person)
- No problem-solving during stand-up
- Park detailed discussions for after
- Update ticket status before stand-up

#### Remote Stand-up Tips
- Camera on for engagement
- Use round-robin order
- Share screen for board review
- Post updates in Slack if unable to attend

### 3. Sprint Review/Demo

#### Purpose
Demonstrate completed work and gather feedback.

#### Structure
1. **Sprint Summary** (5 min)
   - Sprint goals recap
   - Metrics overview
   - Completion status

2. **Demonstrations** (40 min)
   - Live demos of completed features
   - Show working software only
   - Focus on user value

3. **Feedback & Discussion** (15 min)
   - Stakeholder questions
   - Feedback collection
   - Next steps identification

#### Demo Best Practices
- Prepare demo environment in advance
- Have backup recordings ready
- Focus on business value, not technical details
- Assign demo presenters during planning
- Practice demos beforehand

### 4. Sprint Retrospective

#### Purpose
Continuous improvement through team reflection.

#### Format Options

##### Start, Stop, Continue
- **Start**: What should we start doing?
- **Stop**: What should we stop doing?
- **Continue**: What's working well?

##### 4 L's
- **Liked**: What went well?
- **Learned**: What did we learn?
- **Lacked**: What was missing?
- **Longed for**: What do we wish we had?

#### Process
1. Set the stage (5 min)
2. Gather data (20 min)
3. Generate insights (15 min)
4. Decide actions (15 min)
5. Close retrospective (5 min)

#### Action Items
- Limit to 2-3 actionable improvements
- Assign owners to each action
- Review previous sprint's actions
- Track in sprint backlog

### 5. Backlog Grooming/Refinement

#### Activities
- Break down epics into stories
- Add/update acceptance criteria
- Estimate story points
- Identify dependencies
- Prioritize backlog items
- Remove obsolete stories

#### Story Readiness Checklist
- [ ] Clear description
- [ ] Acceptance criteria defined
- [ ] Story points estimated
- [ ] Dependencies identified
- [ ] Technical approach discussed
- [ ] Test scenarios outlined

## Story Point Estimation

### Fibonacci Scale
We use Fibonacci numbers: 1, 2, 3, 5, 8, 13, 21

### Reference Stories
| Points | Reference | Complexity |
|--------|-----------|------------|
| 1 | Simple text change | Trivial |
| 2 | Basic CRUD operation | Simple |
| 3 | Standard feature | Moderate |
| 5 | Complex feature | Significant |
| 8 | Multiple integrations | Complex |
| 13 | Architectural change | Very Complex |
| 21 | Epic - needs breakdown | Too Large |

### Estimation Techniques

#### Planning Poker
1. Present story
2. Discuss briefly
3. Everyone selects card
4. Reveal simultaneously
5. Discuss outliers
6. Re-estimate if needed

#### T-Shirt Sizing
- **XS**: 1 point
- **S**: 2-3 points
- **M**: 5 points
- **L**: 8 points
- **XL**: 13 points
- **XXL**: Break it down

## Sprint Metrics

### Velocity
- Average story points completed per sprint
- Track trend over last 6 sprints
- Use for capacity planning

### Burndown Chart
- Daily tracking of remaining work
- Identify sprint risks early
- Adjust scope if needed

### Sprint Goals Achievement
- Percentage of sprint goals met
- Track over time
- Identify patterns

### Quality Metrics
- Bugs found in sprint
- Bugs escaped to production
- Test coverage
- Code review turnaround

## Definition of Done (DoD)

### Code Complete
- [ ] Code written and committed
- [ ] Unit tests written and passing
- [ ] Code reviewed and approved
- [ ] Documentation updated
- [ ] No critical linting errors

### Testing Complete
- [ ] Integration tests passing
- [ ] Manual testing completed
- [ ] Edge cases tested
- [ ] Performance validated
- [ ] Security scan passed

### Ready for Production
- [ ] Deployed to staging
- [ ] Product Owner acceptance
- [ ] Release notes prepared
- [ ] Monitoring configured
- [ ] Feature flag configured (if applicable)

## Sprint Roles

### Product Owner
- Maintains product backlog
- Defines acceptance criteria
- Prioritizes work
- Accepts completed stories
- Communicates with stakeholders

### Scrum Master
- Facilitates ceremonies
- Removes blockers
- Coaches team on Agile
- Tracks sprint metrics
- Protects team from disruptions

### Development Team
- Estimates stories
- Commits to sprint work
- Self-organizes work
- Delivers increment
- Participates in ceremonies

## Sprint Anti-patterns

### To Avoid
❌ Changing sprint scope mid-sprint
❌ Skipping ceremonies
❌ Not updating tickets daily
❌ Working on non-sprint items
❌ Carrying over unfinished stories
❌ No clear sprint goal
❌ Ignoring retrospective actions

### Warning Signs
- Consistently missing sprint goals
- High story carryover rate
- Low team morale
- Increasing technical debt
- Stakeholder dissatisfaction
- Velocity declining

## Tools & Artifacts

### Project Management
- **Tool**: Jira/Linear/Trello
- **Boards**: Sprint board, Backlog
- **Reports**: Velocity, Burndown, Cumulative flow

### Communication
- **Slack Channels**:
  - #dev-standup
  - #sprint-planning
  - #sprint-demos

### Documentation
- Sprint goal document
- Retrospective notes
- Demo recordings
- Sprint metrics dashboard

## Remote Sprint Considerations

### Virtual Ceremonies
- Use video for better engagement
- Share screens for visual aids
- Record important sessions
- Use collaborative tools (Miro, Figma)

### Async Updates
- Daily written standups option
- Record demos for different timezones
- Document decisions clearly
- Over-communicate status

### Team Building
- Virtual coffee breaks
- Sprint celebration rituals
- Team health checks
- Regular 1-on-1s

## Continuous Improvement

### Sprint Process Evolution
- Review process quarterly
- Experiment with new techniques
- Gather team feedback regularly
- Adapt to team needs

### Learning & Development
- Share knowledge sessions
- Technical spikes
- Innovation time
- Cross-training opportunities

## Emergency Procedures

### Mid-Sprint Critical Issues
1. Assess impact and urgency
2. Consult Product Owner
3. Decide: Fix now or next sprint
4. If fixing now:
   - Identify what to remove
   - Update sprint backlog
   - Communicate to stakeholders

### Sprint Failure Recovery
1. Stop and assess
2. Hold emergency retrospective
3. Identify root causes
4. Adjust next sprint capacity
5. Implement preventive measures

## Success Indicators

### Healthy Sprint Signs
✅ Consistent velocity
✅ High story completion rate
✅ Active participation in ceremonies
✅ Quick blocker resolution
✅ Positive team morale
✅ Stakeholder satisfaction
✅ Continuous improvement

### Team Satisfaction
- Regular pulse surveys
- Open feedback culture
- Celebrated successes
- Learning from failures
- Work-life balance maintained