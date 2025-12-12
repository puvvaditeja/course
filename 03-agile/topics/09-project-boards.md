# Project Boards

## Overview

Project boards are visual tools that help Agile teams manage and track their work. By making work visible, teams can better coordinate, identify bottlenecks, and maintain flow. The most common types are Kanban boards and Sprint boards.

---

## Kanban Boards

### What is a Kanban Board?

**Kanban** is a Japanese term meaning "visual signal" or "card." A Kanban board visualizes work, limits work-in-progress, and maximizes flow.

**Origins:**
- Developed by Toyota in manufacturing
- Adapted for software development
- Core Lean/Agile practice
- Can be used standalone or with Scrum

### Basic Kanban Board Structure

```
┌──────────┬──────────────┬────────────┬────────┐
│  To Do   │ In Progress  │   Review   │  Done  │
├──────────┼──────────────┼────────────┼────────┤
│          │              │            │        │
│ [Story 1]│  [Story 2]   │ [Story 5]  │[Story 7]
│          │              │            │        │
│ [Story 3]│  [Story 4]   │            │[Story 8]
│          │              │            │        │
│ [Story 6]│              │            │[Story 9]
│          │              │            │        │
│          │              │            │        │
└──────────┴──────────────┴────────────┴────────┘
```

### Key Principles of Kanban

**1. Visualize Work**
- Make all work visible
- Use cards to represent work items
- Show work state clearly
- Create shared understanding

**2. Limit Work in Progress (WIP)**
- Set maximum items per column
- Prevents overload
- Encourages completion
- Identifies bottlenecks

**3. Manage Flow**
- Optimize work movement
- Minimize wait times
- Smooth handoffs
- Continuous delivery

**4. Make Policies Explicit**
- Clear column definitions
- Entry/exit criteria
- Definition of Done
- Team agreements

**5. Improve Collaboratively**
- Use metrics (lead time, cycle time)
- Identify bottlenecks
- Experiment with improvements
- Evolve board as needed

**6. Implement Feedback Loops**
- Daily standup
- Replenishment meetings
- Retrospectives
- Metrics review

### Workflow Columns

#### Standard Columns

**To Do (Backlog):**
```
┌──────────┐
│  To Do   │
├──────────┤
│[Story A] │
│[Story B] │
│[Story C] │
│[Story D] │
│[Story E] │
└──────────┘

Purpose: Work ready to be started
Criteria: Refined, estimated, prioritized
WIP Limit: Usually none (or very high)
```

**In Progress (Development):**
```
┌──────────────┐
│ In Progress  │  WIP: 3
├──────────────┤
│  [Story A]   │  👤 Alice
│  [Story B]   │  👤 Bob
│  [Story C]   │  👤 Carol
└──────────────┘

Purpose: Active work
Criteria: Team member assigned, actively coding
WIP Limit: Typically 1-2 per team member
```

**Review (Code Review/QA):**
```
┌────────────┐
│   Review   │  WIP: 2
├────────────┤
│ [Story A]  │  🔍 Reviewing
│ [Story D]  │  🔍 Testing
└────────────┘

Purpose: Verification before completion
Criteria: Code complete, needs review/testing
WIP Limit: Based on review capacity
```

**Done:**
```
┌────────┐
│  Done  │
├────────┤
│[Story E]
│[Story F]
│[Story G]
│[Story H]
└────────┘

Purpose: Completed work
Criteria: Meets Definition of Done
WIP Limit: None
```

#### Advanced Workflow Columns

**Detailed Software Development Board:**

```
┌─────┬──────┬──────┬────────┬──────┬────────┬──────┬──────┐
│Back │Ready │Devel │Code    │Test  │Deploy  │Review│Done  │
│log  │      │      │Review  │      │Staging │      │      │
├─────┼──────┼──────┼────────┼──────┼────────┼──────┼──────┤
│  📋 │  ✓   │  💻  │   🔍   │  🧪  │   🚀   │  👀  │  ✅  │
│     │ WIP:5│ WIP:4│  WIP:2 │ WIP:3│  WIP:2 │      │      │
└─────┴──────┴──────┴────────┴──────┴────────┴──────┴──────┘
```

**Column Definitions:**

