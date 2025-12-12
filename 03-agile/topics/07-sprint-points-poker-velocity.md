# Sprint Points, Planning Poker, and Velocity

## Overview

This topic provides a deeper dive into the practical application of story points, Planning Poker, and velocity tracking. We'll explore real-world scenarios, common challenges, and advanced techniques for effective Agile estimation.

---

## Understanding Story Points in Depth

### Story Points as a Multi-Dimensional Measure

Story points consider multiple factors simultaneously:

```
                Story Points
                     ║
        ┌────────────╬────────────┐
        │            ║            │
   COMPLEXITY    EFFORT      UNCERTAINTY
        │            ║            │
        ▼            ▼            ▼
   How hard?    How much?    How risky?
```

### Breaking Down Each Dimension

**1. Complexity**

How difficult is this to implement?

```
LOW COMPLEXITY (1-2 points):
- Standard CRUD operation
- Using familiar technology
- Clear, simple logic
- Well-understood domain

Example:
"Add a logout button to navigation"
- Standard HTML/CSS
- Call existing logout API
- Redirect to login page

MEDIUM COMPLEXITY (3-5 points):
- Some algorithmic thinking
- Multiple components involved
- Integration with existing systems
- Moderate business logic

Example:
"Implement password strength validation"
- Multiple validation rules
- Real-time feedback
- Visual strength indicator
- Integration with registration form

HIGH COMPLEXITY (8-13 points):
- Complex algorithms
- Multiple system integration
- Intricate business rules
- Performance considerations

Example:
"Implement real-time collaborative editing"
- Websocket connections
- Conflict resolution
- Multi-user synchronization
- Complex state management
```

**2. Effort**

How much work is involved?

```
LOW EFFORT (1-2 points):
- Minimal code changes
- Single file modification
- Little testing needed
- Quick to implement

Example:
"Update error message text"
- Change string constant
- Verify display
- Done in minutes

MEDIUM EFFORT (3-5 points):
- Multiple files
- Moderate testing
- Some documentation
- Database changes

Example:
"Add email field to user profile"
- Update database schema
- Modify API endpoints
- Update frontend form
- Write tests
- Update documentation

HIGH EFFORT (8-13 points):
- Many files affected
- Extensive testing
- Multiple components
- Integration work

Example:
"Implement comprehensive audit logging"
- Database design
- Middleware for all requests
- UI for viewing logs
- Search and filter functionality
- Export capabilities
- Performance optimization
```

**3. Uncertainty**

How much do we know?

```
LOW UNCERTAINTY (1-2 points):
- Done similar work before
- Well-understood requirements
- Familiar technology
- No dependencies

Example:
"Add sort functionality to existing table"
- Library already in use
- Pattern established
- Clear requirements

MEDIUM UNCERTAINTY (3-5 points):
- Some unknowns
- New library to learn
- Dependencies on other work
- Requirements mostly clear

Example:
"Integrate Google Maps for location picker"
- Never used Maps API before
- Need to research best approach
- Pricing/limits to understand
- But well-documented API

HIGH UNCERTAINTY (8-13 points):
- Many unknowns
- New technology
- Unclear requirements
- External dependencies
- Proof of concept needed

Example:
"Implement AI-powered product recommendations"
- New to machine learning
- Algorithm selection unclear
- Data quality unknown
- Performance unpredictable
```

### Story Point Calibration Examples

Let's establish a baseline with real examples:

**1 Point Stories:**
```
✓ Change button label text
✓ Update CSS color scheme
✓ Fix typo in documentation
✓ Add tooltip to existing field
✓ Update API error message
✓ Add simple validation rule
```

**2 Point Stories:**
```
✓ Add new form field with validation
✓ Create simple GET API endpoint
✓ Add sorting to existing table
✓ Implement basic search filter
✓ Add confirmation dialog
✓ Create simple unit test
```

**3 Point Stories:**
```
✓ Create CRUD API for simple entity
✓ Add pagination to list view
✓ Implement basic file upload
✓ Create multi-field search
✓ Add export to CSV functionality
✓ Implement email notification
```

**5 Point Stories:**
```
✓ User registration with email verification
✓ Password reset flow (email + reset page)
✓ Complex form with multiple validations
✓ Basic reporting page with charts
✓ Third-party service integration (known API)
✓ Multi-step wizard (3-4 steps)
```

**8 Point Stories:**
```
✓ Payment gateway integration
✓ Real-time notification system
✓ Advanced search with multiple filters
✓ Data migration from legacy system
✓ Complex dashboard with multiple widgets
✓ Video/image upload with processing
```

