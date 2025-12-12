# Burndown and Burnup Charts

## Overview

Burndown and burnup charts are visual tools used in Agile to track progress and forecast completion. They provide transparency into whether a team is on track to meet their goals and help identify issues early.

---

## Burndown Charts

### What is a Burndown Chart?

A **burndown chart** shows the amount of work remaining over time. The chart typically shows story points or tasks on the Y-axis and time (days or sprints) on the X-axis.

**Purpose:**
- Track progress toward Sprint Goal
- Identify if team is on track
- Spot problems early
- Facilitate daily discussions
- Provide transparency to stakeholders

### Sprint Burndown Chart

**Anatomy of a Sprint Burndown:**

```
Remaining Work (Story Points)
    ↑
 30 │●
    │ ╲
 25 │  ●━━━━━━━━━━━━━━━━━━━━━ Ideal Burndown
    │   ╲
 20 │    ●
    │     ╲
 15 │      ●___
    │          ●___        Actual Progress
 10 │              ●___
    │                  ●___
  5 │                      ●
    │                       ╲
  0 │                        ●
    └──┬───┬───┬───┬───┬───┬───┬───┬───┬──→
       1   2   3   4   5   6   7   8   9  10
                      Days

● = Actual remaining work
━ = Ideal burndown line
```

**Components:**

**Y-Axis**: Remaining work (story points or hours)
- Starts at total committed work
- Ends at zero

**X-Axis**: Time (days in sprint)
- Starts at Day 1
- Ends at last day of sprint

**Ideal Line**: Perfect burn rate
- Straight diagonal from start to end
- Represents even daily progress
- Benchmark for comparison

**Actual Line**: Real progress
- Updated daily (usually after Daily Scrum)
- Shows actual remaining work
- May vary from ideal

### Perfect Burndown Chart

```
Story Points Remaining
    ↑
 25 │●
    │ ╲
 20 │  ●
    │   ╲
 15 │    ●
    │     ╲
 10 │      ●
    │       ╲
  5 │        ●
    │         ╲
  0 │          ●
    └──┬───┬───┬───┬───┬──→
       Day 1  3   5   7   9  10

Perfect scenario:
✓ Steady progress each day
✓ Meets Sprint Goal
✓ Work evenly distributed
```

### Common Burndown Patterns

**Pattern 1: Ahead of Schedule**

```
Points
    ↑
 25 │●━━━━━━━━━━━━━━━━━━ Ideal
    │ ╲●
 20 │   ╲●
    │     ╲●
 15 │       ╲●
    │         ╲
 10 │           ●
    │            ╲
  5 │             ●
    │              ╲
  0 │               ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Analysis:
✓ Team burning down faster than expected
✓ May finish early
✓ Could pull in additional work
⚠ Check: Are we cutting corners?
```

**Pattern 2: Behind Schedule**

```
Points
    ↑
 25 │●━━━━━━━━━━━━━━━━━━ Ideal
    │ ●╲
 20 │  ●╲
    │   ●╲
 15 │    ●╲
    │      ╲
 10 │      ●╲
    │        ╲
  5 │        ●╲
    │          ╲
  0 │           ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Analysis:
⚠ Team burning down slower than expected
⚠ May not complete all work
⚠ Need to take action

Actions:
- Identify blockers
- Adjust sprint scope
- Request help
- Communicate risks
```

**Pattern 3: Flat Line (No Progress)**

```
Points
    ↑
 25 │●━●━●━━━━━━━━━━━━━━ Ideal
    │  ╲
 20 │   ╲
    │    ╲
 15 │     ╲
    │      ╲
 10 │       ╲
    │        ╲
  5 │         ╲
    │          ╲
  0 │           ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Analysis:
🚨 No work completed for several days
🚨 Major blocker or issue

Possible causes:
- Blocked by external dependency
- Technical issues
- Unclear requirements
- Wrong estimates

Actions:
- Emergency team discussion
- Escalate blockers
- Re-plan sprint
```

