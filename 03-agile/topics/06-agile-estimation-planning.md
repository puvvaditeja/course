# Agile Estimation and Planning

## Overview

Agile estimation and planning differs fundamentally from traditional project management. Instead of creating detailed upfront plans based on guesses, Agile teams use empirical data, relative estimation, and adaptive planning to forecast and deliver value.

---

## Why Estimation is Different in Agile

### Traditional Estimation Problems

**Traditional Approach:**
```
Manager: "How long will this feature take?"
Developer: "Um... 3 weeks?"
Manager: "Great! I'll tell the client 2 weeks."
Developer: *spends 6 weeks building it*
```

**Problems:**
- Based on guesses, not data
- Pressure to underestimate
- Doesn't account for uncertainty
- Ignores complexity and learning
- Individual estimates, not team-based
- False precision (exact hours/dates)

### Agile Approach

**Key Differences:**
- **Relative sizing** instead of absolute time
- **Team-based** instead of individual estimates
- **Story points** instead of hours
- **Velocity** instead of planned hours
- **Empirical** instead of theoretical
- **Collaborative** instead of top-down

---

## Story Points vs Hours

### The Problem with Hour-Based Estimates

**Why Hours Are Problematic:**

1. **Every developer is different**
   - Senior dev: 2 hours
   - Junior dev: 8 hours
   - Same feature, wildly different estimates

2. **Productivity varies by day**
   - Monday morning: focused and productive
   - Friday afternoon: distracted and tired
   - Post-meeting days: fragmented time

3. **Non-development time ignored**
   - Meetings
   - Email
   - Code reviews
   - Helping teammates
   - Actual coding time: 4-6 hours of an 8-hour day

4. **False precision**
   - "This will take 17.5 hours"
   - Implies accuracy that doesn't exist
   - Software development is inherently uncertain

5. **Parkinson's Law**
   - Work expands to fill available time
   - Given 8 hours, task takes 8 hours
   - Given 4 hours, same task might take 4 hours

### Story Points: A Better Way

**What are Story Points?**

Story points are a unit of measure for expressing the overall effort required to implement a user story or product backlog item.

**Story Points Consider:**
- **Complexity**: How complicated is it?
- **Effort**: How much work is involved?
- **Uncertainty**: How much do we know?
- **Risk**: What could go wrong?

**Story Points Do NOT Represent:**
- Calendar time
- Hours of work
- Individual productivity
- Specific tasks

### The Fibonacci Sequence

Most teams use Fibonacci numbers for story points:

```
1, 2, 3, 5, 8, 13, 21, 34, 55, 89...
```

**Why Fibonacci?**

1. **Reflects uncertainty**: Gaps get bigger as estimates get larger
2. **Prevents false precision**: No arguing between 14 vs 15 points
3. **Forces meaningful differences**: Big enough gaps to be meaningful
4. **Natural human sizing**: Easier to distinguish 5 from 8 than 7 from 8

**Common Scale:**
```
1 point:   Trivial, very simple
2 points:  Simple, straightforward
3 points:  Moderate complexity
5 points:  Average story, some complexity
8 points:  Complex, significant work
13 points: Very complex, should consider splitting
21 points: Too large, must split
```

### Reference Story Method

**How It Works:**

1. **Pick a reference story** everyone understands
2. **Assign it 5 points** (medium complexity)
3. **Compare all other stories** to this reference

**Example:**

Reference Story (5 points):
```
As a user, I want to reset my password via email,
So that I can regain access if I forget it.
```

Comparing Other Stories:
```
Story: Add logout button
Comparison: Much simpler than password reset
Points: 1

Story: Implement 2FA authentication
Comparison: More complex than password reset
Points: 13

Story: Add profile picture upload
Comparison: Slightly simpler than password reset
Points: 3
```

### Story Points Example Scale

**1 Point: Trivial**
```
- Update button text
- Fix typo in documentation
- Change color of element
- Add simple validation message
```

**2 Points: Simple**
```
- Add new form field with validation
- Create simple API endpoint
- Add basic unit test
- Simple CSS layout change
```

**3 Points: Moderate**
```
- Create CRUD endpoint with validation
- Implement simple search functionality
- Add email notification for action
- Integrate third-party library
```

**5 Points: Average**
```
- User registration with email verification
- Complex form with multiple validations
- File upload with size/type restrictions
- Basic reporting page
```