**13 Point Stories:**
```
✓ Complete authentication system (OAuth, 2FA, SSO)
✓ Complex reporting system with custom queries
✓ Integration with unknown/poorly documented API
✓ Performance optimization (entire application)
✓ Real-time chat system
✓ Advanced analytics dashboard

Note: These should often be split into smaller stories
```

---

## Planning Poker: Advanced Techniques

### Running Effective Planning Poker Sessions

**Session Structure:**

```
┌──────────────────────────────────────────────┐
│         Planning Poker Session               │
│              (2-3 hours)                      │
├──────────────────────────────────────────────┤
│                                              │
│  1. Introduction (5 min)                     │
│     - Review reference stories               │
│     - Clarify estimation scale               │
│     - Set time limits                        │
│                                              │
│  2. Story Estimation Loop (1.5-2 hours)      │
│     For each story (5-10 min max):           │
│       a. PO reads story (1 min)              │
│       b. Q&A (2-3 min)                       │
│       c. Estimate (30 sec)                   │
│       d. Discussion if needed (2-3 min)      │
│       e. Re-estimate if needed (30 sec)      │
│       f. Record estimate                     │
│                                              │
│  3. Wrap-up (5 min)                          │
│     - Review estimates                       │
│     - Identify items needing refinement      │
│                                              │
└──────────────────────────────────────────────┘
```

### Detailed Planning Poker Example

**Story to Estimate:**
```
As a customer,
I want to save items to a wishlist,
So that I can purchase them later.

Acceptance Criteria:
☐ "Add to Wishlist" button on product pages
☐ View all wishlist items on dedicated page
☐ Remove items from wishlist
☐ Move items from wishlist to cart
☐ Wishlist persists across sessions
☐ Wishlist syncs across devices (if logged in)
```

**Round 1: Initial Questions**

```
Developer A: "Does this need to work for guest users too,
             or only logged-in users?"
PO: "Only logged-in users for MVP. Guest users see
     'Log in to save' message."

Developer B: "How many items can be in a wishlist?"
PO: "Let's say 100 items maximum."

Developer C: "Do we need to show product availability
             or price changes in wishlist?"
PO: "Good question! Let's show current price but not
     track changes for MVP. We can add that later."

Scrum Master: "Any other questions? ... Okay, everyone
              select your cards."
```

**Round 2: First Estimate**

```
Revealed: [3] [5] [8] [5] [5] [8]

Scrum Master: "We have 3, 5s, and 8s. Let's hear from
              the 3 and one of the 8s."

Developer A (3 points):
"I think this is pretty straightforward:
 - Database table for wishlist items
 - Few API endpoints (add, remove, list)
 - UI component to display list
 - Button on product page
We've built similar features before. Three points."

Developer B (8 points):
"I think there's more to it:
 - We need user authentication check
 - Handle edge cases (product deleted, out of stock)
 - Mobile responsive design
 - Cross-device sync mentioned in AC
 - What if user adds same item twice?
 - Performance with large wishlists
I think it's closer to 8."

Developer C (5 points):
"Both make good points. The core is simple, but
there are details. I think 5 is right—it's
average complexity for us."

Developer D (5 points):
"I agree with C. The sync is just using existing
user ID in database. Not trivial but not complex."

Developer E (5 points):
"Yeah, 5 for me too."

Developer A (originally 3):
"Okay, I see now—I wasn't thinking about all the
edge cases and mobile. I could go to 5."
```

**Round 3: Re-estimate**

```
Revealed: [5] [5] [5] [5] [5] [5]

Scrum Master: "Perfect consensus! We'll record this as
              5 points. Let's move to the next story."
```

### Handling Difficult Estimating Scenarios

**Scenario 1: Wide Divergence**

```
Revealed: [2] [5] [13] [8] [5] [13]

This indicates:
- Different interpretations of story
- Hidden assumptions
- Missing information
- Possibly too large/complex

Actions:
1. Ask both extremes to explain
2. PO clarifies requirements
3. Consider splitting story
4. May need spike (research story)
5. Table for more refinement
```

**Scenario 2: Everyone Picks "?"**

```
Revealed: [?] [?] [?] [?] [?]

This indicates:
- Story too vague
- Missing critical information
- New technology/domain
- Dependencies unclear

Actions:
1. PO provides more detail
2. Team asks specific questions
3. Create spike story to investigate
4. Defer estimation until more information
5. Schedule refinement session
```

**Scenario 3: Pattern of All High Estimates**