**Pattern 4: Late Start**

```
Points
    ↑
 25 │●━●━●━●━━━━━━━━━━━━ Ideal
    │  ╲    ●
 20 │   ╲    ╲●
    │    ╲     ╲●
 15 │     ╲      ╲●
    │      ╲       ╲
 10 │       ╲        ●
    │        ╲        ╲
  5 │         ╲        ●
    │          ╲        ╲
  0 │           ●        ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Analysis:
⚠ Team didn't start work immediately
⚠ Sprint planning issues
⚠ Setup/onboarding delays

Actions:
- Improve sprint planning
- Better story refinement
- Address setup issues
```

**Pattern 5: Scope Addition (Going Up)**

```
Points
    ↑
 30 │        ●━━━━━━━━━━━━
    │       /
 25 │●━━━━━●━━━━━━━━━━━━━ Ideal (original)
    │ ╲   /
 20 │  ● ●
    │   ╲/ ╲
 15 │    ●   ●
    │     ╲   ╲
 10 │      ●   ●
    │       ╲   ╲
  5 │        ●   ●
    │         ╲   ╲
  0 │          ●   ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Analysis:
🚨 Work added mid-sprint (scope creep)
🚨 Violates sprint commitment

Causes:
- Unclear requirements discovered
- Emergency bugs
- Stakeholder pressure

Actions:
- Protect sprint scope
- Push new work to backlog
- Only add if critical
- Communicate impact
```

**Pattern 6: Perfect Completion**

```
Points
    ↑
 25 │●━━━━━━━━━━━━━━━━━━ Ideal
    │ ╲
 20 │  ●
    │   ╲
 15 │    ●
    │     ╲
 10 │      ●
    │       ╲
  5 │        ●
    │         ╲
  0 │          ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Analysis:
✓ Excellent sprint execution
✓ Met commitments
✓ Sustainable pace
✓ Good planning
```

### Creating and Maintaining Burndown Charts

**Daily Update Process:**

```
During or After Daily Scrum:
1. For each task/story:
   - If completed today → Reduce remaining work
   - If still in progress → Keep remaining work same
   - If blocked → Keep remaining work same

2. Update chart:
   - Add new data point
   - Connect to previous day

3. Review with team:
   - Are we on track?
   - Any concerns?
   - Need to adjust?

4. Take action if needed:
   - Remove blockers
   - Re-prioritize
   - Communicate risks
```

**Example Sprint Tracking:**

```
Day 0 (Sprint Planning):
Total commitment: 30 story points

Day 1 (End of day):
Completed: User login (5 pts)
Remaining: 25 points
Plot point at (1, 25)

Day 2:
Completed: Logout button (2 pts)
Remaining: 23 points
Plot point at (2, 23)

Day 3:
Completed: Nothing (working on complex feature)
Remaining: 23 points
Plot point at (3, 23)

Day 4:
Completed: Profile page (8 pts)
Remaining: 15 points
Plot point at (4, 15)

Continue daily...
```

---

## Burnup Charts

### What is a Burnup Chart?

A **burnup chart** shows the amount of work completed over time, along with the total scope. Unlike burndown charts that go down, burnup charts go up.

**Purpose:**
- Show completed work (positive visualization)
- Track scope changes explicitly
- Show progress toward total scope
- Better for long-term planning
- Useful for releases

### Anatomy of a Burnup Chart

```
Story Points
    ↑
200 │                        ━━━━━━━━━━ Total Scope
    │                   ┌────
180 │              ┌────┘
    │         ┌────┘
160 │    ┌────┘
    │┌───┘
140 │┘
    │         ╱────────────── Work Completed
120 │      ╱──
    │   ╱──
100 │ ╱─
    │╱
 80 │
    │
    └──┬───┬───┬───┬───┬───┬───┬───┬──→
       Sprint 1  2   3   4   5   6   7

━━━ = Total Scope (can change)
─── = Completed Work (only goes up)
```