**8 Points: Complex**
```
- Payment integration
- Multi-step wizard process
- Complex data migration
- Real-time chat feature
```

**13 Points: Very Complex**
```
- Advanced search with multiple filters
- Complex analytics dashboard
- Third-party API integration (unknown API)
- Video streaming functionality
```

**21+ Points: Too Large**
```
These should be split into smaller stories!
```

---

## Relative Estimation

### The T-Shirt Analogy

You can quickly determine if a shirt is Small, Medium, or Large without measuring it. Relative estimation works the same way.

**T-Shirt Sizing:**
```
XS  - Tiny changes
S   - Simple features
M   - Average features
L   - Complex features
XL  - Very complex features
XXL - Epics (need to break down)
```

Later, convert to points:
```
XS = 1
S  = 2
M  = 5
L  = 8
XL = 13
```

### Why Relative Estimation Works

**The Comparison Principle:**

Humans are better at comparing things than measuring them absolutely.

**Example:**
```
Question 1: How tall is that building?
Person A: "Um... 150 feet?"
Person B: "Maybe 200 feet?"
Person C: "I'd say 180 feet?"
(High variability, lots of disagreement)

Question 2: Is that building taller or shorter than this one?
Everyone: "Taller!"
(Quick consensus, accurate comparison)
```

**In Agile Estimation:**
```
Question: How many hours will this take?
Team: "8 hours? 16 hours? 24 hours?"
(Lots of disagreement)

Question: Is this bigger or smaller than our reference story?
Team: "Definitely bigger, but not twice as big, so 8 points"
(Faster consensus)
```

### Relative Estimation Process

**Step 1: Select Reference Stories**
```
Small (2 points):   [Well-known simple story]
Medium (5 points):  [Well-known average story]
Large (13 points):  [Well-known complex story]
```

**Step 2: Compare New Stories**
```
New Story X:
- Simpler than Medium reference
- More complex than Small reference
→ Estimate: 3 points

New Story Y:
- More complex than Large reference
- Needs to be split!
```

**Step 3: Use Historical Data**
```
After several sprints, patterns emerge:
- 2-point stories usually take 1 day
- 5-point stories usually take 2-3 days
- But we still estimate in points, not days!
```

---

## Planning Poker

Planning Poker is a consensus-based technique for estimating effort or relative size of development goals.

### How Planning Poker Works

**Setup:**
- Each team member has cards: 0, 1, 2, 3, 5, 8, 13, 21, ?, ☕
- Product Owner presents a user story
- Team can ask clarifying questions

**The Process:**

```
┌─────────────────────────────────────────────────────────┐
│                    Planning Poker Flow                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. Product Owner reads story                           │
│     ↓                                                   │
│  2. Team asks clarifying questions                      │
│     ↓                                                   │
│  3. Brief discussion of approach                        │
│     ↓                                                   │
│  4. Each member privately selects card                  │
│     ↓                                                   │
│  5. Everyone reveals simultaneously                     │
│     ↓                                                   │
│  6a. If consensus → Record estimate                     │
│     OR                                                  │
│  6b. If different → Discuss and re-estimate             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Planning Poker Cards

**Number Cards (Fibonacci):**
```
1, 2, 3, 5, 8, 13, 21
```

**Special Cards:**

**0 (Zero):**
- Already done
- Trivial (minutes of work)

**? (Question Mark):**
- Don't understand the story
- Need more information
- Can't estimate

**☕ (Coffee Cup):**
- Need a break
- Too tired to estimate
- Let's pause

**∞ (Infinity):**
- Way too large
- Must split the story

### Example Planning Poker Session

**Story:**
```
As a user,
I want to upload a profile picture,
So that others can recognize me.
```

**Round 1:**
```
Product Owner: "Any questions?"

Developer A: "What file types?"
PO: "JPG, PNG"

Developer B: "Size limits?"
PO: "5MB max"

Developer C: "Do we need cropping?"
PO: "Not for MVP, just upload"

PO: "Ready to estimate?"
```

**Round 2: First Estimate**
```
Everyone selects a card...

[3] [8] [5] [5] [8] [3]

(Cards revealed simultaneously)
```

**Round 3: Discussion**
```
Scrum Master: "We have 3s, 5s, and 8s. Let's hear from the extremes."

