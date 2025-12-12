# Agile Process

## Overview

The Agile process is fundamentally different from traditional waterfall development. Instead of attempting to plan and build everything upfront, Agile embraces an iterative and incremental approach that delivers value continuously while adapting to change.

---

## Iterative and Incremental Development

### What is Iterative Development?

**Iterative development** means building software through repeated cycles (iterations), where each cycle refines and improves upon the previous work.

**Key Characteristics:**
- Work is done in small, repeatable cycles
- Each iteration produces a working product increment
- Feedback from each iteration informs the next
- Allows for refinement and course correction
- Reduces risk by validating assumptions early

### What is Incremental Development?

**Incremental development** means building software piece by piece, adding new functionality in each increment.

**Key Characteristics:**
- System grows incrementally over time
- Each increment adds new features or capabilities
- Previous increments remain functional
- Users can start using the system earlier
- Provides flexibility in priority and scope

### Combining Both Approaches

Agile combines iterative and incremental development:

```
Sprint 1: Build Feature A (v1) ────────> Working Product (Feature A)
                                                    ↓
Sprint 2: Refine Feature A (v2) + Build Feature B ──> Working Product (A+B)
                                                    ↓
Sprint 3: Refine A&B + Build Feature C ──────────> Working Product (A+B+C)
                                                    ↓
Sprint 4: Refine All + Build Feature D ──────────> Working Product (A+B+C+D)
```

### Benefits of This Approach

1. **Early Value Delivery**: Users get working software sooner
2. **Risk Reduction**: Problems discovered early when cheaper to fix
3. **Flexibility**: Can adjust direction based on feedback
4. **Better Quality**: Continuous refinement improves quality
5. **Learning**: Team learns and improves with each iteration
6. **Validation**: Assumptions tested with real software

### Comparison with Waterfall

**Waterfall (Sequential):**
```
Month 1-3: Requirements (No working software)
Month 4-6: Design (No working software)
Month 7-9: Implementation (No working software)
Month 10-11: Testing (Finding major issues)
Month 12: Deployment (First time users see product)
```

**Agile (Iterative & Incremental):**
```
Month 1: Sprint 1 → Working Feature Set 1
Month 2: Sprint 2 → Working Feature Set 1+2
Month 3: Sprint 3 → Working Feature Set 1+2+3
...
Each month: Working software with growing functionality
```

---

## The Sprint Cycle

The sprint is the heartbeat of Agile development—a time-boxed period during which specific work must be completed and made ready for review.

### What is a Sprint?

A **sprint** is a fixed-length iteration (typically 1-4 weeks) during which the team creates a potentially shippable product increment.

**Sprint Characteristics:**
- **Time-boxed**: Fixed duration, cannot be extended
- **Goal-oriented**: Each sprint has a clear goal
- **Consistent**: Same duration for all sprints in a project
- **Protected**: Scope changes avoided during sprint
- **Complete**: Results in working, tested software

### Typical Sprint Length

| Duration | Best For | Considerations |
|----------|----------|----------------|
| 1 week | Simple tasks, experienced teams | Very fast feedback, limited scope per sprint |
| 2 weeks | Most common, balanced approach | Good balance of planning and execution |
| 3 weeks | Complex work, less experienced teams | More time for development, slower feedback |
| 4 weeks | Large, complex features | Maximum sprint length, slower adaptation |

**Most teams use 2-week sprints** as the optimal balance.

### Sprint Cycle Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     SPRINT CYCLE (2 weeks)                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Day 1: Sprint Planning (2-4 hours)                         │
│         → Select backlog items                              │
│         → Define sprint goal                                │
│         → Create task breakdown                             │
│                                                             │
│  Days 2-9: Development Work                                 │
│         → Daily Standup (15 min each day)                   │
│         → Build, test, integrate                            │
│         → Update task board                                 │
│         → Collaborate and problem-solve                     │
│                                                             │
│  Day 10: Sprint Review (1-2 hours)                          │
│         → Demo completed work                               │
│         → Gather feedback                                   │
│         → Update product backlog                            │
│                                                             │
│  Day 10: Sprint Retrospective (1-1.5 hours)                 │
│         → Reflect on process                                │
│         → Identify improvements                             │
│         → Commit to action items                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────────┐
│                  NEXT SPRINT CYCLE (2 weeks)                │
└─────────────────────────────────────────────────────────────┘
```

### Daily Workflow During Sprint

```
Morning:
  09:00 - Daily Standup (15 minutes)
  09:15 - Review today's tasks
  09:30 - Start development work