**Components:**

**Total Scope Line (Top):**
- Shows total work in project/release
- Can increase (scope added)
- Can decrease (scope removed)
- Horizontal when scope stable

**Completed Work Line (Bottom):**
- Shows cumulative completed work
- Always goes up (or stays flat)
- Never goes down
- Velocity visible by slope

**Gap Between Lines:**
- Remaining work
- Closes as project nears completion
- Widens if scope increases faster than delivery

### Burndown vs Burnup Comparison

**Same Sprint - Burndown View:**
```
Points Remaining
    ↑
 30 │●━━━━━━━━━━━━━━━━━━ Ideal
    │ ╲
 25 │  ●
    │   ╲  ●
 20 │    ●/ ╲
    │     ╲  ●
 15 │      ╲  ╲
    │       ●  ●
 10 │        ╲  ╲
    │         ●  ●
  5 │          ╲  ╲
    │           ●  ●
  0 │            ╲  ●
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Spike up = scope added (confusing)
```

**Same Sprint - Burnup View:**
```
Points Completed
    ↑
 35 │        ━━━━━━━━━━━━━━ Total Scope (increased)
    │       /
 30 │━━━━━━━━━━━━━━━━━━━━ Original Scope
    │     /
 25 │    /
    │   /   ╱────────────── Work Completed
 20 │  /  ╱
    │ / ╱
 15 │ /╱
    │╱
 10 │
    │
  5 │
    │
  0 │
    └──┬───┬───┬───┬───┬──→
       1   2   3   4   5   6

Scope change clearly visible
```

**Advantages of Burnup:**
- ✓ Shows positive progress (going up)
- ✓ Scope changes explicit
- ✓ Completed work always visible
- ✓ Can't "hide" scope creep
- ✓ Better for stakeholder communication

**Advantages of Burndown:**
- ✓ Shows remaining work directly
- ✓ Simpler for teams to understand
- ✓ Traditional Scrum approach
- ✓ Clear view of "are we done yet?"

### Release Burnup Chart

Burnup charts excel at tracking releases over multiple sprints:

```
Story Points
    ↑
250 │                              ━━━━━ Target
    │                         ┌────
225 │                    ┌────┘
    │               ┌────┘
200 │          ┌────┘                    Scope increased
    │     ┌────┘
175 │┌────┘
    │              ╱─────────────────── Completed
150 │           ╱──
    │        ╱──
125 │     ╱──
    │  ╱──
100 │╱──
    │
 75 │
    │
 50 │
    │
 25 │
    │
  0 │
    └──┬───┬───┬───┬───┬───┬───┬───┬───┬──→
       Sprint 1  2   3   4   5   6   7   8

Projection: Lines will meet around Sprint 9-10
```

**Burnup Chart Patterns:**

**Pattern 1: On Track**
```
Points
    ↑
200 │━━━━━━━━━━━━━━━━━━━━ Scope (stable)
    │           ╱─────────
180 │        ╱──
    │     ╱──
160 │  ╱──                  Completed (steady)
    │╱
    └──┬───┬───┬───┬───┬──→

✓ Steady velocity
✓ Stable scope
✓ Will complete on time
```

**Pattern 2: Scope Creep**
```
Points
    ↑
250 │                    ━━━━━ Scope (increasing)
    │               ┌────
225 │          ┌────┘
    │     ┌────┘
200 │━━━━━┘
    │        ╱───────────── Completed
175 │     ╱──
    │  ╱──
150 │╱
    └──┬───┬───┬───┬───┬──→

⚠ Scope increasing faster than delivery
⚠ Release date will slip
Actions: Freeze scope or extend timeline
```

