# Scrum Roles, Artifacts, and Ceremonies

## Overview

Scrum defines specific roles, artifacts, and ceremonies (events) that work together to create an effective framework for product development. Each element has a specific purpose and contributes to the overall success of the team.

---

## Scrum Roles

Scrum defines three key roles, each with distinct responsibilities. Unlike traditional project management, there is no project manager role in Scrum.

### The Scrum Team Structure

```
┌─────────────────────────────────────────┐
│         THE SCRUM TEAM                  │
├─────────────────────────────────────────┤
│                                         │
│  ┌──────────────────┐                   │
│  │  Product Owner   │                   │
│  │  (The "What")    │                   │
│  └──────────────────┘                   │
│                                         │
│  ┌──────────────────┐                   │
│  │  Scrum Master    │                   │
│  │  (The "How")     │                   │
│  └──────────────────┘                   │
│                                         │
│  ┌──────────────────┐                   │
│  │ Development Team │                   │
│  │  (The "Do")      │                   │
│  │  5-9 members     │                   │
│  └──────────────────┘                   │
│                                         │
└─────────────────────────────────────────┘
```

---

### 1. Product Owner

**Primary Responsibility**: Maximize the value of the product and the work of the Development Team.

**Key Responsibilities:**

**Product Backlog Management:**
- Create and maintain the Product Backlog
- Clearly express backlog items
- Order items to achieve goals and missions
- Ensure backlog is visible, transparent, and clear
- Ensure the team understands items to necessary level

**Value Optimization:**
- Define product vision and strategy
- Prioritize features based on business value
- Make decisions about product direction
- Accept or reject work results
- Manage stakeholder expectations

**Stakeholder Engagement:**
- Represent stakeholders to the team
- Represent the team to stakeholders
- Gather and communicate requirements
- Ensure team builds the right thing

**Characteristics of a Good Product Owner:**
- Available and accessible to the team
- Empowered to make decisions
- Knowledgeable about the business domain
- Good communicator
- Visionary yet pragmatic
- Data-driven decision maker
- Customer-focused

**Product Owner's Day:**
```
Morning:
  - Review overnight customer feedback
  - Respond to team questions on backlog items
  - Meet with stakeholders about priorities

During Sprint:
  - Available for team clarifications
  - Refine upcoming backlog items
  - Review completed work
  - Prepare for Sprint Review

End of Sprint:
  - Accept or reject completed work
  - Lead Sprint Review
  - Reprioritize backlog based on feedback
```

**Common Anti-Patterns:**
- Product Owner by committee (no single decision maker)
- Absent or unavailable Product Owner
- Product Owner micromanaging the team
- Development team bypassing Product Owner
- Product Owner with no authority

---

### 2. Scrum Master

**Primary Responsibility**: Ensure Scrum is understood and enacted, helping everyone understand Scrum theory, practices, rules, and values.

**Key Responsibilities:**

**Servant Leadership:**
- Serve the team, not manage them
- Remove impediments
- Facilitate events as needed
- Shield team from external interruptions
- Foster self-organization

**Coaching:**
- Coach the team on Scrum practices
- Help Product Owner with backlog management
- Guide the organization in Scrum adoption
- Facilitate change and improvement

**Facilitation:**
- Ensure all Scrum events happen
- Keep events time-boxed and productive
- Help team resolve conflicts
- Facilitate decision-making

**Protection:**
- Protect team from outside distractions
- Ensure team can focus on Sprint Goal
- Challenge impediments to progress
- Guard against scope changes during sprint

**Service to Product Owner:**
- Techniques for effective backlog management
- Understanding product planning
- Facilitating Scrum events as requested
- Understanding and practicing agility

**Service to Development Team:**
- Coaching in self-organization and cross-functionality
- Removing impediments to progress
- Facilitating Scrum events as requested
- Coaching in organizational environments where Scrum is not fully adopted

**Service to Organization:**
- Leading and coaching Scrum adoption
- Planning Scrum implementations
- Helping employees and stakeholders understand Scrum
- Causing change that increases team productivity

**Characteristics of a Good Scrum Master:**
- Patient and supportive
- Good communicator and facilitator
- Knowledgeable about Scrum
- Politically savvy
- Problem solver
- Observant and insightful
- Influential without authority

**Scrum Master's Day:**
```
Morning:
  - Prepare for Daily Scrum
  - Check on impediments from yesterday
  - Review burndown chart

During Day:
  - Facilitate Daily Scrum
  - Work on removing impediments
  - Coach team members
  - Shield team from interruptions
  - Help with Scrum practices

Ongoing:
  - Observe team dynamics
  - Identify improvement opportunities
  - Coordinate with other Scrum Masters
  - Educate stakeholders on Scrum
```