Development:
  - Write code
  - Write tests
  - Code review
  - Integration
  - Update task status

Afternoon:
  - Continue development
  - Pair programming sessions
  - Address blockers
  - Prepare for tomorrow

End of Day:
  - Update task board
  - Commit code
  - Document progress
```

### Sprint Rules

1. **No scope changes during sprint**: Protects team focus
2. **Definition of Done must be met**: Quality standards maintained
3. **Sprint cannot be extended**: Time-box is sacred
4. **Team commits to sprint goal**: Not individual tasks
5. **Product Owner available for questions**: Quick clarification
6. **Working software by end**: Potentially shippable increment

---

## Continuous Improvement

Continuous improvement, or "Kaizen" in Lean methodology, is central to Agile. Teams regularly inspect their work and processes to identify opportunities for enhancement.

### The Inspect and Adapt Cycle

```
        ┌──────────┐
        │  PLAN    │
        └────┬─────┘
             ↓
        ┌────┴─────┐
        │  DO      │
        └────┬─────┘
             ↓
        ┌────┴─────┐
        │  CHECK   │
        └────┬─────┘
             ↓
        ┌────┴─────┐
        │  ACT     │
        └────┬─────┘
             ↓
        (Repeat)
```

### Three Pillars of Empirical Process Control

**1. Transparency**
- All aspects of process visible to those responsible
- Common language and standards
- Shared definition of "Done"
- Open communication

**2. Inspection**
- Regular examination of artifacts and progress
- Daily standups inspect daily progress
- Sprint review inspects product increment
- Sprint retrospective inspects process
- Not so frequent as to impede work

**3. Adaptation**
- Adjust process when inspection reveals issues
- Minimize deviation from goals
- Quick response to problems
- Continuous learning culture

### Mechanisms for Continuous Improvement

**Sprint Retrospectives**
- Held after each sprint
- Team reflects on what worked and what didn't
- Identifies concrete action items
- Implements improvements in next sprint

**Daily Standups**
- Identify blockers immediately
- Adjust daily plans as needed
- Quick course correction

**Sprint Reviews**
- Demonstrate completed work
- Gather stakeholder feedback
- Adjust product direction

**Backlog Refinement**
- Regularly review and update backlog
- Improve story quality and estimates
- Better preparation for future sprints

**Metrics and Measurement**
- Track velocity over time
- Monitor quality metrics (bugs, technical debt)
- Measure cycle time
- Use data to drive improvements

### Continuous Improvement Examples

**Code Quality:**
```
Sprint 1: Manual code reviews
    ↓ (Retrospective insight: reviews taking too long)
Sprint 2: Implement automated linting
    ↓ (Retrospective insight: still finding bugs in production)
Sprint 3: Add automated testing requirements
    ↓ (Retrospective insight: tests running slow)
Sprint 4: Optimize test suite
```

**Communication:**
```
Sprint 1: Remote team, email only
    ↓ (Retrospective insight: slow response times)
Sprint 2: Implement Slack for quick questions
    ↓ (Retrospective insight: important messages getting lost)
Sprint 3: Use Slack channels by topic
    ↓ (Retrospective insight: video calls needed for complex topics)