**Pattern 3: Scope Reduction**
```
Points
    ↑
200 │━━━━━┐
    │     └────────────── Scope (reduced)
175 │          ━━━━━━━━━
    │      ╱──────────────── Completed
150 │   ╱──
    │╱──
125 │
    └──┬───┬───┬───┬───┬──→

✓ Removed low-priority items
✓ Will complete earlier
✓ Focus on core features
```

**Pattern 4: Slowing Velocity**
```
Points
    ↑
200 │━━━━━━━━━━━━━━━━━━━━ Scope
    │        ╱────
175 │     ╱──    ╲
    │  ╱──        ╲ Velocity declining
150 │╱──           ╲
    │
125 │
    └──┬───┬───┬───┬───┬──→

⚠ Velocity decreasing over time
⚠ Technical debt?
⚠ Quality issues?
Actions: Retrospective, address root cause
```

---

## Reading and Interpreting Charts

### Key Questions to Ask

**Looking at Burndown Chart:**

1. **Are we on track?**
   - Is actual line tracking ideal line?
   - Will we complete by end of sprint?

2. **What's the trend?**
   - Getting closer or further from ideal?
   - Accelerating or slowing?

3. **Any anomalies?**
   - Flat lines (no progress)
   - Spikes up (scope added)
   - Sudden drops (bulk completion)

4. **Action needed?**
   - Remove blockers?
   - Adjust scope?
   - Ask for help?

**Looking at Burnup Chart:**

1. **Scope stability?**
   - Is top line stable?
   - Scope increasing or decreasing?

2. **Progress rate?**
   - Is completed line slope steady?
   - Accelerating or slowing?

3. **Gap closing?**
   - Are lines converging?
   - At current rate, when will they meet?

4. **Forecast accuracy?**
   - Original forecast still valid?
   - Need to adjust expectations?

### Daily Review Process

**Daily Scrum Chart Review:**

```
1. Look at yesterday's point
   "We completed 3 points yesterday"

2. Look at today's point
   "We have 12 points remaining"

3. Compare to ideal line
   "We're 2 points behind ideal"

4. Discuss as team
   "We need to focus on completing the payment
    integration today to stay on track"

5. Identify blockers
   "Waiting on API credentials is blocking us"

6. Adjust if needed
   "Let's pair up on the complex feature to
    move it faster"
```

---

## Identifying Issues Early

### Warning Signs

**Sprint Level:**

```
Warning Sign: Flat burndown for 2+ days
┌────────────────────────────────┐
│ Day 1: 30 points               │
│ Day 2: 30 points  ← No change │
│ Day 3: 30 points  ← No change │
└────────────────────────────────┘

Actions:
- Identify blocker immediately
- Escalate if needed
- Re-plan sprint if necessary
- Don't wait until end
```

```
Warning Sign: Scope increase mid-sprint
┌────────────────────────────────┐
│ Day 1: 30 points               │
│ Day 2: 27 points               │
│ Day 3: 32 points  ← Went up!  │
└────────────────────────────────┘

Actions:
- Understand why scope increased
- Push back if not critical
- Protect sprint commitment
- Communicate impact to PO
```

```
Warning Sign: Consistent underburndown
┌────────────────────────────────┐
│ Ideal burn: 3 points/day       │
│ Actual burn: 1.5 points/day    │
│ Gap widening each day           │
└────────────────────────────────┘

Actions:
- Reassess estimates
- Identify hidden complexity
- Adjust future planning
- May need to descope sprint
```

**Release Level:**

```
Warning Sign: Scope creep pattern
┌────────────────────────────────┐
│ Sprint 1: +10 points           │
│ Sprint 2: +15 points           │
│ Sprint 3: +20 points           │
└────────────────────────────────┘

Actions:
- Scope freeze
- Prioritization discussion
- Push features to later release
- Reset expectations
```