```
Story 1: [13] [13] [13] [13]
Story 2: [13] [8] [13] [13]
Story 3: [8] [13] [13] [8]

This indicates:
- Stories too large
- Epic needs breaking down
- Scope unclear
- Wrong level for sprint planning

Actions:
1. Work with PO to split stories
2. Use story splitting techniques
3. Create smaller, more manageable stories
4. Re-estimate the smaller pieces
```

### Planning Poker Tips and Tricks

**For Facilitators:**

```
✓ Keep energy high
  - Stand up
  - Keep pace moving
  - Use timer for discussions

✓ Encourage participation
  - Ask quiet members first
  - Value all perspectives
  - No wrong answers

✓ Prevent analysis paralysis
  - Time-box discussions
  - "Good enough" is fine
  - Can re-estimate later if needed

✓ Make it fun
  - Celebrate consensus
  - Use humor
  - Keep it lighthearted
```

**For Teams:**

```
✓ Think independently
  - Don't peek at others' cards
  - Trust your judgment
  - Reveal simultaneously

✓ Speak up
  - Share your reasoning
  - Ask questions
  - Challenge assumptions

✓ Listen actively
  - Consider other viewpoints
  - Learn from teammates
  - Be open to changing estimate

✓ Stay focused
  - On relative sizing
  - On the story at hand
  - On sprint-sized work
```

---

## Velocity: Deep Dive

### Calculating and Tracking Velocity

**Sprint 1 Example:**

```
Sprint Backlog:
┌────────────────────────┬────────┬──────────┐
│        Story           │ Points │  Status  │
├────────────────────────┼────────┼──────────┤
│ User login             │   5    │   Done   │
│ Password reset         │   5    │   Done   │
│ Profile editing        │   3    │   Done   │
│ Email notifications    │   5    │   80%    │
│ Dashboard widgets      │   8    │   Not    │
└────────────────────────┴────────┴──────────┘

Velocity = 5 + 5 + 3 = 13 points
(Only count completed stories)
```

**Important: No Partial Credit**

```
❌ Wrong:
Email notifications (5 points, 80% done) = 4 points
Velocity = 13 + 4 = 17 points

✅ Correct:
Email notifications incomplete = 0 points
Velocity = 13 points

Why?
- Story either meets DoD or doesn't
- No partially working software
- Prevents false sense of progress
- Encourages completing work
```

### Velocity Over Time

**New Team Velocity Pattern:**

```
Sprint  Velocity  Pattern
  1       12      (Learning, setting up, low)
  2       18      (Getting comfortable)
  3       22      (Improving)
  4       25      (Stabilizing)
  5       24      (Stable)
  6       26      (Stable)
  7       25      (Stable)
  8       24      (Stable)

Average (last 3): 25 points
Use for planning: 25 points per sprint
```

**Mature Team Velocity Pattern:**

```
Sprint  Velocity  Notes
  10      28
  11      26
  12      27      Stable, predictable
  13      25
  14      27
  15      26

Average: 26.5 points
Range: 25-28 points (normal variation)
```

### Velocity Chart Patterns

**Healthy Pattern:**
```
Velocity (Story Points)
    ↑
 30 │                 ┌───┬───┬───┐
    │             ┌───┤   │   │   │
 25 │         ┌───┤   │   │   │   │
    │     ┌───┤   │   │   │   │   │
 20 │ ┌───┤   │   │   │   │   │   │
    │ │   │   │   │   │   │   │   │
 15 │ │   │   │   │   │   │   │   │
    └─┴───┴───┴───┴───┴───┴───┴───┴──→
      1   2   3   4   5   6   7   8
                 Sprint

✓ Increases initially (team learning)
✓ Stabilizes (team performing)
✓ Small variations normal
✓ Sustainable pace
```

**Warning: Declining Velocity**
```
Velocity
    ↑
 30 │ ┌───┐
    │ │   ├───┐
 25 │ │   │   ├───┐
    │ │   │   │   ├───┐
 20 │ │   │   │   │   ├───┐
    │ │   │   │   │   │   ├───┐
 15 │ │   │   │   │   │   │   │
    └─┴───┴───┴───┴───┴───┴───┴──→
      1   2   3   4   5   6   7

⚠ Possible issues:
- Technical debt accumulating
- Team burnout
- Increasing complexity
- Quality problems
- External distractions

Actions:
- Hold retrospective
- Address root causes
- Reduce WIP
- Focus on quality
- Protect team time
```