```
Backlog:
- All potential work
- Prioritized by PO
- Not yet refined

Ready:
- Refined and estimated
- Acceptance criteria clear
- Ready to start
- WIP limit: 5

Development:
- Actively coding
- Unit tests being written
- WIP limit: 4 (team of 5)

Code Review:
- PR submitted
- Awaiting review
- WIP limit: 2

Testing:
- QA testing
- Integration tests
- WIP limit: 3

Deploy Staging:
- Deployed to staging
- Final verification
- WIP limit: 2

Review:
- Demo to stakeholders
- Final acceptance
- No WIP limit

Done:
- Meets DoD
- Deployed to production
- No WIP limit
```

### Work-in-Progress (WIP) Limits

**What are WIP Limits?**

Maximum number of items allowed in a column at one time.

**Why Use WIP Limits?**

```
Without WIP Limits:
┌──────────────┐
│ In Progress  │
├──────────────┤
│  [Story A]   │  Started, 20% done
│  [Story B]   │  Started, 30% done
│  [Story C]   │  Started, 10% done
│  [Story D]   │  Started, 40% done
│  [Story E]   │  Started, 25% done
│  [Story F]   │  Started, 15% done
└──────────────┘

Problems:
❌ Context switching
❌ Nothing finishing
❌ Hidden bottlenecks
❌ No focus

With WIP Limits:
┌──────────────┐
│ In Progress  │  WIP: 3
├──────────────┤
│  [Story A]   │  80% done
│  [Story B]   │  90% done
│  [Story C]   │  70% done
└──────────────┘

Benefits:
✓ Focus on completion
✓ Faster flow
✓ Less context switching
✓ Bottlenecks visible
```

**Setting WIP Limits:**

```
General Guidelines:

Per Person Limits:
- 1 item: Strict focus (pair/mob programming)
- 2 items: Balance (one active, one blocked)
- 3+ items: Too much context switching

Column Limits:
In Progress: Number of developers × 1.5
Code Review: Number of reviewers × 2
Testing: QA capacity

Example Team (5 developers, 1 QA):
┌──────────────┬────────────┬────────┐
│ Development  │   Review   │  Test  │
│   WIP: 7     │   WIP: 3   │ WIP: 2 │
└──────────────┴────────────┴────────┘
```

**When WIP Limit Reached:**

```
Scenario: Development column at WIP limit

┌──────────────┐
│ Development  │  WIP: 3/3  ⚠️ FULL
├──────────────┤
│  [Story A]   │
│  [Story B]   │
│  [Story C]   │
└──────────────┘

Options:
1. Help complete existing work
2. Pull downstream work (review, test)
3. Pair program to finish faster
4. Work on process improvements
5. Documentation, learning

Don't:
❌ Start new work
❌ Increase WIP limit without discussion
❌ Hide work (shadow boards)
```

### Card Details

**What to Include on Cards:**

```
┌─────────────────────────────────────┐
│ #1234  👤 Alice  📅 Sprint 23      │  ID, Owner, Sprint
├─────────────────────────────────────┤
│ User Profile Editing                │  Title
│                                     │
│ As a user, I want to edit my        │  User story
│ profile details...                  │
│                                     │
│ Story Points: 5                     │  Estimate
│ Priority: High                      │  Priority
│                                     │
│ ☐ API endpoint                      │  Tasks
│ ☑ Frontend form                     │
│ ☐ Validation                        │
│                                     │
│ Blocked by: API design              │  Blockers
└─────────────────────────────────────┘
```

**Color Coding:**

```
Card Colors by Type:
🟦 Blue:   Feature
🟩 Green:  Bug fix
🟨 Yellow: Technical debt
🟥 Red:    Critical/urgent
🟪 Purple: Research/spike

Card Colors by Priority:
🔴 High priority
🟡 Medium priority
🟢 Low priority

Card Colors by Team:
🔵 Backend team
🟢 Frontend team
🟣 Full-stack
```

---

## Sprint Boards

### Scrum Board vs Kanban Board

**Kanban Board:**
- Continuous flow
- No time-boxes
- Work pulled as capacity allows
- Focus on flow and throughput
- No iterations

**Scrum Board:**
- Time-boxed sprints
- Sprint-specific work
- Sprint commitment
- Reset each sprint
- Focus on sprint goal