```
Warning Sign: Declining velocity
┌────────────────────────────────┐
│ Sprint 1: 30 points            │
│ Sprint 2: 27 points            │
│ Sprint 3: 23 points            │
│ Sprint 4: 20 points            │
└────────────────────────────────┘

Actions:
- Retrospective on causes
- Address technical debt
- Check team morale
- Review quality practices
```

### Predictive Analysis

**Forecasting Completion:**

```
Current Sprint Burndown:

Day 5 of 10:
Remaining: 18 points
Completed: 12 points (from 30)
Burn rate: 12 ÷ 5 = 2.4 points/day
Days left: 5

Forecast:
2.4 points/day × 5 days = 12 points completion
Expected remaining: 18 - 12 = 6 points

Conclusion: Will miss Sprint Goal by ~6 points
Action: Discuss with team and PO today, not day 10!
```

**Release Projection:**

```
Current Release Burnup:

After Sprint 3:
Total scope: 200 points
Completed: 75 points
Remaining: 125 points
Average velocity: 25 points/sprint

Forecast:
125 ÷ 25 = 5 more sprints
Current sprint: 3
Total: 3 + 5 = 8 sprints
Original plan: 6 sprints

Conclusion: 2 sprints behind schedule
Actions:
- Communicate to stakeholders
- Options: reduce scope or extend timeline
- Make decision now, not sprint 6
```

---

## Tools and Automation

### Manual Charts

**Pros:**
- Simple and accessible
- Team updates together
- Physical presence in workspace
- Low overhead

**Cons:**
- Manual updates required
- Can be forgotten
- Limited historical data
- Not accessible remotely

**Example Manual Process:**
```
Materials:
- Whiteboard or poster
- Markers
- Grid paper

Process:
1. Draw axes during sprint planning
2. Mark ideal line
3. Update daily after standup
4. Team member volunteers each day
5. Takes 2-3 minutes
```

### Digital Tools

Most Agile tools auto-generate charts:

**Jira:**
```
- Automatic burndown/burnup charts
- Real-time updates
- Historical data
- Customizable
- Reports for stakeholders
```

**Azure DevOps:**
```
- Built-in sprint burndown
- Velocity charts
- Cumulative flow diagrams
- Release burnup
```

**Trello (with extensions):**
```
- Burndown for Trello add-on
- Charts by story points or cards
- Sprint tracking
```

---

## Best Practices

**Do's:**
- ✓ Update daily, consistently
- ✓ Review in Daily Scrum
- ✓ Act on trends early
- ✓ Use as communication tool
- ✓ Keep visible to team
- ✓ Track both sprint and release
- ✓ Show to stakeholders
- ✓ Celebrate when on track

**Don'ts:**
- ✗ Don't update only when behind
- ✗ Don't manipulate to look good
- ✗ Don't ignore warning signs
- ✗ Don't wait until end to review
- ✗ Don't use for performance evaluation
- ✗ Don't hide from team
- ✗ Don't panic over single-day variations
- ✗ Don't treat as report to management

---

## Summary

| Concept | Key Points |
|---------|------------|
| Burndown Chart | Shows remaining work over time, decreasing line |
| Burnup Chart | Shows completed work and total scope, increasing lines |
| Daily Updates | Essential for accuracy and early issue detection |
| Pattern Recognition | Identify issues early through chart patterns |
| Sprint vs Release | Different time scales, both valuable |
| Transparency | Make progress visible to all stakeholders |

## Key Takeaways

- Burndown charts show remaining work, ideal for sprints
- Burnup charts show completed work and scope changes, ideal for releases
- Update charts daily for accuracy and early warning
- Flat lines indicate blockers—address immediately
- Scope changes visible in burnup charts
- Use charts to drive daily discussions and decisions
- Early detection of issues allows early correction
- Charts are communication tools, not performance metrics
- Combine with velocity for accurate forecasting
- Both manual and digital approaches work—consistency matters most

## Next Topic

Continue to [Project Boards](./09-project-boards.md) to learn about Kanban boards, sprint boards, and visual workflow management.
