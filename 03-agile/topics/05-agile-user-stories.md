# Agile User Stories

## What are User Stories?

User stories are short, simple descriptions of a feature told from the perspective of the person who desires the capability, usually a user or customer of the system. They are the primary way of expressing requirements in Agile development.

### Purpose of User Stories

**Traditional Requirements:**
```
The system shall provide a mechanism for users to authenticate
using username and password credentials with password complexity
validation including minimum 8 characters, uppercase, lowercase,
numbers, and special characters, with account lockout after 5
failed attempts...
(Detailed, technical, exhaustive documentation)
```

**User Story:**
```
As a user,
I want to log in with my email and password,
So that I can access my account securely.
```

### Why User Stories?

**Benefits:**
- **User-focused**: Emphasizes value to user, not technical implementation
- **Conversational**: Encourages discussion and collaboration
- **Lightweight**: Easy to write and change
- **Flexible**: Details emerge through conversation
- **Estimable**: Teams can estimate effort
- **Testable**: Acceptance criteria define success

**Shift in Mindset:**
- From "documenting everything" to "conversing continuously"
- From "what the system does" to "what the user needs"
- From "technical specifications" to "business value"
- From "contract" to "collaboration"

---

## User Story Format

### The Standard Template

The most common user story format follows this structure:

```
As a [type of user],
I want [an action or goal],
So that [a benefit or value].
```

### Breaking Down the Template

**As a [type of user]:**
- Who wants this feature?
- What is their role?
- Helps team empathize with user

Examples:
- As a customer
- As an administrator
- As a registered user
- As a premium subscriber
- As a guest visitor

**I want [an action or goal]:**
- What does the user want to do?
- What capability do they need?
- Focuses on user intent, not implementation

Examples:
- I want to reset my password
- I want to filter products by price
- I want to export my data
- I want to receive notifications

**So that [a benefit or value]:**
- Why does the user want this?
- What value does it provide?
- Connects feature to business goal

Examples:
- So that I can regain access to my account
- So that I can find items within my budget
- So that I can backup my information
- So that I stay informed of important updates

### Complete Examples

**E-commerce Application:**
```
As a shopper,
I want to add items to a shopping cart,
So that I can purchase multiple items in a single transaction.
```

**Social Media Platform:**
```
As a content creator,
I want to schedule posts in advance,
So that I can maintain consistent posting without being online.
```

**Project Management Tool:**
```
As a project manager,
I want to generate weekly progress reports,
So that I can keep stakeholders informed.
```

**Banking Application:**
```
As an account holder,
I want to set up automatic bill payments,
So that I never miss a payment deadline.
```

### Alternative Formats

**Feature-Driven Format:**
```
In order to [receive benefit],
As a [role],
I want [goal/desire].
```

Example:
```
In order to make better purchasing decisions,
As a customer,
I want to read product reviews from other buyers.
```

**Job Story Format** (focuses on context and motivation):
```
When [situation],
I want to [motivation],
So I can [expected outcome].
```

Example:
```
When I'm browsing products on mobile,
I want to quickly compare similar items,
So I can make a purchase decision without switching devices.
```

---

## Acceptance Criteria

Acceptance criteria define the boundaries of a user story and help the team understand what "done" means for that particular story.

### What are Acceptance Criteria?

**Definition**: Specific conditions that must be satisfied for the story to be considered complete and acceptable.

**Characteristics:**
- Clear and concise
- Testable
- Written from user perspective
- Defined before development starts
- Used to validate completion
- Shared understanding between Product Owner and team

### Format for Acceptance Criteria

**Scenario-Based (Given-When-Then):**
```
Given [context or precondition],
When [action or event],
Then [expected outcome].
```

**Example: Login Feature**
```
User Story:
As a registered user,
I want to log in to my account,
So that I can access personalized content.

Acceptance Criteria:

Scenario 1: Successful login
Given I am on the login page
When I enter valid credentials
Then I am redirected to my dashboard

Scenario 2: Invalid password
Given I am on the login page
When I enter an invalid password
Then I see an error message "Invalid credentials"
And I remain on the login page

Scenario 3: Account lockout
Given I have failed login 5 times
When I attempt to log in again
Then I see "Account locked. Reset password to continue"
And I cannot log in until password reset
```

