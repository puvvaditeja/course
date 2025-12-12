# Scrum Framework

## What is Scrum?

Scrum is the most widely used Agile framework for managing complex projects. Created by Jeff Sutherland and Ken Schwaber in the early 1990s, Scrum provides a lightweight structure that helps teams work together to develop, deliver, and sustain complex products.

### Key Characteristics

- **Lightweight**: Simple to understand, difficult to master
- **Framework, not methodology**: Provides structure, but teams adapt practices
- **Empirical**: Based on experience and observation, not theory
- **Time-boxed**: All activities have maximum time limits
- **Iterative and Incremental**: Work in short cycles, building incrementally
- **Self-organizing teams**: Teams decide how to accomplish work

### The Name "Scrum"

The term comes from rugby, where a scrum is a formation where the team works together to move the ball forward. Similarly, in Scrum software development, the entire team works collaboratively toward a common goal.

---

## Scrum Overview and Benefits

### The Scrum Framework at a Glance

```
┌─────────────────────────────────────────────────────────────────┐
│                         SCRUM FRAMEWORK                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ROLES:                                                         │
│    • Product Owner                                              │
│    • Scrum Master                                               │
│    • Development Team                                           │
│                                                                 │
│  ARTIFACTS:                                                     │
│    • Product Backlog                                            │
│    • Sprint Backlog                                             │
│    • Increment                                                  │
│                                                                 │
│  EVENTS:                                                        │
│    • Sprint (container for all events)                          │
│    • Sprint Planning                                            │
│    • Daily Scrum                                                │
│    • Sprint Review                                              │
│    • Sprint Retrospective                                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Benefits of Using Scrum

**1. Faster Time to Market**
- Deliver working software every sprint (1-4 weeks)
- Release minimum viable features quickly
- Rapid iteration based on feedback
- Continuous value delivery

**2. Higher Product Quality**
- Definition of Done ensures standards
- Continuous testing and integration
- Regular inspections catch defects early
- Focus on potentially shippable increments

**3. Improved Team Productivity**
- Self-organizing teams more motivated
- Clear goals and priorities
- Elimination of unnecessary work
- Focus on high-value features
- Sustainable pace prevents burnout

**4. Enhanced Stakeholder Satisfaction**
- Regular demonstrations of progress
- Continuous collaboration and feedback
- Transparency into development process
- Ability to influence product direction

**5. Better Risk Management**
- Short iterations limit exposure
- Regular inspection points
- Early identification of issues
- Ability to pivot quickly

**6. Increased Flexibility**
- Adapt to changing requirements
- Reprioritize backlog as needed
- Respond to market changes
- Embrace new opportunities

**7. Greater Predictability**
- Velocity tracking improves forecasting
- Regular rhythm and cadence
- Empirical data for planning
- Transparent progress tracking

**8. Continuous Improvement**
- Regular retrospectives
- Inspect and adapt culture
- Team empowerment
- Learning organization

### When Scrum Works Best

**Ideal Conditions:**
- Complex, innovative work
- Evolving requirements
- Cross-functional team available
- Product Owner can be engaged
- Organization supports Agile values
- Customer available for collaboration

**Less Ideal Scenarios:**
- Simple, repetitive tasks
- Fully defined requirements with no flexibility
- Distributed teams with poor communication
- Lack of organizational support
- Highly regulated environments (may need modifications)

---

## Scrum Theory

Scrum is founded on empirical process control theory, or empiricism. Empiricism asserts that knowledge comes from experience and making decisions based on what is known.

### The Three Pillars of Empiricism

**1. Transparency**

**Definition**: Significant aspects of the process must be visible to those responsible for the outcome.

**In Practice:**
- Common language for process
- Shared Definition of Done
- Visible artifacts (boards, backlogs)
- Open communication
- Transparent progress tracking
- Honest reporting of status

**Example:**
```
Task Board (Visible to All):

┌──────────┬──────────────┬────────────┬────────┐
│  To Do   │ In Progress  │   Review   │  Done  │
├──────────┼──────────────┼────────────┼────────┤
│ Story A  │  Story B     │  Story D   │Story F │
│ Story C  │  Story E     │            │Story G │
│ Story H  │              │            │        │
└──────────┴──────────────┴────────────┴────────┘