Sprint 4: Schedule regular video pairing sessions
```

---

## Adaptive Planning

Unlike traditional project management with detailed upfront plans, Agile embraces adaptive planning that responds to reality.

### Levels of Planning in Agile

**1. Product Vision (6-12 months)**
- High-level product goals
- Target market and users
- Key capabilities
- Success metrics

**2. Release Planning (3-6 months)**
- Major features and epics
- Release milestones
- Rough timeline
- Dependencies identified

**3. Sprint Planning (1-4 weeks)**
- Specific user stories
- Detailed task breakdown
- Sprint goal defined
- Team capacity considered

**4. Daily Planning (24 hours)**
- Today's tasks
- Immediate blockers
- Quick adjustments
- Tactical decisions

### Planning Onion Diagram

```
         ┌─────────────────────────┐
         │   Product Vision        │  (6-12 months)
         │  ┌───────────────────┐  │
         │  │ Release Plan      │  │  (3-6 months)
         │  │  ┌─────────────┐  │  │
         │  │  │ Sprint Plan │  │  │  (1-4 weeks)
         │  │  │  ┌───────┐  │  │  │
         │  │  │  │ Daily │  │  │  │  (24 hours)
         │  │  │  └───────┘  │  │  │
         │  │  └─────────────┘  │  │
         │  └───────────────────┘  │
         └─────────────────────────┘

    (More detail and precision at inner levels)
    (More flexibility and change at outer levels)
```

### Adaptive Planning Principles

**1. Plan at Multiple Levels**
- Long-term vision provides direction
- Short-term plans provide detail
- Balance between both

**2. Just-in-Time Planning**
- Detailed planning only when needed
- Avoid planning too far ahead
- Information improves over time

**3. Rolling Wave Planning**
- Plan next iteration in detail
- Rough plan for near-term iterations
- High-level plan for distant future
- Continuously refine plans

**4. Empirical Planning**
- Use actual velocity, not estimates
- Learn from past performance
- Data-driven projections

**5. Collaborative Planning**
- Whole team participates
- Product Owner prioritizes
- Developers estimate
- Shared commitment

### Responding to Change

**When Requirements Change:**
```
Traditional Approach:
  Change Request → Formal Review → Impact Analysis →
  Change Control Board → Approval → Re-planning →
  Documentation Update → Implementation
  (Weeks or months)

Agile Approach:
  New Requirement → Product Backlog → Prioritized →
  Next Sprint or Current Sprint (if critical)
  (Days or hours)
```

**Change Management in Agile:**
1. New requirements go into Product Backlog
2. Product Owner prioritizes against existing work
3. Team estimates during backlog refinement
4. High priority items pulled into next sprint
5. Low priority items deferred or removed

### Planning Artifacts

**Product Roadmap**
```
Q1 2024          Q2 2024          Q3 2024          Q4 2024
┌────────┐       ┌────────┐       ┌────────┐       ┌────────┐
│ User   │       │ Payment│       │ Mobile │       │Analytics│
│ Auth   │───────│ System │───────│ App    │───────│Dashboard│
│        │       │        │       │        │       │         │
└────────┘       └────────┘       └────────┘       └────────┘
   Firm             Firm            Likely          Flexible
```

**Release Burn-up Chart**
```
Story Points
    ↑
 200│                                          ┌─── Target Scope
    │                                      ┌───┘
 150│                               ┌──────┘
    │                        ┌──────┘
 100│                 ┌──────┘
    │          ┌──────┘────── Completed Work
  50│   ┌──────┘
    │───┘
  0 └────────────────────────────────────────────→
    Sprint 1  2  3  4  5  6  7  8  9  10