**Checklist Format:**
```
User Story:
As a customer,
I want to filter products by price range,
So that I can find items within my budget.

Acceptance Criteria:
☐ Min and max price input fields are available
☐ Filter applies when "Apply" button is clicked
☐ Results update to show only items in range
☐ Price range is displayed above results
☐ "Clear filters" button resets to all products
☐ URL updates to reflect filter (shareable)
☐ Filter works with other filters (category, brand)
☐ Validation: min cannot exceed max
```

### Writing Good Acceptance Criteria

**Best Practices:**

**1. Be Specific**
```
❌ Bad: "The system should be fast"
✅ Good: "Search results appear within 2 seconds"

❌ Bad: "Users can reset password"
✅ Good: "User receives password reset email within 5 minutes"
```

**2. Focus on "What," Not "How"**
```
❌ Bad: "Use React hooks for state management"
✅ Good: "Form data persists when switching tabs"

❌ Bad: "Implement JWT authentication"
✅ Good: "User session remains active for 24 hours"
```

**3. Make It Testable**
```
❌ Bad: "Interface should be user-friendly"
✅ Good: "New users can complete signup in under 3 minutes"

❌ Bad: "System should handle errors well"
✅ Good: "All API errors display user-friendly messages"
```

**4. Include Edge Cases**
```
For a "Delete Account" feature:
✅ Account with active subscription
✅ Account with pending orders
✅ Account created within last 30 days
✅ Account with no activity
✅ Accidental deletion prevention
```

**5. Define All Acceptance Criteria Upfront**
- Prevents scope creep
- Ensures shared understanding
- Enables accurate estimation
- Provides clear "done" definition

---

## INVEST Criteria

The INVEST acronym describes qualities of good user stories. Each story should be:

### I - Independent

**Definition**: Stories should be self-contained and not dependent on other stories.

**Why It Matters:**
- Stories can be developed in any order
- Easier to prioritize
- Reduces coordination overhead
- Minimizes blocked work

**Example:**

❌ Dependent:
```
Story 1: Create user database table
Story 2: Create user registration API (depends on Story 1)
Story 3: Create registration form (depends on Story 2)
```

✅ Independent:
```
Story 1: Users can register for an account
Story 2: Users can log in to their account
Story 3: Users can reset their password
(Each can be developed separately)
```

**Techniques to Reduce Dependencies:**
- Combine dependent stories
- Rewrite stories to be self-contained
- Use technical spikes for foundational work

---

### N - Negotiable

**Definition**: Stories are not explicit contracts. Details should be negotiable through conversation.

**Why It Matters:**
- Encourages collaboration
- Allows for better solutions
- Adapts to new information
- Teams find best implementation

**Example:**

❌ Too Prescriptive:
```
As a user,
I want a dropdown menu in the top-right corner with my profile
picture (50x50px) showing options for Settings, Profile, and
Logout in that order using the company blue color (#0066CC),
So that I can access my account options.
```

✅ Negotiable:
```
As a user,
I want easy access to my account settings and logout,
So that I can manage my account quickly.

Acceptance Criteria:
- Access profile settings
- Access account settings
- Log out of account
- Visible from any page

(Implementation details decided with team)
```

---

### V - Valuable

**Definition**: Each story must deliver value to the customer or user.

**Why It Matters:**
- Ensures work is meaningful
- Helps with prioritization
- Prevents technical tasks from cluttering backlog
- Keeps focus on user needs

**Example:**

❌ Not Valuable (technical task):
```
As a developer,
I want to refactor the authentication module,
So that the code is cleaner.
```

✅ Valuable:
```
As a user,
I want to log in using my Google account,
So that I don't need to remember another password.

(Refactoring happens as part of implementing this value)
```

**Technical Stories:**
Sometimes technical work is necessary. Frame it in terms of value:

✅ Technical work framed with value:
```
As a user,
I want pages to load in under 2 seconds,
So that I can navigate the site efficiently.

(Enables performance optimization work)
```

---

### E - Estimable

**Definition**: Stories must be able to be estimated by the team.

**Why It Matters:**
- Enables sprint planning
- Required for velocity tracking
- Helps identify unclear requirements
- Facilitates prioritization