Developer A (said 3):
"I thought it was simple - just accept file, validate size,
store it. I've done this before, took a few hours."

Developer B (said 8):
"But we need to handle multiple formats, validate properly,
resize for different display sizes, store securely in S3,
update the database, handle errors. I think it's more complex."

Developer C (said 5):
"I agree with both points. It's not trivial, but we have
libraries for resize and S3. I think 5 is right."

Team discusses for 2-3 minutes...
```

**Round 4: Re-estimate**
```
Everyone selects again...

[5] [5] [5] [5] [5] [8]

(Close enough for consensus)
```

**Round 5: Final Decision**
```
Scrum Master: "We have five 5s and one 8. Can we agree on 5?"

Developer B (who said 8): "Yes, fair enough. I was maybe
overthinking it. 5 works."

Result: Story estimated at 5 points
```

### Benefits of Planning Poker

**1. Team Engagement**
- Everyone participates
- No silent members
- Active collaboration

**2. Diverse Perspectives**
- Different viewpoints surface
- Hidden complexity discovered
- Knowledge sharing

**3. Avoiding Anchoring**
- Simultaneous reveal prevents influence
- Junior devs not intimidated by senior estimates
- Each person thinks independently

**4. Better Estimates**
- Wisdom of crowds
- Outliers spark important discussions
- Shared understanding

**5. Team Building**
- Collaborative activity
- Builds shared context
- Improves communication

### Planning Poker Best Practices

**Do's:**
- ✓ Keep discussions time-boxed (2-3 minutes)
- ✓ Focus on relative sizing, not absolute time
- ✓ Use reference stories
- ✓ Let the Product Owner answer questions
- ✓ Re-estimate if needed (but not more than 2-3 times)
- ✓ Document assumptions
- ✓ Keep it light and fun

**Don'ts:**
- ✗ Don't average the estimates mathematically
- ✗ Don't pressure team to reach consensus
- ✗ Don't let one person dominate
- ✗ Don't estimate for too long
- ✗ Don't get into implementation details
- ✗ Don't estimate in hours/days

### When Estimates Differ Widely

**Scenario:**
```
Estimates: [2] [3] [13] [5] [8] [13]
```

**What This Means:**
- Different understanding of requirements
- Hidden complexity or risks
- Different technical approaches
- Missing information

**What to Do:**
1. **Ask questions of extremes**
   - Why did you say 2?
   - Why did you say 13?

2. **Surface assumptions**
   - "I assumed we had API X available"
   - "I thought we needed to build from scratch"

3. **Clarify requirements**
   - Ask Product Owner for clarification
   - Review acceptance criteria
   - Identify unknowns

4. **Consider splitting**
   - Maybe story is too large
   - Perhaps it has multiple parts
   - Could simplify for MVP

5. **Re-estimate**
   - With new information
   - Usually converges

---

## T-Shirt Sizing

An alternative or complementary approach to numerical story points.

### T-Shirt Size Scale

```
┌────────┬──────────┬─────────────────────────────┐
│  Size  │  Points  │         Description         │
├────────┼──────────┼─────────────────────────────┤
│   XS   │    1     │  Trivial, minutes           │
│   S    │    2     │  Simple, hours              │
│   M    │    5     │  Average, 1-2 days          │
│   L    │    8     │  Complex, 2-3 days          │
│   XL   │   13     │  Very complex, 3-4 days     │
│  XXL   │   21+    │  Too large, needs splitting │
└────────┴──────────┴─────────────────────────────┘
```

### When to Use T-Shirt Sizing

**Initial Backlog Grooming:**
```
Product Backlog (100+ items):
First pass: Quickly size as XS, S, M, L, XL, XXL
Then: Convert to points for sprint planning
```

**High-Level Planning:**
```
Epic Sizing:
- Feature A: L
- Feature B: XL
- Feature C: M
Rough quarterly planning
```

**Stakeholder Communication:**
```
Executive: "How big is that feature?"
Team: "It's a Large, maybe 2-3 weeks"
(Easier than explaining story points)
```

---

## Velocity Tracking

### What is Velocity?

**Velocity** is the amount of work a team completes in a sprint, measured in story points.

**Example:**
```
Sprint 1: Completed 23 points
Sprint 2: Completed 27 points
Sprint 3: Completed 25 points
Sprint 4: Completed 26 points