```

---

## Customer Collaboration

Customer collaboration is a cornerstone of Agile, ensuring the right product is built and value is continuously delivered.

### Why Customer Collaboration Matters

**Traditional Approach Problems:**
- Requirements gathered once at beginning
- Customer sees product at the end
- Misunderstandings discovered too late
- Market conditions change during development
- No opportunity for course correction

**Agile Approach Benefits:**
- Continuous feedback throughout development
- Regular demonstrations of working software
- Rapid response to changing needs
- Build trust and partnership
- Deliver exactly what customer needs

### Forms of Customer Collaboration

**1. Sprint Reviews (Every Sprint)**
- Demo completed functionality
- Gather immediate feedback
- Discuss upcoming priorities
- Adjust product backlog
- Celebrate progress together

**2. Backlog Refinement (Weekly or Bi-weekly)**
- Review and clarify upcoming stories
- Discuss acceptance criteria
- Estimate complexity together
- Ensure shared understanding

**3. On-Demand Collaboration (As Needed)**
- Quick questions during development
- Clarify requirements
- Review UI/UX designs
- Discuss technical constraints

**4. Release Planning (Quarterly or as Needed)**
- Align on high-level goals
- Discuss major features
- Set release timelines
- Review market conditions

### Customer Collaboration Best Practices

**1. Define "Customer" Clearly**
- Internal customers: Other departments, management
- External customers: End users, paying clients
- Proxy customers: Product Owner, subject matter experts
- User representatives: Beta testers, focus groups

**2. Set Expectations**
- Time commitment required
- Response time expectations
- Decision-making authority
- Communication channels

**3. Make Collaboration Easy**
- Convenient meeting times
- Remote participation options
- Recorded demos for review
- Shared collaboration tools

**4. Active Listening**
- Ask open-ended questions
- Paraphrase to confirm understanding
- Observe non-verbal cues
- Dig deeper into requirements

**5. Visualize Ideas**
- Wireframes and mockups
- Prototypes
- User flow diagrams
- Example data

### Customer Collaboration Anti-Patterns

**Avoid These Mistakes:**

1. **Waterfall in Agile Clothing**
   - Gathering all requirements upfront
   - No customer involvement during sprints
   - Single big release at end

2. **No Real Decision Maker**
   - Customer proxy with no authority
   - Decisions constantly overturned
   - Endless rounds of approval

3. **Overwhelming the Customer**
   - Too many meetings
   - Too much detail
   - No respect for customer's time

4. **Ignoring Feedback**
   - Collecting feedback but not acting on it
   - Defending decisions instead of listening
   - Continuing with original plan regardless

5. **Over-reliance on Customer**
   - Expecting customer to design solution
   - No team ownership of product
   - Customer dictating technical decisions

### Measuring Customer Collaboration

**Quality Indicators:**
- Customer satisfaction scores
- Frequency of feedback incorporation
- Speed of requirement clarification
- Number of rework cycles
- Customer participation in events
- Net Promoter Score (NPS)

---

## The Agile Development Cycle

### Complete Picture

```
                    ┌──────────────────┐
                    │  Product Vision  │
                    └────────┬─────────┘
                             ↓
                    ┌────────┴─────────┐
                    │ Product Backlog  │ ← Customer Input
                    │  (Prioritized)   │
                    └────────┬─────────┘
                             ↓
        ┌────────────────────┴────────────────────┐
        │         SPRINT CYCLE (2 weeks)          │
        ├─────────────────────────────────────────┤
        │                                         │
        │  Sprint Planning                        │
        │    ↓                                    │
        │  Development (with Daily Standups)      │
        │    ↓                                    │
        │  Sprint Review ──→ Customer Feedback    │
        │    ↓                                    │
        │  Sprint Retrospective ──→ Process       │
        │                          Improvements   │
        └─────────────────┬───────────────────────┘
                          ↓
                 ┌────────┴─────────┐
                 │ Product Increment│
                 │ (Potentially     │
                 │  Shippable)      │
                 └────────┬─────────┘
                          ↓
                    (Next Sprint)
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Iterative & Incremental | Build in cycles, adding functionality each iteration |
| Sprint Cycle | Fixed time-box (1-4 weeks) producing working software |
| Continuous Improvement | Regular inspection and adaptation through retrospectives |
| Adaptive Planning | Plan at multiple levels, adjust based on reality |
| Customer Collaboration | Continuous engagement for feedback and direction |

## Key Takeaways

- Agile process is based on iterative and incremental development
- Sprints provide a regular rhythm for delivery and improvement
- Continuous improvement through inspect and adapt cycles
- Planning happens at multiple levels with increasing detail
- Customer collaboration ensures building the right product
- Each sprint produces potentially shippable software
- Teams adapt quickly based on feedback and learning

## Next Topic

Continue to [Scrum Framework](./03-scrum-framework.md) to learn about the most popular Agile framework in detail.