Everyone can see:
- What work exists
- What's being worked on
- What's completed
- Who's working on what
```

**2. Inspection**

**Definition**: Scrum users must frequently inspect Scrum artifacts and progress toward goals to detect undesirable variances.

**In Practice:**
- Daily Scrum inspects progress toward Sprint Goal
- Sprint Review inspects the Increment
- Sprint Retrospective inspects the process
- Backlog refinement inspects upcoming work
- Code reviews inspect quality
- Automated tests inspect functionality

**Inspection Frequency:**
```
Daily:        Daily Scrum (15 minutes)
              ↓
              Inspect yesterday's progress
              Inspect today's plan
              Inspect blockers

Every Sprint: Sprint Review (inspect product)
              Sprint Retrospective (inspect process)

Ongoing:      Continuous integration (inspect code)
              Automated testing (inspect quality)
              Monitoring (inspect production)
```

**Important**: Inspection should not be so frequent that it impedes the work. Skilled inspectors should perform inspections.

**3. Adaptation**

**Definition**: If an inspector determines that aspects of a process deviate outside acceptable limits, the process or material being processed must be adjusted.

**In Practice:**
- Adjust Sprint plan during Daily Scrum
- Adjust Product Backlog during Sprint Review
- Adjust process during Sprint Retrospective
- Replan if Sprint Goal becomes obsolete
- Reprioritize based on feedback

**Adaptation Cycle:**
```
┌──────────────┐
│   INSPECT    │ → Identify variance
└──────┬───────┘
       ↓
┌──────┴───────┐
│   ANALYZE    │ → Understand root cause
└──────┬───────┘
       ↓
┌──────┴───────┐
│     PLAN     │ → Decide adjustment
└──────┬───────┘
       ↓
┌──────┴───────┐
│   EXECUTE    │ → Implement change
└──────┬───────┘
       ↓
    (Inspect again)
```

### The Relationship Between the Three Pillars

```
        ┌─────────────────┐
        │  TRANSPARENCY   │
        │                 │
        │  Makes work     │
        │  visible        │
        └────────┬────────┘
                 ↓
        ┌────────┴────────┐
        │   INSPECTION    │
        │                 │
        │  Identifies     │
        │  variances      │
        └────────┬────────┘
                 ↓
        ┌────────┴────────┐
        │   ADAPTATION    │
        │                 │
        │  Adjusts to     │
        │  improve        │
        └─────────────────┘
                 ↓
          (Back to Transparency)
```

**Without transparency**, inspection is difficult or misleading.
**Without inspection**, no opportunity to identify need for adaptation.
**Without adaptation**, inspection is pointless.

---

## Empirical Process Control

### Empirical vs Defined Process Control

**Defined Process Control:**
- Every piece of work fully understood
- Process repeatable with predictable results
- Detailed upfront planning
- Best for manufacturing, simple processes
- Example: Assembly line production

**Empirical Process Control:**
- Complexity makes prediction difficult
- Learn as you go
- Frequent inspection and adaptation
- Best for complex, creative work
- Example: Software development

### Why Empiricism for Software Development?

**Software Development is Complex:**
- Requirements often unclear initially
- Technology constantly evolving
- Creative problem-solving required
- Many interdependencies
- Unpredictable challenges

**Traditional Planning Fails Because:**
```
Month 0: Create detailed 12-month plan
         ↓
Month 3: Market changes, plan outdated
         ↓
Month 6: Technology evolves, plan irrelevant
         ↓
Month 9: Requirements misunderstood, plan wrong
         ↓
Month 12: Deliver product nobody wants
```

**Empirical Approach Succeeds:**
```
Sprint 1: Build based on current knowledge
         ↓ (Inspect)
Sprint 2: Adapt based on Sprint 1 learnings
         ↓ (Inspect)
Sprint 3: Adapt based on Sprint 2 learnings
         ↓ (Inspect)
...continuing to adapt and improve
         ↓