**Common Anti-Patterns:**
- Scrum Master as project manager
- Scrum Master doing team's work
- Scrum Master as only process enforcer
- Part-time Scrum Master (when team needs full-time)
- Scrum Master with no authority or influence

---

### 3. Development Team

**Primary Responsibility**: Deliver a potentially releasable increment of "Done" product at the end of each Sprint.

**Key Characteristics:**

**Self-Organizing:**
- Team decides how to accomplish work
- No one tells them how to turn backlog into increments
- Teams structure and manage their own work
- No sub-teams or hierarchies

**Cross-Functional:**
- Team has all skills needed to create increment
- No dependencies on people outside the team
- Members may have specialized skills but accountability belongs to team

**No Titles:**
- No titles for team members
- Everyone is a "Developer"
- No sub-teams (no testers team, architects team)

**No Individual Ownership:**
- Accountability belongs to team as whole
- No individual code ownership
- Everyone responsible for quality

**Size:**
- Small enough to remain nimble (typically 5-9 members)
- Large enough to complete significant work
- Fewer than 5: may lack skills, limited collaboration
- More than 9: too much coordination complexity

**Key Responsibilities:**

**Delivery:**
- Create releasable increment each sprint
- Meet Definition of Done
- Estimate backlog items
- Track progress toward Sprint Goal

**Quality:**
- Write tests
- Review code
- Refactor as needed
- Maintain technical excellence

**Collaboration:**
- Work together daily
- Help each other
- Share knowledge
- Collectively own the work

**Planning:**
- Forecast what can be done in sprint
- Break down work into tasks
- Adjust plan daily as needed

**Improvement:**
- Identify process improvements
- Implement retrospective action items
- Enhance skills and practices

**Typical Team Composition:**
```
Development Team (7 members):
  - 3-4 Full-stack developers
  - 1-2 Frontend specialists
  - 1 Backend specialist
  - 1 QA engineer
  - (All capable of helping with various tasks)

Note: Actual composition varies by product needs
```

**Development Team's Sprint:**
```
Sprint Planning:
  - Forecast backlog items for sprint
  - Create plan for delivering increment
  - Agree on Sprint Goal

Daily:
  - Attend Daily Scrum (15 minutes)
  - Work on sprint tasks
  - Update task board
  - Collaborate and pair program

End of Sprint:
  - Complete all Definition of Done items
  - Demonstrate work in Sprint Review
  - Reflect in Retrospective
```

**Common Anti-Patterns:**
- Individual assignments instead of team commitment
- Team members working in silos
- Waiting for specific person instead of helping each other
- QA as bottleneck at end of sprint
- Team relying on people outside team

---

## Scrum Artifacts

Artifacts represent work or value and are designed to maximize transparency of key information.

### 1. Product Backlog

**Definition**: An ordered list of everything that is known to be needed in the product. It is the single source of requirements for any changes to be made to the product.

**Characteristics:**
- Never complete—evolves as product and environment evolve
- Dynamic—constantly changes to identify what product needs
- Ordered—highest priority items at top
- Detailed appropriately—top items more detailed than lower ones
- Estimated—items have relative size estimates

**Product Backlog Structure:**
```
Priority | Item                          | Story Points | Status
---------|-------------------------------|--------------|--------
1        | User Authentication           | 13           | Ready
2        | Password Reset Flow           | 8            | Ready
3        | Social Media Login            | 8            | Ready
4        | User Profile Page             | 5            | In Refinement
5        | Email Notifications           | 5            | In Refinement
6        | Dashboard Analytics           | 13           | Rough
7        | Export Data Feature           | ?            | Rough
8        | Dark Mode Theme               | ?            | Idea
9        | Mobile App                    | ?            | Idea
```

**Backlog Refinement:**
- Ongoing activity to add detail, estimates, and order
- Typically consumes no more than 10% of Development Team capacity
- Items for next sprint refined to "Ready" state
- Product Owner and Development Team collaborate

**Ready Criteria (Definition of Ready):**
- Clear and actionable
- Testable
- Estimated
- Small enough for one sprint
- Acceptance criteria defined
- Dependencies identified

**Backlog Ordering Factors:**
- Business value
- Risk
- Dependencies
- Customer priority
- Time sensitivity
- Cost of delay

---

### 2. Sprint Backlog