Average Velocity: 25 points per sprint
```

### Calculating Velocity

**Only count completed stories** that meet Definition of Done:

```
Sprint Plan:
├─ Story A (5 points) → Done ✓      = 5 points
├─ Story B (8 points) → Done ✓      = 8 points
├─ Story C (3 points) → Done ✓      = 3 points
├─ Story D (5 points) → In Progress = 0 points
└─ Story E (8 points) → Not Started = 0 points

Velocity = 16 points (only completed stories)
```

**Do NOT:**
- ✗ Count partially completed stories
- ✗ Give partial credit
- ✗ Count in-progress work
- ✗ Adjust estimates after the fact

### Using Velocity for Planning

**Sprint Planning:**
```
Average velocity: 25 points
Team member on vacation: Reduce by 20%
Adjusted capacity: 20 points

Select stories totaling ~20 points for sprint
```

**Release Planning:**
```
Remaining work: 150 points
Average velocity: 25 points per sprint
Estimated sprints: 150 ÷ 25 = 6 sprints

Release date: 6 sprints × 2 weeks = 12 weeks from now
```

**Example Release Plan:**
```
Current Date: January 1
Velocity: 25 points/sprint
Sprint Length: 2 weeks

Backlog:
├─ Epic A: 75 points → Sprints 1-3 (6 weeks)
├─ Epic B: 50 points → Sprints 4-5 (4 weeks)
└─ Epic C: 25 points → Sprint 6 (2 weeks)

Projected Release: March 26 (12 weeks)
```

### Velocity Trends

**Ideal Velocity Chart:**
```
Points
  ↑
 30│              ┌───┬───┬───┐
   │          ┌───┤   │   │   │
 25│      ┌───┤   │   │   │   │
   │  ┌───┤   │   │   │   │   │
 20│──┤   │   │   │   │   │   │
   │  │   │   │   │   │   │   │
 15│  │   │   │   │   │   │   │
   └──┴───┴───┴───┴───┴───┴───┴──→
      1   2   3   4   5   6   7  Sprint

Starts lower (team forming)
Increases (team norming)
Stabilizes (team performing)
```

**Warning Signs:**

**Declining Velocity:**
```
Points
  ↑
 30│  ┌───┐
   │  │   │
 25│  │   ├───┐
   │  │   │   ├───┐
 20│  │   │   │   ├───┐
   │  │   │   │   │   ├───┐
 15│  │   │   │   │   │   │
   └──┴───┴───┴───┴───┴───┴──→
      1   2   3   4   5   6  Sprint

Possible causes:
- Technical debt accumulating
- Team members leaving
- Increasing complexity
- External interruptions
```

**Erratic Velocity:**
```
Points
  ↑
 30│      ┌───┐       ┌───┐
   │      │   │       │   │
 25│      │   │       │   │
   │  ┌───┤   │   ┌───┤   │
 20│  │   │   │   │   │   │
   │  │   │   ├───┤   │   │
 15│  │   │   │   │   │   │
   └──┴───┴───┴───┴───┴───┴──→
      1   2   3   4   5   6  Sprint

Possible causes:
- Inconsistent sprint planning
- External dependencies
- Unclear requirements
- Team size changes
```

### Velocity Best Practices

**Do's:**
- ✓ Track velocity for planning, not performance evaluation
- ✓ Use 3-sprint rolling average for forecasting
- ✓ Account for team changes (vacation, new members)
- ✓ Expect velocity to stabilize after 3-5 sprints
- ✓ Use velocity ranges, not exact numbers
- ✓ Compare team to itself, not other teams

**Don'ts:**
- ✗ Don't compare velocities between teams
- ✗ Don't pressure team to increase velocity
- ✗ Don't use velocity for performance reviews
- ✗ Don't artificially inflate estimates to boost velocity
- ✗ Don't panic over single-sprint variations
- ✗ Don't ignore sustained trends

---

## Release Planning

### Release Planning Process

**1. Define Release Goal**
```
Goal: Launch MVP e-commerce platform
Features: Product catalog, shopping cart, checkout, user accounts
Timeline: 3 months (6 two-week sprints)
```

**2. Estimate All Features**
```
Epic: Product Catalog
├─ Browse products (8)
├─ Search products (5)
├─ Filter by category (3)
├─ Product details (5)
└─ Product images (3)
Total: 24 points