### Sprint Board Structure

```
┌─────────────────────────────────────────────────────────┐
│              Sprint 23 - April 1-14                     │
│        Sprint Goal: Complete User Profile Features      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────┬──────────────┬────────────┬────────┐     │
│  │ To Do   │ In Progress  │   Review   │  Done  │     │
│  ├─────────┼──────────────┼────────────┼────────┤     │
│  │         │              │            │        │     │
│  │[Story 1]│  [Story 2]   │ [Story 5]  │[Story 7]│     │
│  │  5 pts  │   8 pts      │  3 pts     │ 5 pts  │     │
│  │         │              │            │        │     │
│  │[Story 3]│  [Story 4]   │            │[Story 8]│     │
│  │  3 pts  │   5 pts      │            │ 3 pts  │     │
│  │         │              │            │        │     │
│  │[Story 6]│              │            │        │     │
│  │  2 pts  │              │            │        │     │
│  └─────────┴──────────────┴────────────┴────────┘     │
│                                                         │
│  Committed: 31 pts  Completed: 8 pts  Remaining: 23 pts│
└─────────────────────────────────────────────────────────┘
```

### Sprint Board Practices

**Sprint Planning:**
```
1. Start with empty "To Do" column
2. Pull stories from product backlog
3. Add to "To Do" until capacity reached
4. Team commits to sprint goal
5. Board represents sprint backlog
```

**During Sprint:**
```
Daily:
- Team gathers at board
- Each person updates their cards
- Move cards left to right
- Identify blockers
- Coordinate work

Visual Signs:
🚫 Red dot: Blocked
⏱️ Clock: Waiting
👥 Multiple dots: Collaboration needed
```

**End of Sprint:**
```
Sprint Review:
- Show cards in "Done" column
- Demo completed work
- Incomplete cards discussed

Sprint Retrospective:
- Review board flow
- Identify bottlenecks
- Improve for next sprint

Next Sprint:
- Archive or clear current board
- Incomplete work returns to backlog
- Start fresh board for new sprint
```

### Task Breakdown on Sprint Board

**Story Card with Task Breakdown:**

```
┌─────────────────────────────────────┐
│ #1234  User Login Feature    5 pts  │
├─────────────────────────────────────┤
│ Tasks:                              │
│ ☑ Create API endpoint         Alice │
│ ☐ Frontend form               Bob   │
│ ☐ Validation                  Carol │
│ ☐ Error handling              ?     │
│ ☐ Unit tests                  ?     │
│ ☐ Integration tests           ?     │
│                                     │
│ Status: 1/6 tasks complete          │
└─────────────────────────────────────┘
```

**Task Board (Alternative View):**

```
Story: User Login (5 pts)
┌──────────┬──────────────┬────────┐
│  To Do   │ In Progress  │  Done  │
├──────────┼──────────────┼────────┤
│[Error    │[Frontend     │[API    │
│ handling]│ form]   Bob  │endpoint│
│          │              │ Alice] │
│[Unit     │[Validation]  │        │
│ tests]   │ Carol        │        │
│          │              │        │
│[Int.     │              │        │
│ tests]   │              │        │
└──────────┴──────────────┴────────┘
```

---

## Visual Management

### Making Work Visible

**Physical Boards:**

```
Advantages:
✓ Visible to whole team
✓ Tactile (move real cards)
✓ Information radiator
✓ Team gathering point
✓ No login required
✓ High engagement

Disadvantages:
✗ Manual updates
✗ Not accessible remotely
✗ Limited history
✗ Can't generate reports
✗ Physical space needed

Best for:
- Co-located teams
- War rooms
- High collaboration
```

**Digital Boards:**

```
Advantages:
✓ Remote accessibility
✓ Automatic updates
✓ Historical data
✓ Reporting and metrics
✓ Integrations
✓ Searchable

Disadvantages:
✗ Less visible
✗ Requires tools/accounts
✗ Can be "out of sight, out of mind"
✗ Less tactile

Best for:
- Distributed teams
- Multiple stakeholders
- Metric tracking
- Large backlogs
```

**Hybrid Approach:**