**Definition**: The set of Product Backlog items selected for the Sprint, plus a plan for delivering the product Increment and realizing the Sprint Goal.

**Components:**
1. **Selected Product Backlog Items**: Stories committed for the sprint
2. **Tasks**: Detailed breakdown of how to complete items
3. **Sprint Goal**: Objective that provides cohesion

**Sprint Backlog Structure:**
```
Sprint Goal: "Enable users to manage their account settings"

Story: User Profile Editing
├── Task: Create profile API endpoints       [Done]
├── Task: Design profile edit form           [Done]
├── Task: Implement form validation          [In Progress]
├── Task: Connect frontend to API            [To Do]
├── Task: Write unit tests                   [To Do]
└── Task: Write integration tests            [To Do]

Story: Password Change
├── Task: Create password change API         [Done]
├── Task: Design password change form        [In Progress]
├── Task: Implement password strength meter  [To Do]
├── Task: Add email confirmation             [To Do]
└── Task: Write tests                        [To Do]
```

**Characteristics:**
- Visible and transparent to all
- Real-time picture of sprint work
- Updated continuously by Development Team
- Only Development Team can change it
- Flexible—tasks can be added/removed during sprint

**Monitoring Sprint Progress:**
- Burndown charts track remaining work
- Daily updates show trend
- Team inspects progress at Daily Scrum
- Can replan if Sprint Goal at risk

---

### 3. Increment

**Definition**: The sum of all Product Backlog items completed during a Sprint combined with increments of all previous Sprints. The Increment must be in usable condition and meet the Definition of Done.

**Characteristics:**
- Must be "Done" according to Definition of Done
- Must be potentially releasable
- Includes all previous increments
- Inspected at Sprint Review
- Product Owner decides whether to release

**Increment Evolution:**
```
Sprint 1 Increment:
  [Feature A v1.0]

Sprint 2 Increment:
  [Feature A v1.0] + [Feature A v1.1] + [Feature B v1.0]

Sprint 3 Increment:
  Previous + [Feature C v1.0] + [Feature B v1.1]

Sprint 4 Increment:
  Previous + [Feature D v1.0] + [Feature A v2.0]
```

**Definition of Done (DoD):**

The Definition of Done is a shared understanding of what it means for work to be complete. It ensures transparency and quality.

**Example Definition of Done:**
```
✓ Code written and reviewed
✓ Unit tests written and passing
✓ Integration tests passing
✓ Code merged to main branch
✓ Documentation updated
✓ Acceptance criteria met
✓ No critical bugs
✓ Performance tested
✓ Security reviewed
✓ Deployed to staging environment
✓ Product Owner accepted
```

**Multiple Levels of Done:**
```
Done for Task:
  ✓ Coded
  ✓ Unit tested

Done for Story:
  ✓ All tasks done
  ✓ Acceptance criteria met
  ✓ Code reviewed
  ✓ Integrated

Done for Sprint:
  ✓ All stories done
  ✓ Sprint Goal achieved
  ✓ Documentation complete
  ✓ Deployed to staging

Done for Release:
  ✓ All sprints done
  ✓ Performance tested
  ✓ User acceptance testing
  ✓ Deployed to production
```

---

## Scrum Ceremonies (Events)

Scrum prescribes five events to create regularity and minimize the need for meetings not defined in Scrum.

### The Sprint (Container Event)

**Duration**: Fixed length of one month or less (typically 2 weeks)

**Purpose**: Container for all other events, creates consistency

**Characteristics:**
- Time-boxed—cannot be extended
- New sprint starts immediately after previous
- No gap between sprints
- Scope may be clarified and renegotiated between Product Owner and Development Team
- Sprint can be cancelled if Sprint Goal becomes obsolete (rare)

**Sprint Timeline (2-week sprint):**
```
Day 1     Day 2-9        Day 10        Day 10
  ↓          ↓             ↓             ↓
Sprint   Development   Sprint        Sprint
Planning  + Daily      Review        Retro
          Scrums
```

---

### 1. Sprint Planning

**Duration**: Maximum 8 hours for one-month sprint (4 hours for 2-week sprint)

**Participants**: Entire Scrum Team

**Purpose**: Plan the work to be performed in the Sprint

**Two Key Questions:**

**Part 1: What can be done this Sprint?**
- Review Product Backlog
- Discuss capacity
- Select items for Sprint
- Craft Sprint Goal

**Part 2: How will the chosen work get done?**
- Break stories into tasks
- Estimate tasks
- Identify dependencies
- Create plan