Result: Product that meets actual needs
```

### Applying Empiricism in Scrum

**Product Development:**
- Build small increments
- Demonstrate to stakeholders
- Gather feedback
- Adjust direction
- Repeat

**Process Improvement:**
- Try a practice
- Measure results
- Discuss in retrospective
- Adjust or continue
- Experiment continuously

**Estimation and Planning:**
- Track actual velocity
- Use historical data
- Adjust forecasts
- Plan based on reality, not hope

### Empirical Decision Making

**Data-Driven Decisions:**

Instead of:
- "I think this will take 2 weeks"
- "This feature is probably important"
- "We should be able to finish everything"

Use:
- "Based on our velocity of 30 points/sprint, this will take 2 sprints"
- "Customer feedback shows 80% request this feature"
- "Our burndown shows we're on track for 75% completion"

**Example: Sprint Planning**
```
❌ Bad (Opinion-Based):
"I think we can finish all 10 stories this sprint"

✅ Good (Empirical):
"Our average velocity is 25 points. These 6 stories
total 24 points, so we commit to these."
```

---

## Scrum Values

Scrum is built upon five core values that guide the team's behavior and decision-making:

### The Five Scrum Values

**1. Commitment**
- Commit to team goals
- Commit to quality
- Commit to Sprint Goal
- Personal commitment to doing your best

**Example**: Team commits to completing selected backlog items and achieving Sprint Goal, not just "trying."

**2. Courage**
- Courage to tackle tough problems
- Courage to admit mistakes
- Courage to ask for help
- Courage to challenge status quo
- Courage to speak truth

**Example**: Developer has courage to say "This approach won't work" rather than proceeding with a flawed solution.

**3. Focus**
- Focus on Sprint Goal
- Focus on high-value work
- Minimize distractions
- One thing at a time

**Example**: Team protects sprint from scope changes to maintain focus on Sprint Goal.

**4. Openness**
- Open about challenges
- Open to new ideas
- Open communication
- Transparent about progress

**Example**: Team openly discusses velocity drop in retrospective rather than hiding problems.

**5. Respect**
- Respect team members
- Respect different perspectives
- Respect expertise
- Respect roles

**Example**: Developers respect Product Owner's prioritization decisions, even when they disagree.

### How Values Support Scrum

```
            ┌──────────────┐
            │  COMMITMENT  │
            └──────┬───────┘
                   ↓
         Team commits to goals
                   ↓
            ┌──────┴───────┐
            │   COURAGE    │
            └──────┬───────┘
                   ↓
         Address challenges
                   ↓
            ┌──────┴───────┐
            │    FOCUS     │
            └──────┬───────┘
                   ↓
         Work on priorities
                   ↓
            ┌──────┴───────┐
            │   OPENNESS   │
            └──────┬───────┘
                   ↓
         Share information
                   ↓
            ┌──────┴───────┐
            │   RESPECT    │
            └──────────────┘
                   ↓
         Trust each other
```

### Values in Action

**Daily Scrum Example:**
- **Commitment**: "I commit to completing my task today"
- **Courage**: "I'm blocked and need help"
- **Focus**: "I'll focus on the Sprint Goal priority"
- **Openness**: "I encountered an unexpected issue"
- **Respect**: "I'll help Jane with her blocker"

**Sprint Review Example:**
- **Commitment**: Deliver what was committed
- **Courage**: Demonstrate incomplete features and explain why
- **Focus**: Show progress toward Sprint Goal
- **Openness**: Honestly discuss what worked and didn't
- **Respect**: Value stakeholder feedback

---

## Summary

| Concept | Key Points |
|---------|------------|
| What is Scrum | Lightweight Agile framework for complex product development |
| Benefits | Faster delivery, higher quality, better productivity, increased flexibility |
| Three Pillars | Transparency, Inspection, Adaptation |
| Empiricism | Make decisions based on observation and experience |
| Five Values | Commitment, Courage, Focus, Openness, Respect |

## Key Takeaways

- Scrum is the most popular Agile framework worldwide
- Based on empirical process control (inspect and adapt)
- Three pillars: Transparency, Inspection, Adaptation
- Five values guide team behavior and decisions
- Simple framework but requires discipline to master
- Best suited for complex, innovative work
- Emphasizes self-organization and cross-functional teams
- Delivers value through regular, time-boxed iterations

## Next Topic

Continue to [Scrum Roles, Artifacts, Ceremonies](./04-scrum-roles-artifacts-ceremonies.md) to learn about the specific components that make Scrum work.