```
Many teams use both:
- Physical board in team room
- Digital board (Jira, etc.) for:
  - Remote workers
  - Stakeholders
  - Reporting
  - History

Synchronization:
- Update both daily
- One person responsible
- Or use screen displaying digital board
```

### Visual Signals

**Blockers:**

```
┌─────────────────────────────┐
│ Story Card              🚫  │  ← Red flag/sticker
│                             │
│ Blocked by: API team        │
│ Waiting since: 2 days       │
└─────────────────────────────┘
```

**Avatar/Ownership:**

```
┌─────────────────────────────┐
│ Story Card             👤A  │  ← Person's initial/photo
│                             │
│ Owner: Alice                │
└─────────────────────────────┘
```

**Age:**

```
┌─────────────────────────────┐
│ Story Card            📅 7d │  ← Days in current column
│                             │
│ Started: April 1            │
└─────────────────────────────┘

Action: If age > threshold, investigate
```

**Dependencies:**

```
┌─────────────────────────────┐
│ Story A               🔗 B  │  ← Depends on Story B
└─────────────────────────────┘
    │
    └──> Wait for Story B
```

---

## Board Design Examples

### Example 1: Simple Team Board

```
┌──────────┬──────────────┬────────────┬────────┐
│ Backlog  │   Doing      │   Review   │  Done  │
│          │   WIP: 3     │            │        │
├──────────┼──────────────┼────────────┼────────┤
│          │              │            │        │
│ Story A  │  Story B 👤A │  Story E   │Story G │
│ Story C  │  Story D 👤B │            │Story H │
│ Story F  │              │            │Story I │
│          │              │            │        │
└──────────┴──────────────┴────────────┴────────┘

Best for:
- Small teams (3-5 people)
- Simple workflow
- Minimal handoffs
```

### Example 2: Development Team Board

```
┌─────┬────────┬────────┬────────┬────────┬──────┐
│Back │ Ready  │  Dev   │ Code   │  Test  │ Done │
│log  │        │        │ Review │        │      │
│     │ WIP:5  │ WIP:4  │ WIP:2  │ WIP:2  │      │
├─────┼────────┼────────┼────────┼────────┼──────┤
│     │        │        │        │        │      │
│ A   │  B ✓   │ C 👤A  │ E 👤B  │ G 👤QA │  J   │
│ D   │  F ✓   │ H 👤C  │        │        │  K   │
│ I   │  L ✓   │        │        │        │  M   │
│ N   │  O ✓   │        │        │        │      │
│     │  P ✓   │        │        │        │      │
│     │        │        │        │        │      │
└─────┴────────┴────────┴────────┴────────┴──────┘

Best for:
- Standard dev teams
- Clear workflow stages
- Multiple checkpoints
```

### Example 3: Multi-Team Board

```
BACKEND TEAM:
┌──────────┬──────────────┬────────────┬────────┐
│  To Do   │ In Progress  │   Review   │  Done  │
│          │   WIP: 3     │            │        │
└──────────┴──────────────┴────────────┴────────┘

FRONTEND TEAM:
┌──────────┬──────────────┬────────────┬────────┐
│  To Do   │ In Progress  │   Review   │  Done  │
│          │   WIP: 3     │            │        │
└──────────┴──────────────┴────────────┴────────┘

INTEGRATION:
┌──────────┬──────────────┬────────────┬────────┐
│  Ready   │ Integrating  │  Testing   │  Done  │
│          │   WIP: 2     │            │        │
└──────────┴──────────────┴────────────┴────────┘

Best for:
- Multiple specialized teams
- Component-based development
- Integration dependencies
```

### Example 4: Support/Maintenance Board

```
┌────────┬──────────┬──────────┬────────┬────────┐
│ New    │ Triaged  │ In Prog  │ Verify │ Closed │
│ Bugs   │          │ WIP: 3   │        │        │
├────────┼──────────┼──────────┼────────┼────────┤
│        │          │          │        │        │
│🔴 Crit │🔴 Bug A  │🔴 Bug C  │🟢 Bug F│🟢 Bug H│
│        │🟡 Bug B  │🟡 Bug D  │        │🟢 Bug I│
│🟡 Med  │🟢 Bug E  │          │        │🔴 Bug J│
│        │          │          │        │        │
│🟢 Low  │          │          │        │        │
│        │          │          │        │        │
└────────┴──────────┴──────────┴────────┴────────┘

Colors: 🔴 Critical  🟡 Medium  🟢 Low

Best for:
- Support teams
- Bug tracking
- Priority management
```