**Sprint Planning Flow:**
```
1. Product Owner Presents
   ├─ Top priority items
   ├─ Business objectives
   └─ Stakeholder feedback

2. Team Discusses
   ├─ Capacity for sprint
   ├─ Velocity from past sprints
   └─ Team availability

3. Select Backlog Items
   ├─ Pull items from top of backlog
   ├─ Discuss each item
   └─ Confirm understanding

4. Create Sprint Goal
   ├─ One sentence objective
   ├─ Provides cohesion
   └─ Guides decisions

5. Task Breakdown
   ├─ Decompose stories into tasks
   ├─ Identify technical approach
   └─ Estimate hours/tasks

6. Commitment
   ├─ Team commits to Sprint Goal
   ├─ Sprint Backlog created
   └─ Ready to start Sprint
```

**Sprint Goal Examples:**
- "Enable users to complete checkout process"
- "Improve application performance by 50%"
- "Complete user profile management features"
- "Fix all critical bugs and improve stability"

---

### 2. Daily Scrum (Daily Standup)

**Duration**: 15 minutes (time-boxed)

**Participants**: Development Team (required), Scrum Master (facilitates), Product Owner (optional)

**Purpose**: Inspect progress toward Sprint Goal and adapt Sprint Backlog as necessary

**Format**: Each team member answers three questions:
1. What did I do yesterday that helped meet the Sprint Goal?
2. What will I do today to help meet the Sprint Goal?
3. Do I see any impediment that prevents me or the team from meeting the Sprint Goal?

**Example Daily Scrum:**
```
Alice:
  Yesterday: Completed the login API endpoint
  Today: Will work on frontend login form
  Blockers: Need design mockups from UX team

Bob:
  Yesterday: Fixed 3 bugs from testing
  Today: Will continue bug fixing and start code review
  Blockers: None

Carol:
  Yesterday: Wrote unit tests for user service
  Today: Will integrate payment gateway
  Blockers: Need API credentials from Product Owner

Dave:
  Yesterday: Worked on database migration
  Today: Will finish migration and help Carol with payment
  Blockers: Database server was slow yesterday
```

**Best Practices:**
- Same time and place every day
- Stand up (keeps it brief)
- Focus on Sprint Goal
- Discuss details after meeting
- Update task board during or immediately after
- Scrum Master removes impediments
- Not a status report to management

**Common Anti-Patterns:**
- Goes over 15 minutes
- Turns into problem-solving session
- Status report to Scrum Master/Product Owner
- Not everyone participates
- Team doesn't synchronize

---

### 3. Sprint Review

**Duration**: Maximum 4 hours for one-month sprint (2 hours for 2-week sprint)

**Participants**: Scrum Team + Stakeholders

**Purpose**: Inspect the Increment and adapt the Product Backlog if needed

**Activities:**

**1. Demonstration (60-70% of time)**
- Product Owner explains what was "Done"
- Development Team demonstrates working software
- Product Owner discusses Product Backlog status
- Product Owner projects target and delivery dates

**2. Feedback and Discussion (30-40% of time)**
- Stakeholders provide feedback
- Discuss what went well and problems encountered
- Discuss what to do next
- Review timeline, budget, marketplace changes
- Collaborate on next steps

**Sprint Review Flow:**
```
1. Welcome and Introduction (5 min)
   - Product Owner welcomes stakeholders
   - Review Sprint Goal

2. Demo of Completed Work (60 min)
   - Show working software
   - Each feature demonstrated
   - Real data, real scenarios
   - Interactive exploration

3. Stakeholder Feedback (30 min)
   - Questions and answers
   - Gather input on features
   - Discuss usability
   - Identify new requirements

4. Product Backlog Update (20 min)
   - Review updated backlog
   - Discuss priorities
   - Add new items from feedback
   - Re-estimate if needed

5. Next Steps (5 min)
   - Discuss upcoming sprint focus
   - Confirm priorities
   - Set expectations
```

**Demo Best Practices:**
- Show working software, not slides
- Use realistic scenarios and data
- Let stakeholders interact with features
- Be honest about what's incomplete
- Focus on value delivered

**What Sprint Review is NOT:**
- Not a sign-off meeting
- Not a status report
- Not just a demo—it's collaborative
- Not the only time to gather feedback

---

### 4. Sprint Retrospective

**Duration**: Maximum 3 hours for one-month sprint (1.5 hours for 2-week sprint)

**Participants**: Scrum Team (Development Team, Scrum Master, Product Owner)

**Purpose**: Inspect how the last Sprint went regarding people, relationships, process, and tools, and identify improvements

**Format:**