**Reasons Stories Might Not Be Estimable:**
1. Lack of domain knowledge
2. Lack of technical knowledge
3. Too much uncertainty
4. Story too large

**Solutions:**

**If team lacks knowledge:**
```
❌ Cannot Estimate:
"Integrate with Salesforce API"
(Team has never used Salesforce)

✅ Solution: Create a spike story
"Research Salesforce API integration approach" (time-boxed to 4 hours)
Then estimate actual implementation story
```

**If story is unclear:**
```
❌ Cannot Estimate:
"Improve the search feature"

✅ Solution: Add specificity
"Add autocomplete suggestions to search based on popular queries"
```

**If story is too large:**
```
❌ Cannot Estimate:
"Build complete reporting system"

✅ Solution: Break it down (see next section)
```

---

### S - Small

**Definition**: Stories should be small enough to complete within one sprint.

**Why It Matters:**
- Faster feedback
- Easier to estimate
- Reduced risk
- Better flow
- Sense of progress

**Guidelines for Story Size:**
- Should complete in a few days, not weeks
- Ideal: 1-3 days for one developer
- Maximum: Fits within one sprint
- Smaller is generally better

**Example:**

❌ Too Large:
```
As a customer,
I want a complete e-commerce shopping experience,
So that I can purchase products online.

(Could take months)
```

✅ Right Size:
```
Story 1: As a customer, I want to add items to a cart,
        So that I can purchase multiple items together.

Story 2: As a customer, I want to view my cart,
        So that I can review items before purchasing.

Story 3: As a customer, I want to update quantities in my cart,
        So that I can buy the right amount.

Story 4: As a customer, I want to remove items from my cart,
        So that I can change my mind about purchases.

(Each takes 1-2 days)
```

---

### T - Testable

**Definition**: Stories must have clear criteria that can be tested.

**Why It Matters:**
- Defines "done"
- Enables verification
- Prevents ambiguity
- Supports automated testing

**Example:**

❌ Not Testable:
```
As a user,
I want the application to be intuitive,
So that I can use it easily.
```

✅ Testable:
```
As a first-time user,
I want clear labels and tooltips,
So that I can complete my first task without external help.

Acceptance Criteria:
☐ All form fields have descriptive labels
☐ Complex features have tooltip help
☐ Error messages explain how to fix issues
☐ 80% of test users complete signup without assistance
```

**Making Stories Testable:**

Add specific, measurable criteria:
```
❌ "Fast loading"
✅ "Page loads in under 2 seconds on 4G connection"

❌ "Secure password"
✅ "Password requires 8+ characters, including number and symbol"

❌ "Good error handling"
✅ "All API errors display user-friendly message and log to monitoring"
```

---

## Story Splitting Techniques

When stories are too large, they need to be split into smaller, valuable pieces.

### Why Split Stories?

- Too large to complete in one sprint
- Hard to estimate accurately
- Risk of not delivering value
- Difficult to test
- Blocks other work

### Splitting Patterns

**1. By Workflow Steps**

❌ Large Story:
```
As a customer, I want to complete a purchase
```

✅ Split by Steps:
```
Story 1: Review cart before checkout
Story 2: Enter shipping information
Story 3: Enter payment information
Story 4: Receive order confirmation
```

---

**2. By CRUD Operations**

❌ Large Story:
```
As a user, I want to manage my contacts
```

✅ Split by Operation:
```
Story 1: Create new contact
Story 2: View contact list
Story 3: Edit contact details
Story 4: Delete contact
```

---

**3. By Business Rules**

❌ Large Story:
```
As a customer, I want pricing applied correctly
```

✅ Split by Rules:
```
Story 1: Apply standard pricing
Story 2: Apply member discounts
Story 3: Apply promotional codes
Story 4: Apply volume discounts
```

---

**4. By Simple/Complex**

❌ Large Story:
```
As a user, I want to search for products
```

✅ Split by Complexity:
```
Story 1: Search by product name (simple)
Story 2: Search with filters (medium)
Story 3: Advanced search with multiple criteria (complex)
Story 4: Save search preferences (complex)
```

---

**5. By User Role**

❌ Large Story:
```
As a user, I want to manage system settings
```