**Warning: Artificially Increasing Velocity**
```
Velocity
    ↑
 40 │                     ┌───┐
    │                 ┌───┤   │
 35 │             ┌───┤   │   │
    │         ┌───┤   │   │   │
 30 │     ┌───┤   │   │   │   │
    │ ┌───┤   │   │   │   │   │
 25 │ │   │   │   │   │   │   │
    └─┴───┴───┴───┴───┴───┴───┴──→
      1   2   3   4   5   6   7

⚠ Red flags:
- Pressure to increase velocity
- Inflating estimates
- Cutting corners on quality
- Skipping testing
- Unsustainable pace

Actions:
- Review DoD adherence
- Check quality metrics
- Retrospective on process
- Leadership education
- Protect team
```

### Using Velocity for Forecasting

**Simple Forecast:**

```
Scenario:
- Average velocity: 25 points
- Remaining backlog: 200 points
- Sprint length: 2 weeks

Calculation:
Sprints needed: 200 ÷ 25 = 8 sprints
Time estimate: 8 × 2 weeks = 16 weeks

Forecast: Approximately 4 months
```

**Forecast with Ranges:**

```
Scenario:
- Recent velocities: 22, 24, 26, 25, 23
- Average: 24 points
- Range: 22-26 points
- Remaining: 150 points

Best case (26 points/sprint):
150 ÷ 26 = 5.8 → 6 sprints (12 weeks)

Likely case (24 points/sprint):
150 ÷ 24 = 6.25 → 7 sprints (14 weeks)

Worst case (22 points/sprint):
150 ÷ 22 = 6.8 → 7 sprints (14 weeks)

Forecast: 12-14 weeks (with buffer: 16 weeks)
```

**Forecast with Changing Scope:**

```
Current Date: Week 1
Backlog: 200 points
Velocity: 25 points/sprint

Week 1 Forecast:
200 ÷ 25 = 8 sprints (16 weeks)
Target: Week 17

After Sprint 1 (Week 3):
Completed: 24 points
Remaining: 176 points
New items added: 30 points
Total remaining: 206 points
206 ÷ 24 = 8.6 sprints
Target: Week 20 (3 weeks later)

After Sprint 2 (Week 5):
Completed: 26 points
Remaining: 180 points
Items removed: 20 points
Total remaining: 160 points
160 ÷ 25 = 6.4 sprints
Target: Week 18 (2 weeks earlier)

Key: Continuously update forecast
```

### Velocity and Team Changes

**Adding Team Member:**

```
Before:
Team: 5 developers
Velocity: 30 points/sprint

Adding 1 developer:

Week 1-2:
- New member ramping up
- Team training new member
- Velocity temporarily drops
Expected: 25 points

Week 3-4:
- New member partially productive
- Team adjusting
Expected: 28 points

Week 5+:
- New member fully productive
- Team normalized
Expected: 35 points (not 36)

Note: 6 people ≠ 1.2× productivity
      Communication overhead increases
```

**Team Member Vacation:**

```
Regular velocity: 30 points
Team size: 5 members
Per person: 6 points average

One member on vacation (2-week sprint):
Adjusted capacity: 30 - 6 = 24 points
Plan sprint: ~24 points

One member half-time:
Adjusted capacity: 30 - 3 = 27 points
Plan sprint: ~27 points
```

### Velocity Anti-Patterns

**Anti-Pattern 1: Velocity as Performance Metric**

```
❌ Wrong:
Manager: "Team A has velocity 30, Team B has 25.
         Team B needs to improve!"

Problems:
- Different story point scales
- Different team compositions
- Different technology stacks
- Different domain complexity
- Creates wrong incentives

✅ Right:
Each team compares to own history
Track trends, not absolute numbers
Focus on delivering value
```

**Anti-Pattern 2: Pressure to Increase Velocity**

```
❌ Wrong:
Manager: "Our velocity is 25. Next quarter, I want 30!"

Results:
- Inflated estimates (2-point becomes 3-point)
- Cut corners on quality
- Skip testing
- Technical debt
- Burnout
- Actual delivery slows down

✅ Right:
Velocity is for planning, not goals
Focus on sustainable pace
Quality over quantity
Remove impediments naturally improve velocity
```

**Anti-Pattern 3: Counting Partial Points**

```
❌ Wrong:
Story: 10 points, 70% complete
Count: 7 points toward velocity

Problems:
- Not potentially shippable
- Doesn't meet DoD
- False progress
- Hides problems

✅ Right:
Story incomplete = 0 points
Carry to next sprint
Understand why incomplete
Adjust future planning
```

**Anti-Pattern 4: Comparing Team Velocities**