**1. Set the Stage (5-10 min)**
- Create safe environment
- Review retrospective goals
- Set positive tone

**2. Gather Data (15-20 min)**
- What happened during sprint?
- Metrics: velocity, quality, etc.
- Timeline of events

**3. Generate Insights (20-30 min)**
- Why did things happen?
- Identify patterns
- Root cause analysis

**4. Decide What to Do (20-30 min)**
- Brainstorm improvements
- Select top items
- Create action plan

**5. Close (5-10 min)**
- Confirm action items
- Appreciation
- Retrospective evaluation

**Popular Retrospective Formats:**

**Start, Stop, Continue:**
```
START                    STOP                   CONTINUE
- Pair programming       - Long meetings        - Code reviews
- Automated testing      - Context switching    - Daily standups
- Documentation         - Working overtime     - Team lunches
```

**Glad, Sad, Mad:**
```
GLAD                     SAD                    MAD
- Finished all stories   - Lots of bugs found   - Server downtime
- Good teamwork         - Missing requirements - Scope changes
- Learned new tech      - Tight deadline       - Late feedback
```

**4 Ls: Liked, Learned, Lacked, Longed For:**
```
LIKED                    LEARNED                LACKED              LONGED FOR
- Team collaboration     - New framework        - Test coverage     - Better tools
- Clear requirements     - API design           - Documentation     - More feedback
- Quick feedback        - Performance tips     - Time for refactor - Faster builds
```

**Action Items:**
```
Action: Implement automated linting
Owner: Alice
Due: Next sprint
Success Criteria: No linting errors in CI

Action: Weekly backlog refinement
Owner: Product Owner
Due: Start next week
Success Criteria: All sprint+1 stories refined

Action: Reduce meeting time
Owner: Scrum Master
Due: Immediately
Success Criteria: Average meeting time down 25%
```

**Best Practices:**
- Safe, blame-free environment
- Everyone participates equally
- Focus on process, not people
- Actionable improvements
- Follow up on previous action items
- Try different formats to keep fresh
- Limited number of action items (2-3 max)

---

### 5. Backlog Refinement (Grooming)

**Duration**: Typically 5-10% of sprint (2-4 hours for 2-week sprint)

**Participants**: Product Owner + Development Team (whole team or subset)

**Purpose**: Add detail, estimates, and order to Product Backlog items

**Activities:**
- Break down large items
- Add acceptance criteria
- Estimate story points
- Clarify requirements
- Identify dependencies
- Remove obsolete items
- Re-prioritize as needed

**Refinement Goals:**
```
Current Sprint Items: READY
  ↓ Detailed, estimated, understood

Next Sprint Items: Being Refined
  ↓ Adding details, estimating

Future Sprint Items: Rough
  ↓ High-level only
```

**When Items are Ready:**
- Team understands the story
- Acceptance criteria clear
- Estimated (story points)
- Size appropriate (fits in sprint)
- Dependencies identified
- Testable

---

## Summary Table

### Roles Summary

| Role | Primary Focus | Key Responsibility |
|------|---------------|-------------------|
| Product Owner | What to build | Maximize product value |
| Scrum Master | How to work | Enable Scrum effectiveness |
| Development Team | Building it | Deliver working increment |

### Artifacts Summary

| Artifact | Owner | Purpose |
|----------|-------|---------|
| Product Backlog | Product Owner | All work needed for product |
| Sprint Backlog | Development Team | Work for current sprint |
| Increment | Development Team | Potentially releasable product |

### Ceremonies Summary

| Event | Duration (2-week sprint) | Purpose |
|-------|-------------------------|---------|
| Sprint Planning | 4 hours | Plan sprint work |
| Daily Scrum | 15 minutes | Sync and adapt daily |
| Sprint Review | 2 hours | Inspect increment, adapt backlog |
| Sprint Retrospective | 1.5 hours | Inspect process, plan improvements |
| Backlog Refinement | 2-4 hours total | Prepare upcoming work |

## Key Takeaways

- Three roles work together: Product Owner (what), Scrum Master (how), Development Team (do)
- Product Backlog is single source of truth for all product work
- Sprint Backlog is team's plan for achieving Sprint Goal
- Increment must be "Done" and potentially releasable
- Five ceremonies create rhythm and ensure inspect and adapt
- All events are time-boxed to ensure efficiency
- Each event has specific purpose and outcomes
- Regular cadence enables predictability and improvement

## Next Topic

Continue to [Agile User Stories](./05-agile-user-stories.md) to learn how to write effective user stories that drive development.