✅ Split by Role:
```
Story 1: Regular users can update profile settings
Story 2: Managers can update team settings
Story 3: Admins can update system-wide settings
```

---

**6. By Platform/Device**

❌ Large Story:
```
As a user, I want to access my account
```

✅ Split by Platform:
```
Story 1: Access account on desktop web
Story 2: Access account on mobile web
Story 3: Access account on iOS app
Story 4: Access account on Android app
```

---

**7. By Data Variations**

❌ Large Story:
```
As a user, I want to import data
```

✅ Split by Format:
```
Story 1: Import CSV files
Story 2: Import Excel files
Story 3: Import JSON files
```

---

**8. By Happy Path / Exceptions**

❌ Large Story:
```
As a user, I want to process payments
```

✅ Split by Scenarios:
```
Story 1: Process successful payment
Story 2: Handle declined payment
Story 3: Handle network errors
Story 4: Handle fraud detection
```

---

### Splitting Example: Complete Breakdown

**Original Epic:**
```
As a job seeker, I want to apply for jobs online
```

**First Level Split (by workflow):**
```
1. Search and find jobs
2. Create application
3. Submit application
4. Track application status
```

**Second Level Split (Story 1 - by complexity):**
```
1a. Search jobs by keyword
1b. Filter by location
1c. Filter by salary range
1d. Save search preferences
```

**Final User Stories:**
```
Story 1: As a job seeker, I want to search jobs by keyword,
         So that I can find relevant positions.

Story 2: As a job seeker, I want to filter jobs by location,
         So that I can find jobs near me.

Story 3: As a job seeker, I want to filter jobs by salary,
         So that I can find jobs matching my expectations.

(Each story small enough for one sprint)
```

---

## Common User Story Mistakes

### Mistake 1: Too Technical

❌ Wrong:
```
As a developer,
I want to implement Redux state management,
So that we can share state across components.
```

✅ Correct:
```
As a user,
I want my shopping cart to persist across page refreshes,
So that I don't lose my items if I navigate away.
```

---

### Mistake 2: Too Large

❌ Wrong:
```
As a user,
I want a complete social media platform,
So that I can connect with friends.
```

✅ Correct:
```
Story 1: As a user, I want to create a profile
Story 2: As a user, I want to post updates
Story 3: As a user, I want to follow other users
(Epic broken into smaller stories)
```

---

### Mistake 3: Missing the "So That"

❌ Wrong:
```
As a user,
I want to click a button to download reports.
```

✅ Correct:
```
As a user,
I want to download my transaction history,
So that I can review it offline and keep records.
```

---

### Mistake 4: Focusing on UI Instead of Goal

❌ Wrong:
```
As a user,
I want a blue button in the top-right corner,
So that I can save my work.
```

✅ Correct:
```
As a user,
I want to save my work in progress,
So that I can continue later without losing changes.
```

---

### Mistake 5: No Acceptance Criteria

❌ Wrong:
```
As a customer,
I want to search for products.
```

✅ Correct:
```
As a customer,
I want to search for products,
So that I can quickly find what I need.

Acceptance Criteria:
☐ Search box visible on all pages
☐ Results appear within 2 seconds
☐ Results show product name, image, and price
☐ No results shows helpful message
☐ Search handles typos (fuzzy matching)
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| User Stories | Short descriptions from user perspective, emphasizing value |
| Format | As a [user], I want [goal], So that [benefit] |
| Acceptance Criteria | Specific, testable conditions that define "done" |
| INVEST | Independent, Negotiable, Valuable, Estimable, Small, Testable |
| Splitting | Break large stories into smaller valuable increments |

## Key Takeaways

- User stories are conversations, not contracts
- Always write from user perspective, focus on value
- The "So that" clause is crucial—it explains the "why"
- Acceptance criteria make stories testable and clear
- Good stories follow INVEST principles
- Stories should be small enough to complete in days, not weeks
- Split large stories using various patterns (workflow, CRUD, complexity)
- Avoid technical jargon and implementation details in stories
- Include all acceptance criteria before starting development

## Next Topic

Continue to [Agile Estimation and Planning](./06-agile-estimation-planning.md) to learn how to estimate and plan work using Agile techniques.