---

## Metrics from Boards

### Cycle Time

**Definition**: Time from work started to work completed

```
Story Journey:
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│  To Do   │→→→│   Dev    │→→→│  Review  │→→→│   Done   │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
               ↑                               ↑
            Started                         Completed
               │←──── Cycle Time: 5 days ────→│

Metric: Average 3-7 days for typical story
Goal: Minimize cycle time
```

### Lead Time

**Definition**: Time from work requested to work completed

```
Story Journey:
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│ Backlog  │→→→│  To Do   │→→→│  Doing   │→→→│   Done   │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
↑                                              ↑
Created                                    Completed
│←─────────── Lead Time: 12 days ─────────────→│

Metric: Average 7-14 days
Goal: Minimize lead time
```

### Throughput

**Definition**: Number of items completed per time period

```
Week 1: 8 stories completed
Week 2: 7 stories completed
Week 3: 9 stories completed
Week 4: 8 stories completed

Average throughput: 8 stories/week

Use for:
- Capacity planning
- Forecasting
- Trend analysis
```

### Cumulative Flow Diagram

```
Work Items
    ↑
 50 │                    ▓▓▓▓▓▓▓ Done
    │               ▓▓▓▓▓░░░░░░
 40 │          ▓▓▓▓▓▒▒▒▒▒░░░░░░ Review
    │     ▓▓▓▓▓▒▒▒▒▒▒▒▒▒░░░░░░
 30 │▓▓▓▓▓▒▒▒▒▒▒▒▒▒▒▒▒▒░░░░░░░ In Progress
    │░░░░░░░░░░░░░░░░░░░░░░░░░
 20 │░░░░░░░░░░░░░░░░░░░░░░░░░ To Do
    │░░░░░░░░░░░░░░░░░░░░░░░░░
 10 │░░░░░░░░░░░░░░░░░░░░░░░░░
    │
  0 └──┬───┬───┬───┬───┬───┬───→
       W1  W2  W3  W4  W5  W6

Shows:
- Work distribution across states
- Bottlenecks (widening bands)
- Throughput (done band growth)
```

---

## Best Practices

**Board Setup:**
- ✓ Match your actual workflow
- ✓ Start simple, add complexity as needed
- ✓ Clear column definitions
- ✓ Appropriate WIP limits
- ✓ Easy to understand at a glance

**Board Maintenance:**
- ✓ Update daily
- ✓ Keep cards current
- ✓ Remove outdated items
- ✓ Maintain clean "Done" column
- ✓ Regular grooming sessions

**Team Practices:**
- ✓ Daily standup at the board
- ✓ Everyone updates their cards
- ✓ Discuss blockers immediately
- ✓ Celebrate moving to "Done"
- ✓ Respect WIP limits

**Common Mistakes:**
- ✗ Too many columns (complexity)
- ✗ Vague column definitions
- ✗ No WIP limits
- ✗ Stale cards
- ✗ Board doesn't reflect reality
- ✗ Not visible to team
- ✗ No one owns board maintenance

---

## Summary

| Concept | Key Points |
|---------|------------|
| Kanban Board | Visualize workflow, limit WIP, manage flow |
| Sprint Board | Sprint-specific work, supports Scrum ceremonies |
| WIP Limits | Maximize flow, minimize context switching |
| Visual Management | Make work visible, use colors and signals |
| Metrics | Track cycle time, lead time, throughput |
| Board Design | Match actual workflow, keep simple |

## Key Takeaways

- Project boards make work visible and transparent
- Kanban focuses on flow; Scrum boards on sprint commitments
- WIP limits are crucial for maintaining focus and flow
- Column design should match actual workflow
- Visual signals (colors, icons) enhance communication
- Both physical and digital boards have advantages
- Update boards daily for accuracy
- Use metrics from boards to identify improvements
- Start simple and evolve board as team matures
- Board is a tool for team, not management reporting

## Next Topic

Continue to [Agile Tools](./10-agile-tools.md) to explore popular software tools for Agile project management including Jira, Trello, Azure DevOps, and others.