```
❌ Wrong:
Team A: 40 points/sprint
Team B: 25 points/sprint
Conclusion: Team A is better

Reality:
Team A's "1 point" = Team B's "0.5 points"
They might deliver same actual value!

✅ Right:
Velocity is relative to each team
Use for own planning only
Never compare across teams
```

---

## Practical Exercises

### Exercise 1: Calibration Session

Estimate these stories using reference story method:

**Reference Story (5 points):**
```
As a user, I want to reset my password via email,
So that I can regain access if I forget it.

Includes:
- "Forgot password" link
- Email input and validation
- Send reset email with token
- Reset password page
- Update password in database
```

**Stories to Estimate:**

**Story A:**
```
As a user, I want to log out of my account,
So that my session ends securely.

Includes:
- Logout button
- Clear session
- Redirect to login page
```

**Story B:**
```
As a user, I want to enable two-factor authentication,
So that my account is more secure.

Includes:
- 2FA setup page
- QR code generation
- Code verification
- Backup codes
- Option to disable 2FA
```

**Story C:**
```
As a user, I want to update my email address,
So that I can use my current email.

Includes:
- Edit email field
- Validation
- Confirmation email to new address
- Update database
```

**Try estimating each before seeing answers:**

<details>
<summary>Suggested Estimates</summary>

```
Story A: 1 point
- Much simpler than reference
- Straightforward implementation
- Minimal testing

Story B: 13 points
- More complex than reference
- Multiple components
- Security critical
- Need thorough testing
- Consider splitting

Story C: 3 points
- Less complex than reference
- Similar pattern
- Fewer steps
```
</details>

### Exercise 2: Planning Poker Practice

Practice a Planning Poker session with your team for these stories:

```
Story 1:
As an admin, I want to export user data to CSV,
So that I can analyze it in Excel.

Story 2:
As a customer, I want to filter products by multiple attributes,
So that I can find exactly what I need.

Story 3:
As a developer, I want API documentation auto-generated,
So that it stays in sync with code.
```

**Reflection Questions:**
- Did estimates vary widely initially?
- What questions were most helpful?
- How did discussion improve estimates?
- Were there any surprises?

### Exercise 3: Velocity Calculation

Calculate velocity for this sprint:

```
Sprint Plan:
┌────────────────────────┬────────┬──────────────┐
│        Story           │ Points │   Status     │
├────────────────────────┼────────┼──────────────┤
│ User registration      │   8    │   Done       │
│ Email verification     │   5    │   Done       │
│ Login page             │   3    │   Done       │
│ Profile page           │   5    │   In Review  │
│ Settings page          │   8    │   50% Done   │
│ Dashboard              │   13   │   Not Started│
└────────────────────────┴────────┴──────────────┘
```

**Questions:**
1. What is the velocity for this sprint?
2. What should be done with incomplete stories?
3. What might this pattern indicate?

<details>
<summary>Answers</summary>

```
1. Velocity = 16 points (8 + 5 + 3)
   Only count fully done stories

2. Incomplete stories:
   - Profile page: If passes code review, can count
   - Settings page: Carry to next sprint
   - Dashboard: Carry to next sprint or return to backlog

3. Patterns indicated:
   - Team may have overcommitted
   - Stories may be too large (13 points)
   - Need better sprint planning
   - Discuss in retrospective
```
</details>

---

## Summary

| Concept | Key Points |
|---------|------------|
| Story Points | Multi-dimensional: complexity + effort + uncertainty |
| Fibonacci Scale | 1, 2, 3, 5, 8, 13, 21—reflects increasing uncertainty |
| Planning Poker | Consensus-based, surfaces assumptions, team activity |
| Velocity | Completed points per sprint, for planning not performance |
| Forecasting | Use historical velocity to predict future delivery |
| Team Changes | Adjust velocity expectations when team composition changes |

## Key Takeaways

- Story points consider complexity, effort, and uncertainty together
- Use reference stories to calibrate the team's point scale
- Planning Poker discussions are more valuable than the estimates themselves
- Velocity stabilizes after 3-5 sprints for a consistent team
- Never compare velocities between different teams
- Use velocity ranges, not single numbers, for forecasting
- Only count fully completed stories toward velocity
- Velocity is a planning tool, not a performance metric
- Team changes temporarily affect velocity—adjust expectations
- Focus on sustainable pace and delivering value

## Next Topic

Continue to [Burndown and Burnup Charts](./08-burndown-burnup-charts.md) to learn how to visualize and track sprint and release progress.