Epic: Shopping Cart
├─ Add to cart (5)
├─ View cart (3)
├─ Update quantities (2)
├─ Remove items (2)
└─ Cart persistence (5)
Total: 17 points

Epic: Checkout
├─ Shipping info (5)
├─ Payment integration (13)
├─ Order confirmation (3)
└─ Email receipt (3)
Total: 24 points

Epic: User Accounts
├─ Registration (8)
├─ Login/Logout (5)
├─ Password reset (5)
├─ Profile management (5)
└─ Order history (8)
Total: 31 points

Grand Total: 96 points
```

**3. Calculate Required Sprints**
```
Total work: 96 points
Team velocity: 20 points/sprint
Required sprints: 96 ÷ 20 = 4.8 sprints

Round up: 5 sprints (10 weeks)
Add buffer (20%): 6 sprints (12 weeks)
```

**4. Create Release Plan**
```
Sprint 1-2: User Accounts (31 points)
  - Registration, login, password reset
  - Foundation for other features

Sprint 3: Product Catalog (24 points)
  - Browse, search, filter products
  - Product details and images

Sprint 4: Shopping Cart (17 points)
  - Add to cart, view, update
  - Cart persistence

Sprint 5-6: Checkout (24 points)
  - Shipping info
  - Payment integration
  - Order confirmation

Reserve Sprint: Buffer for unknowns
```

### Adaptive Release Planning

**Reality: Plans Change**

```
Original Plan → Sprint 1 → Sprint 2 → Sprint 3 → ...

After Sprint 1:
- Velocity was 18, not 20
- New critical feature discovered
- Payment integration more complex than thought

Adapted Plan → Sprint 2 → Sprint 3 → Sprint 4 → ...
(Adjusted based on reality)
```

**Continuous Replanning:**
```
Every Sprint:
├─ Review actual velocity
├─ Reassess remaining work
├─ Adjust projections
├─ Communicate changes
└─ Reprioritize if needed
```

---

## Commitment vs Forecast

### Traditional: Commitment

```
Manager: "Can you commit to delivering this by March 1?"
Team: "Um... sure?"
(Team pressured into unrealistic commitment)
```

**Problems:**
- Pressure to commit
- Based on limited information
- Punished for missing commitment
- Discourages honesty

### Agile: Forecast

```
Product Owner: "What can we deliver in 6 sprints?"
Team: "Based on our velocity of 25 points, we forecast
      150 points of work. That includes features A, B, and C."
(Empirical prediction based on data)
```

**Benefits:**
- Based on historical data
- Acknowledges uncertainty
- Can be adjusted
- Encourages transparency

### Cone of Uncertainty

```
Uncertainty
    ↑
    │ ╲
    │  ╲
High│   ╲
    │    ╲
    │     ╲___
    │         ╲___
Low │             ╲___________
    └───────────────────────────→
    Start  25%  50%  75%  Complete
              Project Progress

Early: High uncertainty, wide range
Later: Low uncertainty, narrow range
```

**Estimates Improve Over Time:**
```
Day 1:     "Between 3 and 9 months"
Month 1:   "Between 4 and 7 months"
Month 3:   "Between 5 and 6 months"
Month 5:   "Will complete in 6 months"
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Story Points | Relative measure of effort, complexity, and uncertainty |
| Planning Poker | Consensus-based estimation technique using cards |
| T-Shirt Sizing | Quick, high-level sizing using XS/S/M/L/XL |
| Velocity | Amount of work team completes per sprint |
| Release Planning | Use velocity to forecast delivery dates |
| Forecast vs Commitment | Empirical predictions, not pressured commitments |

## Key Takeaways

- Story points are relative, not absolute measures
- Use Fibonacci sequence to reflect increasing uncertainty
- Planning Poker creates team consensus and shared understanding
- Velocity is for planning, not performance evaluation
- Track velocity over multiple sprints for accurate forecasting
- Release plans should adapt based on reality
- Forecast based on empirical data, don't commit blindly
- Different teams have different velocities—never compare
- Estimation is a team activity, not individual
- Focus on delivering value, not maximizing points

## Next Topic

Continue to [Sprint Points, Planning Poker, Velocity](./07-sprint-points-poker-velocity.md) for deeper dive into these estimation techniques with practical examples.
