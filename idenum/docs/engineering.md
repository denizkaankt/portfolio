# IDENUM — Architecture

This document defines the high-level technical architecture of IDENUM.

It describes how the major system components relate to each other and where responsibility belongs.

It does not define detailed implementation, database schemas, API endpoints, or individual algorithms.

Those belong to:

- [[01 Behavior Engine|Behavior Engine]]
- [[02 Data Model|Data Model]]
- [[03 API|API]]
- [[04 Database|Database]]
- [[05 ADR|ADR]]

---

# 1. Architectural Goal

IDENUM is a cognitive support system.

Its architecture must allow the system to:

- capture information
- retain relevant memory
- understand context
- determine relevance and priority
- decide whether intervention is necessary
- provide the smallest useful intervention
- learn from confirmed user behavior
- close mental load loops

The architecture should minimize unnecessary complexity.

The system should remain understandable, testable, and replaceable at the component level.

---

# 2. Core Architecture Principle

The application interface is not the cognitive system.

The cognitive system exists behind the interface.

The high-level flow is:

```text
User Input
    ↓
Capture
    ↓
Memory
    ↓
Context
    ↓
Priority
    ↓
Trust Check
    ↓
Intervention Decision
    ↓
Silence or Action
    ↓
Learning
```

The system should not jump directly from user input to intervention.

Each stage exists to reduce unnecessary actions.

---

# 3. Major System Layers

IDENUM is organized into the following conceptual layers:

```text
Interface Layer
       ↓
Cognitive Engine
       ↓
Memory Layer
       ↓
Context Layer
       ↓
Decision / Behavior Layer
       ↓
Persistence Layer
       ↓
External Services
```

These are architectural responsibilities, not necessarily separate applications or services.

The MVP should prefer a modular system over unnecessary distributed infrastructure.

---

# 4. Interface Layer

The interface is responsible for communication with the user.

It may include:

- text input
- notifications
- reminders
- recommendations
- confirmation
- settings
- permissions
- future voice or other interfaces

The interface should not contain core cognitive decisions.

It presents decisions made by the system.

---

# 5. Cognitive Engine

The Cognitive Engine is the central intelligence layer of IDENUM.

Its responsibility is to transform raw user information into appropriate system behavior.

Conceptually it contains:

```text
Capture
Memory
Context
Decision Support
Trust
Learning
```

The Cognitive Engine should remain independent from the UI whenever practical.

---

# 6. Capture

Capture receives information from the user or connected systems.

Examples:

- user-created task
- note
- reminder request
- observed event
- calendar information
- contextual signal

Capture should focus on understanding what was provided.

It should not immediately determine how or when to intervene.

---

# 7. Memory

Memory stores information that may reduce future cognitive load.

IDENUM uses different memory lifetimes.

At the product level, memory includes:

```text
Temporary / Working Memory
        ↓
Persistent Memory
        ↓
Learned Behavioral Memory
```

Temporary information should not automatically become permanent memory.

Persistent memory should have a purpose.

Learned behavioral memory requires stronger evidence and appropriate user trust.

The detailed memory architecture belongs in:

[[../03 product/02 Memory System|Memory System]]

Technical representation belongs in:

[[02 Data Model|Data Model]]

---

# 8. Context

Context determines the circumstances surrounding a piece of information.

Possible context signals include:

- time
- location
- calendar state
- current activity
- relevant events
- known user state
- environmental signals

Context should be used to determine whether information is relevant now.

Time alone should not automatically trigger intervention when meaningful context is available.

---

# 9. Priority

Priority determines whether something deserves attention.

Priority should consider:

- importance
- urgency
- consequences
- user goal
- context
- confidence
- existing user commitments

Priority is not the same as intervention.

Something may be important without requiring immediate user attention.

---

# 10. Trust Check

Before intervention, the system should evaluate whether it has sufficient confidence and trust to act.

The Trust layer considers factors such as:

- confidence in interpretation
- confidence in context
- previous user responses
- permission state
- memory reliability
- intervention history

When confidence is insufficient:

> Prefer silence or clarification over unjustified action.

Trust is a prerequisite for increasing system autonomy.

---

# 11. Decision / Behavior Layer

The Behavior Engine determines what the system should do.

The system has four primary behavior modes:

```text
Ignore
   ↓
Silent Monitor
   ↓
Short Intervention
   ↓
Justified Recommendation
```

The system should prefer the smallest effective behavior.

The Behavior Engine should not assume that every detected event requires user-facing output.

Detailed behavior rules belong in:

[[01 Behavior Engine|Behavior Engine]]

---

# 12. Intervention

Intervention is the point where IDENUM chooses whether to communicate with the user.

Possible outcomes include:

```text
Silence
Reminder
Informational Suggestion
Operational Suggestion
```

Intervention should be contextual whenever possible.

A reminder and an intervention are not architecturally identical.

A reminder may be time-based.

An intervention may depend on context, risk, and user state.

---

# 13. Learning

Learning allows the system to improve its behavior over time.

Learning should not create permanent assumptions from isolated events.

The system should distinguish:

```text
Observation
   ↓
Repeated Pattern
   ↓
Confidence
   ↓
Confirmed Learning
```

Sensitive information should not become persistent behavioral knowledge without appropriate confirmation.

Learning should improve usefulness without creating hidden assumptions.

---

# 14. Persistence Layer

The persistence layer stores system state.

It may contain:

- mental load items
- memory
- context-related state
- user preferences
- trust information
- intervention history
- learning state

The persistence layer should not contain business logic that belongs in the Cognitive Engine.

Database-specific implementation belongs in:

[[04 Database|Database]]

---

# 15. External Services

IDENUM may interact with external systems such as:

- calendar services
- location services
- notification systems
- AI model providers
- contacts
- device capabilities
- future sensors or wearables

External services are dependencies.

The core cognitive logic should not become tightly coupled to one external provider whenever practical.

---

# 16. AI / LLM Layer

LLMs are components inside the Cognitive Engine.

They are not the Cognitive Engine itself.

The architecture should not assume that an LLM is responsible for every decision.

LLMs may assist with:

- language understanding
- classification
- extraction
- summarization
- reasoning
- recommendation generation

Deterministic system logic should remain deterministic where practical.

The system should not delegate every decision to an LLM simply because an LLM is available.

---

# 17. LLM Provider Independence

The architecture should allow IDENUM to work with different AI model providers.

The application should not make the entire system dependent on one model provider.

Conceptually:

```text
Cognitive Engine
       ↓
AI Abstraction
       ↓
Model Provider
       ↓
LLM
```

This allows models to be changed without redesigning the entire product.

The exact provider integration belongs in the engineering implementation.

---

# 18. Data Flow

The primary data flow is:

```text
Input
  ↓
Capture
  ↓
Interpretation
  ↓
Memory / State
  ↓
Context Evaluation
  ↓
Priority Evaluation
  ↓
Trust Evaluation
  ↓
Behavior Decision
  ↓
Intervention or Silence
  ↓
User Response
  ↓
Learning
```

The flow is not necessarily synchronous.

Some stages may operate asynchronously depending on the event.

---

# 19. Event-Driven Behavior

IDENUM should be capable of responding to events rather than relying only on scheduled checks.

Examples:

```text
Calendar changed
Location changed
Task state changed
Time threshold reached
User created new item
Context changed
External signal changed
```

An event may trigger evaluation.

An event does not automatically trigger user communication.

The system evaluates whether intervention is justified.

---

# 20. Separation of Concerns

Each architectural layer should have a clear responsibility.

For example:

```text
Capture
→ What happened?

Memory
→ What do we know?

Context
→ What is happening now?

Priority
→ Does it matter?

Trust
→ Can we rely on this understanding?

Behavior
→ What should the system do?

Intervention
→ Should the user be involved?

Learning
→ What should be improved for the future?
```

No single component should silently own all of these responsibilities.

---

# 21. MVP Architecture Principle

The MVP should be built as a modular monolith unless a clear requirement justifies a distributed architecture.

The initial architecture should prioritize:

- speed of development
- clarity
- testability
- low operational complexity
- replaceable components
- reliable behavior

Do not introduce microservices simply because the system contains multiple conceptual engines.

Conceptual separation does not require physical service separation.

---

# 22. Backend Technology

The current backend decision is:

```text
Node.js
+
TypeScript
```

This decision is documented in:

[[05 ADR|ADR]]

The architecture should respect the accepted technology decision unless a new ADR explicitly changes it.

---

# 23. Suggested Backend Structure

The implementation may conceptually follow:

```text
Application
│
├── API
│
├── Cognitive Engine
│   ├── Capture
│   ├── Memory
│   ├── Context
│   ├── Priority
│   ├── Trust
│   ├── Behavior
│   └── Learning
│
├── AI Layer
│
├── Persistence
│
└── Integrations
```

This is a logical boundary.

The exact folder structure may evolve during implementation.

---

# 24. Dependency Direction

Dependencies should generally flow inward toward core system logic.

Conceptually:

```text
Interface / API
      ↓
Application Logic
      ↓
Cognitive Engine
      ↓
Domain Logic
      ↓
Persistence / Integrations
```

Core cognitive behavior should not depend directly on UI implementation.

External providers should be accessed through defined interfaces where practical.

---

# 25. Failure Philosophy

External systems and AI models can fail.

IDENUM should fail safely.

When a dependency fails:

- do not invent information
- do not create false certainty
- do not trigger unjustified intervention
- preserve existing reliable state
- retry when appropriate
- surface the problem when user action is actually required

Silence is preferable to confidently incorrect behavior.

---

# 26. Privacy and Data Minimization

IDENUM should store information because it has a useful purpose.

The architecture should avoid unnecessary data collection.

Data should have:

- a purpose
- an owner
- an appropriate retention model
- appropriate access controls

Sensitive information should receive stronger protection.

The technical security implementation belongs in the appropriate engineering documentation.

---

# 27. Observability

The system should be observable enough to understand:

- what happened
- what the system believed
- what decision was made
- whether intervention occurred
- what the user did afterward
- where failures occurred

Observability should support debugging and trust.

It should not become a justification for collecting unnecessary user data.

---

# 28. Testability

Core cognitive behavior should be testable independently of:

- UI
- external services
- individual LLM providers
- production infrastructure

Important behavior should be reproducible using controlled inputs.

Examples:

```text
Given context X
And mental load Y
When condition Z occurs
Then behavior should be A
```

The exact testing strategy belongs in engineering implementation.

---

# 29. Architecture Boundaries

The architecture should preserve these boundaries:

```text
Product
   ↓
Architecture
   ↓
Implementation
```

Not:

```text
Implementation
   ↓
Accidental Product Definition
```

Engineering should implement the product.

Engineering should not silently redefine what IDENUM is.

---

# 30. Relationship to Product Architecture

The technical architecture implements the product architecture.

The product-level flow is:

```text
Capture
→ Memory
→ Context
→ Priority
→ Trust Check
→ Intervention
→ Silence or Action
```

The engineering architecture provides the components necessary to execute this flow reliably.

The technical system should serve the product philosophy, not replace it.

---

# 31. Related Documents

Product foundation:

[[../01 vision/00 IDENUM Manifesto|IDENUM Manifesto]]

[[../01 vision/01 Vision|Vision]]

[[../01 vision/02 Principles|Principles]]

Product:

[[../03 product/00 Product Bible|Product Bible]]

[[../03 product/01 MVP Bible|MVP Bible]]

[[../03 product/02 Memory System|Memory System]]

[[../03 product/03 AI Philosophy|AI Philosophy]]

[[../03 product/05 Trust System|Trust System]]

Engineering:

[[01 Behavior Engine|Behavior Engine]]

[[02 Data Model|Data Model]]

[[03 API|API]]

[[04 Database|Database]]

[[05 ADR|ADR]]

AI collaboration:

[[../07 AI Workspace/Collaboration Rules|Collaboration Rules]]

[[../07 AI Workspace/Documentation Rules|Documentation Rules]]

[[../07 AI Workspace/Workflow|Workflow]]

---

# Final Principle

> The architecture should make IDENUM's cognitive philosophy executable without making the system unnecessarily complex.

The system should be:

- modular
- understandable
- testable
- replaceable
- safe
- context-aware
- minimally intrusive

Most importantly:

> Architecture exists to support the Cognitive Engine.

> The Cognitive Engine exists to reduce human cognitive load.

> The human remains at the center.



# IDENUM — Behavior Engine

**Document Type:** Engineering Specification  
**Status:** Accepted  
**Version:** 1.0  
**Scope:** MVP  
**Parent Documents:** Architecture, ADR, Core Features, Trust System  
**Implementation Authority:** This document defines the deterministic behavioral contract of the IDENUM Cognitive Engine.

---

# 1. Purpose

The Behavior Engine is the decision and behavior layer of IDENUM.

Its responsibility is not to understand everything about the user.

Its responsibility is to determine:

> **Given the current state of an entrusted open loop, what should IDENUM do now?**

The Behavior Engine converts:

```text
Memory
+
Context
+
Priority
+
Trust
+
Current Event
```

into:

```text
Behavior
```

Possible behavior outcomes are:

```text
Ignore
Silent Monitor
Reminder
Informational Suggestion
Operational Suggestion
```

The Behavior Engine must always prefer the **minimum effective behavior**.

---

# 2. Core Principle

The Behavior Engine exists to reduce cognitive load.

Therefore:

> **A behavior is successful only if it reduces more cognitive load than it creates.**

The engine must never optimize for:

- engagement,
    
- conversation length,
    
- notification frequency,
    
- apparent intelligence,
    
- number of recommendations.
    

It optimizes for:

- correct behavior,
    
- appropriate timing,
    
- contextual relevance,
    
- user trust,
    
- minimal intervention,
    
- successful closure.
    

---

# 3. Architectural Position

The Behavior Engine sits between the Cognitive Engine inputs and the persistence/output layers.

Conceptually:

```text
                INTERFACE
                    ↓
                 CAPTURE
                    ↓
                UNDERSTAND
                    ↓
                  MEMORY
                    ↓
                 CONTEXT
                    ↓
                 PRIORITY
                    ↓
                  TRUST
                    ↓
            ┌─────────────────┐
            │ BEHAVIOR ENGINE │
            └─────────────────┘
                    ↓
          ┌─────────┴─────────┐
          ↓                   ↓
       SILENCE            INTERVENTION
                              ↓
                         USER ACTION
                              ↓
                           CLOSURE
```

The Behavior Engine is not the UI.

The PWA is an interface adapter.

The Behavior Engine determines behavior independently from presentation.

---

# 4. Responsibility

The Behavior Engine MUST:

- evaluate current loop state,
    
- evaluate relevant context,
    
- evaluate priority,
    
- evaluate trust constraints,
    
- determine whether intervention is necessary,
    
- select the minimum effective behavior,
    
- produce a structured behavior decision,
    
- avoid unauthorized autonomy,
    
- preserve user agency,
    
- support closure.
    

The Behavior Engine MUST NOT:

- directly render UI,
    
- independently modify product policy,
    
- make consequential life decisions,
    
- create permanent user assumptions,
    
- bypass Trust System rules,
    
- delegate the entire decision to an LLM.
    

---

# 5. Deterministic-First Principle

Behavior decisions should be deterministic whenever deterministic logic is sufficient.

LLMs may assist with:

- natural-language interpretation,
    
- ambiguous input understanding,
    
- semantic classification,
    
- extracting structured information.
    

LLMs must not independently control:

- trust escalation,
    
- permission boundaries,
    
- intervention authority,
    
- safety boundaries,
    
- loop lifecycle,
    
- final behavioral constraints.
    

The principle is:

```text
LLM
 ↓
Interpretation / Input
 ↓
Structured State
 ↓
Deterministic Rules
 ↓
Behavior Decision
```

not:

```text
LLM
 ↓
"Decide what IDENUM should do"
```

---

# 6. Behavior Pipeline

Every behavior evaluation follows the conceptual pipeline:

```text
1. Receive Event
2. Load Open Loop
3. Validate Loop State
4. Evaluate Context
5. Evaluate Priority
6. Evaluate Trust
7. Determine Minimum Effective Behavior
8. Generate Behavior Decision
9. Execute or Suppress
10. Record Outcome
11. Update Loop State
12. Await Closure
```

No step may be skipped when it is relevant to the decision.

---

# 7. Input Contract

The Behavior Engine receives a structured evaluation context.

Conceptually:

```text
BehaviorInput

user
openLoop
event
context
priority
trustState
permissions
currentTime
previousInterventions
```

The exact TypeScript interface is defined during implementation.

The Behavior Engine should not require raw UI input.

Raw user input must first pass through Capture and Understand.

---

# 8. Open Loop State

The Behavior Engine operates on open loops.

A loop represents an unresolved cognitive obligation, intention, condition, or desired outcome entrusted to IDENUM.

Conceptual lifecycle:

```text
OPEN
  ↓
MONITORING
  ↓
TRIGGERED
  ↓
ACTION_REQUIRED
  ↓
COMPLETED
```

Alternative terminal states may include:

```text
CANCELLED
EXPIRED
```

The exact persistence representation belongs to the Data Model.

---

# 9. State Validation

Before any behavior decision, the engine must determine whether the loop is still active.

## If loop is completed

Result:

```text
IGNORE
```

No further intervention.

## If loop is cancelled

Result:

```text
IGNORE
```

No further intervention.

## If loop is expired

Result:

```text
IGNORE
```

unless the product specification explicitly defines a recovery behavior.

## If loop is active

Continue evaluation.

This prevents stale loops from generating unnecessary behavior.

---

# 10. Event Evaluation

The Behavior Engine may be triggered by:

- time events,
    
- context events,
    
- state changes,
    
- user actions,
    
- external supported events,
    
- recurrence events.
    

An event does not automatically produce an intervention.

The engine must ask:

> **"Did this event make an entrusted loop relevant?"**

If not:

```text
IGNORE
```

or:

```text
SILENT MONITOR
```

---

# 11. Context Evaluation

Context determines whether the current state is relevant to the loop.

Possible context dimensions include:

- time,
    
- approximate location/context,
    
- event state,
    
- loop state,
    
- recurrence state,
    
- relevant external state.
    

Context must be evaluated only when relevant to the loop.

The engine must not treat every available signal as relevant context.

---

# 12. Context Confidence

Context detection may be uncertain.

Conceptually:

```text
HIGH CONFIDENCE
MEDIUM CONFIDENCE
LOW CONFIDENCE
UNKNOWN
```

The Behavior Engine must not treat uncertain context as certain.

General rule:

```text
LOW / UNKNOWN
     ↓
Do not escalate intervention
     ↓
Monitor or remain silent
```

A low-confidence context event must not trigger a high-autonomy behavior.

---

# 13. Context Before Time

When both time and context are available, the engine should prefer the trigger that more accurately represents the user's intended condition.

Example:

User:

> "When I get to the supermarket, remind me to buy coffee."

Incorrect:

```text
17:00
↓
Reminder
```

Correct:

```text
Relevant supermarket context detected
↓
Priority evaluation
↓
Trust evaluation
↓
Reminder
```

Time may still serve as a fallback when explicitly defined or when the product specification permits it.

---

# 14. Priority Evaluation

Priority determines whether a relevant condition justifies attention.

Priority is not autonomous life prioritization.

The engine should evaluate:

```text
Goal relevance
+
Current condition
+
Urgency
+
Potential consequence
+
Intervention cost
```

The engine must distinguish:

```text
Important in life
```

from:

```text
Relevant to intervene now
```

Only the second is the Behavior Engine's responsibility.

---

# 15. Goal Relevance

A behavior should normally require an explicit or sufficiently structured user goal.

Example:

```text
Goal:
Buy coffee

Context:
Supermarket

Result:
Relevant
```

Where there is no clear goal:

```text
No explicit goal
+
No strong supported inference
↓
Do not recommend
```

The engine must not manufacture goals merely to justify intervention.

---

# 16. Intervention Cost

Every intervention has cognitive cost.

Conceptually:

```text
Intervention Value
-
Intervention Cost
=
Net Benefit
```

If net benefit is insufficient:

```text
SILENCE
```

Intervention cost includes:

- interruption,
    
- attention switching,
    
- confusion,
    
- unnecessary explanation,
    
- repeated notifications,
    
- decision burden.
    

---

# 17. Trust Evaluation

Before selecting a behavior above silent monitoring, the engine must evaluate the Trust System.

Conceptually:

```text
Candidate Behavior
       ↓
Trust Check
       ↓
Authorized?
```

If not authorized:

```text
Reduce autonomy
```

The engine must never use a higher level of trust from one capability to justify an unrelated behavior.

Example:

```text
Reminder Trust = High
Recommendation Trust = Low

Candidate:
Operational Suggestion

Result:
Suggestion not authorized
```

---

# 18. Permission Evaluation

Technical permission and behavioral trust are separate.

Both may be required.

Example:

```text
Location Permission
        +
Location Intervention Trust
        ↓
Location-based intervention permitted
```

If either requirement is missing:

```text
No location-based intervention
```

The engine must not bypass technical permission constraints.

---

# 19. Behavior Hierarchy

The Behavior Engine uses the following hierarchy:

```text
IGNORE
   ↓
SILENT MONITOR
   ↓
REMINDER
   ↓
INFORMATIONAL SUGGESTION
   ↓
OPERATIONAL SUGGESTION
```

This is not a mandatory escalation sequence.

The engine should select the **lowest behavior that adequately resolves the situation**.

---

# 20. Ignore

Use `IGNORE` when:

- the event is irrelevant,
    
- the loop is closed,
    
- the loop is cancelled,
    
- the loop is expired,
    
- the condition is not satisfied,
    
- no useful behavior is possible,
    
- intervention would create unnecessary cognitive load.
    

Output:

```text
behavior = IGNORE
```

No user-facing intervention occurs.

---

# 21. Silent Monitor

Use `SILENT_MONITOR` when:

- the loop remains active,
    
- the required condition has not yet occurred,
    
- context is uncertain,
    
- future evaluation may be useful,
    
- user attention is not currently required.
    

Example:

```text
Open Loop:
Buy coffee at supermarket

Current Context:
At home

Result:
SILENT_MONITOR
```

The system continues monitoring without contacting the user.

---

# 22. Reminder

Use `REMINDER` when:

- the user explicitly entrusted an action,
    
- the relevant condition has occurred,
    
- trust permits intervention,
    
- the user needs to act,
    
- no more complex intervention is necessary.
    

Example:

```text
User:
"Tomorrow at 09:00 call Ahmet."

09:00
↓
Reminder
```

Reminder should be concise.

---

# 23. Informational Suggestion

Use `INFORMATIONAL_SUGGESTION` when the user benefits from relevant information but does not require the system to recommend a specific decision.

The suggestion should:

- provide useful context,
    
- preserve alternatives,
    
- avoid unnecessary complexity.
    

The system must not present information as a mandatory instruction.

---

# 24. Operational Suggestion

Use `OPERATIONAL_SUGGESTION` when IDENUM can reduce practical cognitive effort by proposing an appropriate next action within the user's explicit or sufficiently established goal.

Examples may include:

- preparation suggestions,
    
- next-step suggestions,
    
- practical alternatives.
    

Operational suggestions must remain:

- low consequence,
    
- within scope,
    
- user-controlled.
    

---

# 25. Justified Recommendation

A recommendation requires stronger justification than a reminder.

Before issuing a recommendation:

```text
Explicit / established goal?
        ↓
Current relevance?
        ↓
Meaningful benefit?
        ↓
Within MVP scope?
        ↓
Trust sufficient?
        ↓
User remains decision maker?
        ↓
Recommendation allowed
```

If any required condition fails:

```text
Lower behavior level
```

or:

```text
SILENCE
```

---

# 26. Behavior Selection Algorithm

Conceptual decision logic:

```text
function evaluateBehavior(input):

    if loop is closed:
        return IGNORE

    if loop is cancelled:
        return IGNORE

    if loop is expired:
        return IGNORE

    if event is irrelevant:
        return IGNORE

    if required context is not satisfied:
        return SILENT_MONITOR

    if context confidence is insufficient:
        return SILENT_MONITOR

    if priority does not justify intervention:
        return SILENT_MONITOR

    candidate = minimumEffectiveBehavior(input)

    if trust does not authorize(candidate):
        candidate = lowerAutonomyBehavior(candidate)

    if permissions do not allow(candidate):
        candidate = lowerAutonomyBehavior(candidate)

    if interventionCost >= expectedBenefit:
        return SILENT_MONITOR

    return candidate
```

This is conceptual behavior logic.

The implementation must convert it into deterministic, testable rules.

---

# 27. Minimum Effective Behavior

The engine should evaluate behaviors from lowest to highest autonomy.

Conceptually:

```text
Can silence solve it?
    ↓
YES → SILENCE

NO

Can monitoring solve it?
    ↓
YES → SILENT_MONITOR

NO

Can reminder solve it?
    ↓
YES → REMINDER

NO

Can informational suggestion solve it?
    ↓
YES → INFORMATIONAL_SUGGESTION

NO

Can operational suggestion solve it?
    ↓
YES → OPERATIONAL_SUGGESTION
```

The engine must not escalate merely because a higher-level behavior is available.

---

# 28. Duplicate Intervention Prevention

The engine must prevent repeated interventions for the same unresolved condition.

Example:

```text
Condition becomes true
↓
Reminder sent
↓
Condition remains true
↓
No immediate second reminder
```

A repeated intervention requires a new justification.

Possible new justification:

- relevant state changed,
    
- user explicitly requested repetition,
    
- recurrence rule requires it,
    
- sufficient time/context has passed according to the loop specification.
    

The system must not repeat simply because the condition remains true.

---

# 29. Intervention Cooldown

The implementation may use cooldown or deduplication mechanisms to prevent repeated interventions.

The exact duration must be defined by the specific feature or Data Model where necessary.

The general rule is:

> **Same condition + same unresolved state ≠ automatic new intervention.**

---

# 30. User Response Handling

After intervention, the engine should process user response.

Possible responses:

```text
ACCEPT
COMPLETE
REJECT
DISMISS
CORRECT
MODIFY
IGNORE
```

The response may affect:

- loop state,
    
- trust evidence,
    
- future behavior,
    
- monitoring state.
    

One response must not automatically create a permanent preference.

---

# 31. Closure Handling

A behavior is not complete when the notification is sent.

The engine must continue until the open loop reaches a terminal state.

Example:

```text
Open Loop
↓
Triggered
↓
Reminder
↓
User completes action
↓
COMPLETED
↓
Monitoring stops
```

If the user does not complete the action:

```text
Reminder
↓
Still Open
↓
Continue monitoring according to loop rules
```

The engine must not assume completion merely because an intervention was delivered.

---

# 32. Correction Handling

If the user corrects IDENUM:

> "No, I meant Friday."

The system must update the structured representation.

The corrected information becomes stronger evidence than the previous interpretation.

The engine must then re-evaluate the loop.

Conceptually:

```text
Correction
↓
Update State
↓
Recalculate Context / Priority / Trust
↓
New Behavior Decision
```

---

# 33. Cancellation Handling

If the user cancels a loop:

> "Forget that."

The engine must:

```text
Cancel Loop
↓
Stop Monitoring
↓
Suppress Future Intervention
```

Cancellation must not be interpreted as a general preference unless explicitly stated.

---

# 34. Failure Handling

If the engine cannot safely determine the appropriate behavior:

> **Fail toward lower autonomy.**

Preferred fallback:

```text
Silence
```

or:

```text
Silent Monitor
```

depending on whether future evaluation is meaningful.

The engine must not:

- guess,
    
- invent context,
    
- invent user intent,
    
- issue a high-autonomy recommendation,
    
- repeatedly ask unnecessary questions.
    

---

# 35. LLM Failure Handling

If an LLM produces:

- ambiguous extraction,
    
- unsupported intent,
    
- contradictory values,
    
- unsupported recommendation,
    
- low-confidence interpretation,
    

the deterministic system must be able to reject or constrain the output.

Example:

```text
LLM:
"User probably wants to..."

Deterministic validation:
No explicit goal found.

Result:
Do not create recommendation.
```

LLM output is an input to the engine, not the engine itself.

---

# 36. Behavior Decision Object

The engine should produce a structured behavior decision.

Conceptually:

```text
BehaviorDecision

behavior
reason
confidence
loopId
trigger
requiresUserAction
trustLevelRequired
trustLevelAvailable
contextSatisfied
prioritySatisfied
interventionAllowed
```

The exact TypeScript schema belongs to implementation and Data Model.

The decision must be machine-readable.

---

# 37. Explainability

The engine must internally be able to explain:

> Why did I act?

and:

> Why did I remain silent?

Possible internal reasons:

```text
LOOP_CLOSED
CONDITION_NOT_MET
CONTEXT_UNCERTAIN
LOW_PRIORITY
INSUFFICIENT_TRUST
PERMISSION_UNAVAILABLE
DUPLICATE_INTERVENTION
INTERVENTION_COST_TOO_HIGH
MINIMUM_BEHAVIOR_SELECTED
```

Explainability is primarily for:

- debugging,
    
- testing,
    
- trust analysis,
    
- auditability.
    

It does not mean exposing internal reasoning chains to the user.

---

# 38. Behavior Decision Priority

When multiple rules apply, the following priority order should be used:

```text
1. Product Boundaries
2. User Explicit Instruction
3. Safety / Permission Constraints
4. Loop State
5. Trust Constraints
6. Context Relevance
7. Priority
8. Minimum Intervention
9. Learning Signals
```

A lower-priority signal cannot override a higher-priority constraint.

---

# 39. Rule Conflict Resolution

When rules conflict:

```text
Higher-level constraint wins.
```

Example:

```text
Priority:
HIGH

Trust:
INSUFFICIENT

Result:
No high-autonomy intervention.
```

Another example:

```text
Context:
MATCHED

Permission:
DENIED

Result:
Context-based location behavior cannot execute.
```

The engine must not resolve such conflicts using LLM judgment.

---

# 40. Learning Constraints

The Behavior Engine may consume learning signals but must not independently convert them into permanent preferences.

Example:

```text
Repeated rejection
↓
Learning signal
↓
Trust evaluation
↓
Potential behavior reduction
```

Not:

```text
Repeated rejection
↓
Permanent global preference
```

Learning remains subordinate to explicit user instructions and system rules.

---

# 41. Four Primary Behavior Modes

The system-level behavior modes are:

```text
IGNORE
SILENT MONITOR
SHORT INTERVENTION
JUSTIFIED RECOMMENDATION
```

These map to concrete outputs as follows:

|System Mode|Possible Output|
|---|---|
|Ignore|No output|
|Silent Monitor|Monitoring continues|
|Short Intervention|Reminder|
|Justified Recommendation|Informational / Operational Suggestion|

The system mode describes autonomy.

The concrete behavior describes the resulting action.

---

# 42. Happy Path — Time-Based Loop

Input:

> "Tomorrow at 14:00 call Ahmet."

Flow:

```text
Capture
↓
Understand
↓
Open Loop
↓
Memory
↓
Monitor Time
↓
14:00
↓
Priority Check
↓
Trust Check
↓
Minimum Effective Behavior
↓
REMINDER
↓
User Action
↓
Closure
```

Expected result:

> One appropriate reminder and successful closure.

---

# 43. Happy Path — Context-Based Loop

Input:

> "When I'm at the supermarket, remind me to buy coffee."

Flow:

```text
Capture
↓
Understand
↓
Open Loop
↓
Memory
↓
Monitor Context
↓
Relevant Context Detected
↓
Context Confidence Check
↓
Priority
↓
Trust
↓
REMINDER
↓
User Action
↓
Closure
```

Expected result:

> No reminder before the relevant context; one short reminder when context becomes relevant.

---

# 44. Happy Path — Conditional Loop

Input:

> "If Ahmet hasn't replied by Friday, remind me to follow up."

Flow:

```text
Capture
↓
Understand
↓
Store Condition
↓
Monitor
↓
Expected Event?
   ├── YES → Close / Resolve
   └── NO
        ↓
      Friday
        ↓
      Priority
        ↓
      Trust
        ↓
      Reminder
```

Expected result:

> No intervention if the expected response occurs; intervention only if the condition becomes true.

---

# 45. Happy Path — Routine Loop

Input:

> "Every evening remind me to prepare for tomorrow."

Flow:

```text
Capture
↓
Understand Recurrence
↓
Store Routine
↓
Monitor Recurrence
↓
Relevant Time / Context
↓
Priority
↓
Trust
↓
Reminder
↓
User Action
↓
Routine Instance Closed
↓
Next Instance Created
```

The system must distinguish:

```text
Routine definition
```

from:

```text
Individual routine instance
```

The exact data representation belongs to the Data Model.

---

# 46. Edge Case — Closed Before Trigger

```text
Loop:
Call Ahmet tomorrow

User:
Completes / cancels loop today

Tomorrow:
Trigger occurs
```

Expected behavior:

```text
IGNORE
```

No reminder.

---

# 47. Edge Case — Context Uncertainty

```text
Possible supermarket detected
Confidence: Low
```

Expected behavior:

```text
SILENT_MONITOR
```

No intervention.

---

# 48. Edge Case — Repeated Trigger

```text
Supermarket context detected
↓
Reminder delivered
↓
Context remains active
```

Expected behavior:

```text
No immediate duplicate reminder
```

---

# 49. Edge Case — Insufficient Trust

```text
Priority:
High

Candidate:
Operational Suggestion

Trust:
Reminder Trust only
```

Expected behavior:

```text
Reduce behavior
↓
Reminder or Silent Monitor
```

The engine must not escalate to recommendation.

---

# 50. Edge Case — Permission Denied

```text
Location permission:
Denied

Loop:
Location-dependent
```

Expected behavior:

- Do not execute location-dependent behavior.
    
- Do not pretend context was detected.
    
- Use a supported fallback only if explicitly defined.
    
- Otherwise remain silent or request permission at the appropriate interface layer.
    

---

# 51. Edge Case — Ambiguous Goal

Input:

> "I should probably deal with that thing tomorrow."

The system does not have sufficient information.

Expected behavior:

```text
Do not invent task
Do not invent deadline
Do not invent object

Ask minimum necessary clarification
```

If clarification is not necessary or user does not respond, the system must not create a fabricated open loop.

---

# 52. Edge Case — Conflicting Conditions

Example:

```text
Loop:
Buy coffee at supermarket

Current context:
Supermarket

User has already marked:
Coffee purchased
```

Expected behavior:

```text
Loop state takes precedence
↓
IGNORE
```

The engine must not intervene based on stale context.

---

# 53. Edge Case — Repeated Failure

If the same behavior repeatedly fails:

```text
Failure
↓
Failure
↓
Failure
```

the system should reduce autonomy.

It must not respond:

```text
More notifications
More aggressive suggestions
```

The correct direction is:

```text
Less autonomy
+
More explicit confirmation
```

---

# 54. Edge Case — LLM Contradiction

If the LLM extracts:

```text
Date: Monday
```

but the user explicitly said:

```text
Friday
```

the explicit user information wins.

LLM interpretation cannot override explicit user-provided facts without clarification.

---

# 55. Edge Case — Recommendation Without Goal

The engine detects a potentially useful action but cannot identify a relevant user goal.

Expected behavior:

```text
No recommendation
```

Potential usefulness alone is insufficient justification for intervention.

---

# 56. Edge Case — Information Unchanged

If:

```text
Previous state = Current state
No action required
```

Expected behavior:

```text
SILENCE
```

The engine must not repeat information simply because it remains true.

---

# 57. Testing Contract

Every Behavior Engine rule must be tested using:

```text
Happy Path
+
Edge Case
+
Failure Case
```

A behavior is not considered implemented merely because the happy path works.

---

# 58. Minimum Behavior Test Matrix

At minimum, tests must cover:

|Scenario|Expected Behavior|
|---|---|
|Active loop, condition not met|Silent Monitor|
|Active loop, condition met|Appropriate intervention|
|Closed loop|Ignore|
|Cancelled loop|Ignore|
|Expired loop|Ignore|
|Low-confidence context|Silent Monitor|
|Insufficient trust|Lower autonomy|
|Permission denied|No unauthorized behavior|
|Duplicate trigger|No unnecessary repeat|
|User rejection|Record feedback|
|User correction|Re-evaluate|
|User completion|Close loop|
|Ambiguous input|Clarify / no fabricated loop|
|LLM contradiction|Deterministic/user-confirmed value wins|
|No explicit goal|No recommendation|
|High intervention cost|Silence / lower behavior|

---

# 59. Behavior Engine Invariants

The following must always remain true.

### Invariant 1

The engine never makes consequential decisions for the user.

### Invariant 2

Trust constraints cannot be bypassed.

### Invariant 3

Closed loops cannot generate normal future interventions.

### Invariant 4

Low-confidence context cannot produce high-autonomy behavior.

### Invariant 5

The engine cannot invent user goals.

### Invariant 6

Repeated unchanged conditions cannot automatically produce repeated interventions.

### Invariant 7

One-off user behavior cannot create permanent preferences.

### Invariant 8

The engine prefers lower autonomy when uncertain.

### Invariant 9

LLM output cannot override deterministic system constraints.

### Invariant 10

Every intervention must have an identifiable reason.

---

# 60. Observability

Every behavior decision should be observable internally.

The system should be able to record:

```text
loopId
eventId
behavior
decisionReason
contextState
priorityState
trustState
permissionState
interventionState
timestamp
outcome
```

This is required for:

- debugging,
    
- evaluation,
    
- trust measurement,
    
- product iteration,
    
- regression testing.
    

The system must avoid storing unnecessary sensitive information solely for debugging.

---

# 61. Behavior Audit

For any intervention, the system should be able to reconstruct:

```text
What happened?
↓
Which loop was involved?
↓
Which condition triggered evaluation?
↓
Was context relevant?
↓
Was priority sufficient?
↓
Was trust sufficient?
↓
Which behavior was selected?
↓
Why?
↓
What happened afterward?
```

This provides an operational audit trail without requiring the system to expose internal chain-of-thought.

---

# 62. Implementation Boundary

The Behavior Engine should be implemented as a deterministic domain component.

The implementation should not depend directly on:

- PWA UI components,
    
- notification rendering,
    
- database-specific implementation details,
    
- LLM provider-specific APIs.
    

Instead:

```text
Input State
↓
Behavior Engine
↓
Behavior Decision
↓
Adapters / Services
```

This allows the Behavior Engine to be tested independently.

---

# 63. Separation of Concerns

The following responsibilities must remain distinct:

```text
Capture
→ Understand what the user said

Memory
→ Remember the entrusted state

Context
→ Determine whether conditions are relevant

Priority
→ Determine whether attention is justified

Trust
→ Determine whether behavior is authorized

Behavior Engine
→ Select what IDENUM should do

Interface
→ Present the result

Persistence
→ Store the state
```

No single layer should absorb all responsibilities.

---

# 64. LLM Boundary

LLMs may be used for:

- language understanding,
    
- extraction,
    
- classification,
    
- normalization,
    
- ambiguity detection,
    
- generating concise natural-language intervention text where appropriate.
    

LLMs must not be the final authority for:

- whether a user has granted trust,
    
- whether a capability is permitted,
    
- whether a loop is closed,
    
- whether a recommendation is within product scope,
    
- whether an intervention violates system rules,
    
- whether autonomy should increase.
    

The deterministic Behavior Engine remains authoritative.

---

# 65. Future Extensions

Future versions may add:

- richer contextual reasoning,
    
- more event types,
    
- more recommendation types,
    
- broader planning,
    
- more complex multi-loop interactions,
    
- richer learning.
    

Each new behavior must define:

1. required inputs,
    
2. required trust level,
    
3. allowed autonomy,
    
4. failure behavior,
    
5. edge cases,
    
6. acceptance criteria,
    
7. observability requirements.
    

No future feature may silently extend the authority of an existing behavior.

---

# 66. Final Behavior Engine Principle

The Behavior Engine can be reduced to:

> **Observe everything relevant, act on as little as necessary, and never exceed the autonomy justified by context, priority, trust, and user intent.**

The best Behavior Engine is not the one that acts most often.

It is the one that makes the user think:

> **"It knew exactly when to stay quiet, and exactly when I needed it."**

---

# 67. Implementation Contract

Before implementation begins, the following must exist:

```text
Behavior Engine Specification
        ↓
Behavior Rules
        ↓
Decision Object
        ↓
Happy Paths
        ↓
Edge Cases
        ↓
Failure Cases
        ↓
Acceptance Tests
        ↓
Deterministic Implementation
```

If implementation exposes a behavior not defined in this document:

```text
STOP
↓
Document missing rule
↓
Determine whether Product / Trust / ADR decision is required
↓
Update authoritative document
↓
Implement
```

## The Behavior Engine must never become the place where undocumented product decisions are invented.



# IDENUM — Data Model

**Document Type:** Engineering Specification  
**Status:** Accepted  
**Version:** 1.0  
**Scope:** MVP  
**Parent Documents:** Architecture, ADR, Core Features, Trust System, Behavior Engine  
**Implementation Authority:** This document defines the canonical domain data model for the IDENUM MVP.

---

# 1. Purpose

This document defines the canonical data model of the IDENUM MVP.

Its purpose is to translate the product and behavior specifications into a stable domain model that can be implemented in PostgreSQL and consumed by the Cognitive Engine.

The Data Model defines:

- what entities exist,
    
- what each entity represents,
    
- how entities relate,
    
- what state they can have,
    
- what information must persist,
    
- what information must not be invented,
    
- which lifecycle transitions are allowed.
    

The Data Model does not define:

- database-specific SQL,
    
- API routes,
    
- UI components,
    
- LLM prompts,
    
- notification rendering.
    

Those concerns belong to Database, API, Interface, and AI implementation specifications.

---

# 2. Core Principle

The IDENUM data model exists to represent:

> **The minimum persistent state required to safely manage entrusted open cognitive loops.**

The system must not store information merely because it can.

Every persistent field should have a clear reason to exist.

The model therefore follows:

```text
Necessary
+
Relevant
+
Explicit
+
Bounded
+
Auditable
```

---

# 3. Domain Model Overview

The MVP domain consists of the following primary entities:

```text
User
│
├── OpenLoop
│    ├── Goal
│    ├── Trigger
│    ├── ContextCondition
│    ├── Recurrence
│    └── Closure
│
├── TrustState
│    └── TrustEvidence
│
├── Event
│
├── BehaviorDecision
│
├── Intervention
│    └── Feedback
│
└── Permission
```

Conceptually:

```text
                 USER
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
   OPEN LOOP   TRUST STATE  PERMISSION
       │
       ├──── GOAL
       ├──── TRIGGER
       ├──── CONTEXT
       └──── RECURRENCE
               │
               ↓
             EVENT
               │
               ↓
       BEHAVIOR DECISION
               │
               ↓
         INTERVENTION
               │
               ↓
           FEEDBACK
               │
               ↓
           CLOSURE
```

---

# 4. Entity Categories

Entities are divided into four conceptual groups.

## 4.1 Identity

```text
User
```

Represents the person using IDENUM.

## 4.2 Cognitive State

```text
OpenLoop
Goal
Trigger
ContextCondition
Recurrence
```

Represents what the system is entrusted to manage.

## 4.3 Decision State

```text
Event
BehaviorDecision
Intervention
Feedback
```

Represents what happened and what IDENUM decided to do.

## 4.4 Trust / Authorization

```text
TrustState
TrustEvidence
Permission
```

Represents what the system is allowed to do and why.

---

# 5. User

## Purpose

Represents the owner of IDENUM data and cognitive state.

## Conceptual fields

```text
User
- id
- createdAt
- updatedAt
```

The MVP should keep the User entity minimal.

Additional profile data must not be added unless required by a product capability.

## Rules

A user:

- owns their open loops,
    
- owns trust state,
    
- owns intervention history,
    
- controls permissions,
    
- can revoke entrusted capabilities.
    

No cognitive assumption should be stored on the User entity merely because it may be useful later.

---

# 6. OpenLoop

## Purpose

`OpenLoop` is the central domain entity of IDENUM.

It represents:

> **An unresolved cognitive obligation, intention, condition, or desired outcome that the user has entrusted to IDENUM.**

The product does not primarily manage tasks.

It manages open loops.

## Conceptual fields

```text
OpenLoop
- id
- userId
- title / summary
- originalInput
- status
- goalId
- triggerId
- recurrenceId
- createdAt
- updatedAt
- completedAt
- cancelledAt
- expiresAt
```

Some fields may be nullable depending on loop type.

---

# 7. OpenLoop Status

The canonical lifecycle is:

```text
OPEN
MONITORING
TRIGGERED
ACTION_REQUIRED
COMPLETED
CANCELLED
EXPIRED
```

Conceptual flow:

```text
OPEN
 ↓
MONITORING
 ↓
TRIGGERED
 ↓
ACTION_REQUIRED
 ↓
COMPLETED
```

Alternative terminal paths:

```text
OPEN → CANCELLED
OPEN → EXPIRED
MONITORING → CANCELLED
MONITORING → EXPIRED
```

Not every loop must use every state.

---

# 8. OpenLoop State Definitions

## OPEN

The loop has been captured and understood but has not yet entered active monitoring.

## MONITORING

The loop has an unresolved condition that IDENUM is currently monitoring.

## TRIGGERED

The condition associated with the loop has become true.

## ACTION_REQUIRED

The system has determined that user action is required.

## COMPLETED

The loop has been successfully closed.

## CANCELLED

The user explicitly cancelled the loop.

## EXPIRED

The loop passed its valid lifecycle without completion.

---

# 9. OpenLoop Lifecycle Rules

The following transitions are valid:

```text
OPEN → MONITORING
OPEN → CANCELLED
OPEN → EXPIRED

MONITORING → TRIGGERED
MONITORING → CANCELLED
MONITORING → EXPIRED

TRIGGERED → ACTION_REQUIRED
TRIGGERED → COMPLETED
TRIGGERED → CANCELLED

ACTION_REQUIRED → COMPLETED
ACTION_REQUIRED → CANCELLED
ACTION_REQUIRED → EXPIRED
```

Invalid transitions must be rejected by the domain layer.

For example:

```text
COMPLETED → MONITORING
```

is invalid.

A completed loop must not become active again unless an explicit future product behavior introduces reopening.

---

# 10. Original Input

Every captured open loop should preserve the original user input when necessary for interpretation, debugging, or auditability.

Example:

```text
"When I'm at the supermarket, remind me to buy coffee."
```

The original input is not the canonical behavioral representation.

It is source information.

The structured model is authoritative for execution.

---

# 11. Goal

## Purpose

Represents what the user is trying to accomplish within the open loop.

Examples:

```text
Goal:
Call Ahmet

Goal:
Buy coffee

Goal:
Follow up with Ahmet if no response
```

## Conceptual fields

```text
Goal
- id
- openLoopId
- description
- createdAt
- updatedAt
```

The MVP supports basic actionable goals.

It does not attempt to model deep psychological intent.

---

# 12. Goal Rules

A Goal:

- should normally originate from explicit user input,
    
- must remain within the open loop's scope,
    
- must not be invented to justify recommendations,
    
- may be corrected by the user.
    

The following is invalid:

```text
User:
"I need to buy coffee."

System:
Goal = "Maintain a healthy lifestyle."
```

The second statement is not supported by the user's explicit goal.

---

# 13. Trigger

## Purpose

Defines what condition can cause an open loop to become relevant.

Supported MVP trigger categories include:

```text
TIME
CONTEXT
CONDITION
RECURRENCE
```

## Conceptual fields

```text
Trigger
- id
- openLoopId
- type
- configuration
- active
- createdAt
- updatedAt
```

The exact trigger configuration should be strongly typed in implementation.

---

# 14. Trigger Types

## TIME

Example:

```text
Tomorrow at 14:00
```

## CONTEXT

Example:

```text
When at supermarket
```

## CONDITION

Example:

```text
If no response by Friday
```

## RECURRENCE

Example:

```text
Every evening
```

A loop may require more than one condition.

The implementation must not assume that all loops have a single trigger.

---

# 15. Trigger Rules

A trigger:

- defines when a loop becomes relevant,
    
- does not automatically define what behavior occurs,
    
- must be evaluated by the Behavior Engine,
    
- must not automatically generate a user-facing intervention.
    

Example:

```text
Trigger:
Supermarket detected

≠

Behavior:
Reminder
```

The trigger only causes evaluation.

Behavior is selected separately.

---

# 16. ContextCondition

## Purpose

Represents the context required for an open loop to become relevant.

Examples:

```text
Location:
Supermarket

Time:
Morning

Event:
Meeting starts

State:
No response received
```

## Conceptual fields

```text
ContextCondition
- id
- openLoopId
- type
- configuration
- confidenceRequirement
- active
```

The exact structure depends on supported context types.

---

# 17. Context Types

MVP context may include:

```text
LOCATION_CONTEXT
TIME_CONTEXT
EVENT_CONTEXT
STATE_CONTEXT
RECURRENCE_CONTEXT
```

Future context types must not be added without defining:

- required permission,
    
- trust requirements,
    
- accuracy requirements,
    
- failure behavior.
    

---

# 18. Context Confidence

Context evaluation should support confidence.

Conceptually:

```text
HIGH
MEDIUM
LOW
UNKNOWN
```

The data model may store the relevant confidence associated with an Event or Context Evaluation.

The Behavior Engine remains responsible for determining whether confidence is sufficient for action.

The Data Model must not itself decide:

> "High confidence means intervene."

That is a Behavior Engine rule.

---

# 19. Recurrence

## Purpose

Represents a repeating open-loop definition.

Example:

```text
Every evening
```

## Conceptual fields

```text
Recurrence
- id
- openLoopId
- rule
- timezone
- active
- createdAt
- updatedAt
```

The exact recurrence syntax is an implementation decision.

---

# 20. Routine Definition vs Instance

A recurring loop must distinguish between:

```text
Routine Definition
```

and:

```text
Routine Instance
```

Example:

```text
Routine:
Prepare for tomorrow every evening

Instance:
August 17 preparation

Instance:
August 18 preparation

Instance:
August 19 preparation
```

The recurring definition persists.

Each occurrence has its own lifecycle.

The implementation may represent this using a separate entity or an explicit instance model depending on database design.

---

# 21. Event

## Purpose

Represents something that happened and may require Behavior Engine evaluation.

An Event is not necessarily user-facing.

Examples:

```text
TIME_REACHED
LOCATION_CONTEXT_DETECTED
CONTEXT_CHANGED
EXPECTED_EVENT_OCCURRED
EXPECTED_EVENT_NOT_OCCURRED
USER_ACTION
LOOP_STATE_CHANGED
RECURRENCE_TRIGGERED
```

## Conceptual fields

```text
Event
- id
- userId
- openLoopId
- type
- occurredAt
- payload
- confidence
- source
- processedAt
```

---

# 22. Event Rules

Events:

- trigger evaluation,
    
- do not automatically trigger intervention,
    
- must be associated with a relevant source,
    
- should be processed deterministically where possible.
    

The same Event should not unintentionally cause repeated behavior evaluation without deduplication.

---

# 23. Event Sources

Possible MVP sources include:

```text
SYSTEM
USER
TIME
LOCATION
EXTERNAL_SIGNAL
```

External signals are limited to explicitly supported capabilities.

The system must not create arbitrary integrations merely because an external event exists.

---

# 24. Event Idempotency

Event processing should be idempotent where practical.

If the same event is delivered twice:

```text
Event A
↓
Behavior evaluation
↓
Reminder
```

and then:

```text
Event A again
```

the system must not automatically produce:

```text
Reminder again
```

unless the product behavior explicitly permits repetition.

---

# 25. BehaviorDecision

## Purpose

Represents the deterministic decision produced by the Behavior Engine.

This is a critical domain object.

## Conceptual fields

```text
BehaviorDecision
- id
- userId
- openLoopId
- eventId
- behavior
- reason
- contextSatisfied
- prioritySatisfied
- trustSatisfied
- permissionSatisfied
- confidence
- createdAt
```

---

# 26. Behavior Types

Canonical behavior values:

```text
IGNORE
SILENT_MONITOR
REMINDER
INFORMATIONAL_SUGGESTION
OPERATIONAL_SUGGESTION
```

The domain model should not use arbitrary free-text behavior values.

New behavior types require an explicit product/engineering decision.

---

# 27. Decision Reason

The system should store a machine-readable reason.

Examples:

```text
LOOP_CLOSED
CONDITION_NOT_MET
CONTEXT_NOT_RELEVANT
CONTEXT_UNCERTAIN
LOW_PRIORITY
INSUFFICIENT_TRUST
PERMISSION_UNAVAILABLE
DUPLICATE_INTERVENTION
INTERVENTION_COST_TOO_HIGH
MINIMUM_EFFECTIVE_BEHAVIOR
```

The reason exists for:

- debugging,
    
- testing,
    
- observability,
    
- product evaluation.
    

It is not a substitute for exposing hidden model reasoning.

---

# 28. Intervention

## Purpose

Represents an actual user-facing intervention generated from a Behavior Decision.

A Behavior Decision can result in no Intervention.

For example:

```text
BehaviorDecision:
SILENT_MONITOR

Intervention:
NONE
```

## Conceptual fields

```text
Intervention
- id
- userId
- openLoopId
- behaviorDecisionId
- type
- content
- status
- createdAt
- deliveredAt
- respondedAt
```

---

# 29. Intervention Types

```text
REMINDER
INFORMATIONAL_SUGGESTION
OPERATIONAL_SUGGESTION
```

`IGNORE` and `SILENT_MONITOR` do not create user-facing interventions.

---

# 30. Intervention Status

Conceptual states:

```text
CREATED
DELIVERED
SEEN
ACCEPTED
DISMISSED
REJECTED
EXPIRED
```

Not every intervention must reach every state.

The exact delivery semantics depend on the Interface/API implementation.

---

# 31. Intervention Content

Intervention content should be:

- concise,
    
- relevant,
    
- actionable when necessary,
    
- consistent with the selected behavior,
    
- free of unnecessary explanation.
    

Example:

```text
"You're at the supermarket. Buy coffee."
```

The Data Model stores the resulting user-facing content where required for auditability.

The model must not require the database to store hidden LLM chain-of-thought.

---

# 32. Feedback

## Purpose

Represents the user's response to IDENUM behavior.

Possible feedback:

```text
ACCEPT
COMPLETE
REJECT
DISMISS
CORRECT
MODIFY
IGNORE
```

## Conceptual fields

```text
Feedback
- id
- userId
- interventionId
- openLoopId
- type
- payload
- createdAt
```

---

# 33. Feedback Rules

Feedback is evidence.

Feedback is not automatically a permanent preference.

Example:

```text
One rejected reminder
```

does not mean:

```text
Never remind this user again.
```

Likewise:

```text
One accepted suggestion
```

does not mean:

```text
Always make this type of suggestion.
```

The Learning and Trust systems determine whether repeated feedback should affect future behavior.

---

# 34. Closure

## Purpose

Represents the resolution of an open loop.

Closure may occur through:

- explicit user completion,
    
- explicit cancellation,
    
- supported automatic resolution,
    
- expiry.
    

## Conceptual fields

```text
Closure
- id
- openLoopId
- type
- source
- createdAt
- reason
```

---

# 35. Closure Types

```text
COMPLETED
CANCELLED
EXPIRED
```

Future automatic closure types require explicit definition.

The system must not infer completion from weak evidence.

---

# 36. Automatic Closure

Automatic closure is allowed only when the evidence is sufficiently reliable and the behavior is explicitly supported.

Example:

```text
Expected event:
Response received

Response occurs
↓
Loop condition resolved
↓
Automatic closure permitted
```

Weak inference such as:

> "The user probably did it"

must not automatically close an entrusted loop.

---

# 37. TrustState

## Purpose

Represents the current behavioral trust state of the user toward IDENUM capabilities.

Trust is not one global number.

## Conceptual fields

```text
TrustState
- id
- userId
- capability
- level
- status
- lastEvaluatedAt
- updatedAt
```

---

# 38. Trust Capabilities

The MVP conceptual trust dimensions are:

```text
CAPTURE
MEMORY
MONITORING
CONTEXT
INTERVENTION
RECOMMENDATION
PLANNING
```

Not every dimension requires an independent numerical score.

The system should prefer explicit state and evidence over opaque scoring.

---

# 39. Trust Levels

Canonical levels:

```text
REMINDER
MONITORING
RECOMMENDATION
PLANNING
```

These represent increasing behavioral authority.

Planning is a future-level capability and is not required for MVP success.

---

# 40. Trust Evidence

## Purpose

Represents an event or user action that can affect trust evaluation.

Examples:

```text
EXPLICIT_PERMISSION
SUCCESSFUL_OUTCOME
ACCEPTED_INTERVENTION
REJECTED_INTERVENTION
USER_CORRECTION
SYSTEM_FAILURE
REPEATED_SUCCESS
REPEATED_REJECTION
PERMISSION_REVOCATION
```

## Conceptual fields

```text
TrustEvidence
- id
- userId
- capability
- type
- sourceId
- createdAt
- metadata
```

TrustEvidence is evidence.

It does not itself change TrustState.

Trust rules determine how evidence affects trust.

---

# 41. Permission

## Purpose

Represents technical permission required to use a capability.

Examples:

```text
LOCATION
NOTIFICATIONS
MICROPHONE
CALENDAR
CONTACTS
```

Only permissions required by MVP features should be implemented.

## Conceptual fields

```text
Permission
- id
- userId
- type
- status
- grantedAt
- revokedAt
- updatedAt
```

---

# 42. Permission vs Trust

Permission and trust must remain separate.

Example:

```text
Location permission:
GRANTED

Trust:
INSUFFICIENT
```

means:

> Technically possible, behaviorally unauthorized.

Conversely:

```text
Location permission:
DENIED

Trust:
HIGH
```

means:

> User may trust the behavior, but the technical capability is unavailable.

Both must be evaluated before relevant behavior executes.

---

# 43. Entity Relationship Model

Conceptually:

```text
USER
 │
 ├───────────────┐
 │               │
 ↓               ↓
OPEN_LOOP      TRUST_STATE
 │               │
 ├── GOAL        └── TRUST_EVIDENCE
 │
 ├── TRIGGER
 │
 ├── CONTEXT_CONDITION
 │
 ├── RECURRENCE
 │
 └──────┐
        ↓
      EVENT
        ↓
BEHAVIOR_DECISION
        ↓
  INTERVENTION
        ↓
    FEEDBACK
        ↓
    CLOSURE
```

---

# 44. Cardinality

The conceptual relationships are:

```text
User 1 ── N OpenLoop

User 1 ── N Event

User 1 ── N TrustState

User 1 ── N TrustEvidence

User 1 ── N Permission

OpenLoop 1 ── N Goal

OpenLoop 1 ── N Trigger

OpenLoop 1 ── N ContextCondition

OpenLoop 0..1 ── 1 Recurrence

OpenLoop 1 ── N Event

OpenLoop 1 ── N BehaviorDecision

BehaviorDecision 1 ── 0..1 Intervention

Intervention 1 ── N Feedback

OpenLoop 1 ── 0..1 Closure
```

The exact normalization may be adjusted during database design if behavior and performance requirements justify it.

Such adjustments must not change domain semantics.

---

# 45. Entity Ownership

Every user-specific entity must be traceable to a User.

At minimum:

```text
OpenLoop → User
Event → User
TrustState → User
TrustEvidence → User
Permission → User
Intervention → User
Feedback → User
```

The system must prevent cross-user access at the domain and persistence layers.

---

# 46. Data Ownership

The user owns their:

- open loops,
    
- goals,
    
- feedback,
    
- interventions,
    
- trust state,
    
- permissions,
    
- relevant history.
    

IDENUM may use these records to provide the product behavior but must not treat them as unrestricted general-purpose data.

---

# 47. Required vs Optional Information

The system must distinguish:

```text
Required
Optional
Unknown
Not Applicable
```

Unknown information must not automatically become:

```text
null = false
```

or an invented default.

Example:

If the user says:

> "Remind me to call Ahmet."

The system does not know the time.

It must not silently assign:

```text
18:00
```

---

# 48. Explicit vs Inferred Data

Data should preserve whether it originated from explicit user input or system inference where that distinction matters.

Conceptually:

```text
Source:
EXPLICIT_USER
SYSTEM_INFERENCE
EXTERNAL_EVENT
SYSTEM_GENERATED
```

Explicit user-provided information should generally have stronger authority than inference.

---

# 49. Confidence

Confidence may be relevant for inferred information.

Examples:

```text
Goal confidence
Context confidence
Intent classification confidence
Entity extraction confidence
```

Confidence must not be treated as permission.

Example:

```text
Context confidence = 0.95
```

does not mean:

```text
Intervention authorized = true
```

Trust and permission remain separate.

---

# 50. Timestamps

Time-sensitive entities should use explicit timestamps.

At minimum:

```text
createdAt
updatedAt
```

Additional timestamps may include:

```text
triggeredAt
completedAt
cancelledAt
expiredAt
deliveredAt
respondedAt
```

All timestamps must have an unambiguous timezone strategy.

User-local time should be preserved where recurrence or time-based behavior depends on it.

---

# 51. Timezone

Time-based loops and recurrence must be evaluated using the relevant user timezone.

A recurrence such as:

> "Every evening"

must not depend on server timezone accidentally.

The system must explicitly determine the timezone used for evaluation.

---

# 52. Original Meaning Preservation

Structured data must preserve the user's intended meaning.

Transformation:

```text
Natural Language
↓
Structured Representation
```

must not become:

```text
Natural Language
↓
Simplified assumption
```

Example:

User:

> "If Ahmet hasn't replied by Friday, remind me."

The model must preserve the conditional relationship:

```text
Expected event:
Ahmet replies

Deadline:
Friday

Action if condition remains unresolved:
Remind user
```

It must not become:

```text
Reminder:
Friday
```

because that loses the condition.

---

# 53. Data Model and Behavior Engine

The Data Model provides state.

The Behavior Engine interprets that state.

Therefore:

```text
Data Model
=
What is true / stored

Behavior Engine
=
What should happen given what is true
```

The Data Model must not embed business decisions that belong in Behavior Engine rules.

---

# 54. Data Model and Trust System

The Data Model stores:

```text
TrustState
TrustEvidence
Permission
```

The Trust System determines:

```text
How trust changes
+
What trust permits
```

The database must not independently decide that:

```text
3 accepted interventions = Recommendation Trust
```

unless the Trust System explicitly defines that rule.

---

# 55. Data Model and LLM

LLMs may generate candidate structured data.

The system must validate it before persistence.

Conceptually:

```text
User Input
↓
LLM / Parser
↓
Candidate Structured Data
↓
Validation
↓
Domain Rules
↓
Persistence
```

The system must not blindly persist arbitrary LLM output.

---

# 56. Validation Rules

Before persistence:

### OpenLoop

Must have:

- valid user,
    
- valid status,
    
- meaningful goal or actionable representation,
    
- valid lifecycle.
    

### Trigger

Must have:

- valid type,
    
- valid configuration,
    
- valid associated loop.
    

### Event

Must have:

- valid type,
    
- valid timestamp,
    
- valid source.
    

### BehaviorDecision

Must have:

- valid behavior,
    
- valid loop,
    
- evaluation context,
    
- decision reason.
    

### Intervention

Must reference:

- valid behavior decision,
    
- valid user,
    
- valid open loop.
    

### Feedback

Must reference:

- valid intervention where applicable,
    
- valid user,
    
- valid feedback type.
    

---

# 57. Data Integrity Invariants

The following must always remain true.

### Invariant 1

Every OpenLoop belongs to exactly one User.

### Invariant 2

A completed OpenLoop cannot be actively monitored.

### Invariant 3

A cancelled OpenLoop cannot generate new normal interventions.

### Invariant 4

An expired OpenLoop cannot generate new normal interventions unless explicitly reactivated by supported behavior.

### Invariant 5

Every Intervention must originate from a BehaviorDecision.

### Invariant 6

Every BehaviorDecision must reference the state evaluated at decision time.

### Invariant 7

TrustEvidence cannot directly mutate TrustState without Trust System logic.

### Invariant 8

Permission status cannot be inferred from TrustState.

### Invariant 9

Unknown data cannot be silently converted into explicit facts.

### Invariant 10

An LLM cannot bypass domain validation.

---

# 58. Deletion and Retention

Deletion behavior must distinguish between:

```text
Active operational data
Historical audit data
User-controlled data
System-required records
```

A user deleting an open loop must stop future behavior associated with it.

Historical retention must be limited to what is necessary for:

- product operation,
    
- debugging,
    
- security,
    
- trust evaluation,
    
- explicitly defined audit requirements.
    

The exact retention policy belongs to the Database / Privacy specification.

---

# 59. Sensitive Data Minimization

The MVP should avoid storing sensitive information unless required for a supported feature.

The Data Model must not become a general-purpose personal surveillance database.

Examples of information that should not be stored merely because it could be useful:

- inferred personality traits,
    
- psychological profiles,
    
- relationship judgments,
    
- financial profiles,
    
- medical diagnoses,
    
- sensitive personal attributes.
    

---

# 60. Data Model Versioning

Changes to domain entities are potentially breaking changes.

Changes must be classified as:

```text
ADDITIVE
MODIFYING
DEPRECATED
BREAKING
```

A breaking domain change requires:

```text
Specification Update
↓
Migration Plan
↓
Implementation
↓
Regression Tests
```

The Data Model must not be casually changed during implementation.

---

# 61. MVP Entity Summary

|Entity|Purpose|MVP|
|---|---|--:|
|User|Identity / ownership|✓|
|OpenLoop|Core cognitive unit|✓|
|Goal|User's actionable objective|✓|
|Trigger|Activation condition|✓|
|ContextCondition|Relevant context|✓|
|Recurrence|Repeating loop|✓|
|Event|System/user occurrence|✓|
|BehaviorDecision|Deterministic decision|✓|
|Intervention|User-facing action|✓|
|Feedback|User response|✓|
|Closure|Loop resolution|✓|
|TrustState|Behavioral authority|✓|
|TrustEvidence|Trust signals|✓|
|Permission|Technical capability authorization|✓|

---

# 62. Minimal MVP Data Model

The smallest useful implementation can be represented conceptually as:

```text
User
  │
  ├── OpenLoop
  │     ├── Goal
  │     ├── Trigger
  │     └── ContextCondition
  │
  ├── Event
  │
  ├── TrustState
  │
  ├── Permission
  │
  └── Intervention
          │
          └── Feedback
```

The system should not add additional domain entities until a real product or engineering requirement exists.

---

# 63. Data Lifecycle

The general lifecycle is:

```text
CAPTURE
  ↓
STRUCTURE
  ↓
VALIDATE
  ↓
PERSIST
  ↓
MONITOR
  ↓
EVALUATE
  ↓
INTERVENE / SILENCE
  ↓
FEEDBACK
  ↓
CLOSE
  ↓
RETAIN / ARCHIVE / DELETE
```

---

# 64. Data Model Acceptance Criteria

The Data Model is considered successfully specified when:

## Open Loops

- OpenLoop is the central cognitive entity.
    
- OpenLoop lifecycle is explicitly defined.
    
- Terminal states are defined.
    
- Invalid lifecycle transitions can be rejected.
    

## Goals

- User goals can be represented.
    
- Deep psychological intent is not required.
    
- Goals cannot be invented to justify behavior.
    

## Triggers

- Time triggers are supported.
    
- Context triggers are supported.
    
- Conditional triggers are supported.
    
- Recurrence is supported.
    

## Events

- Events can trigger behavior evaluation.
    
- Duplicate event processing can be prevented.
    
- Events do not automatically imply intervention.
    

## Behavior

- BehaviorDecision is a separate entity from Intervention.
    
- Silent behavior does not require user-facing intervention.
    
- Decision reasons are observable.
    

## Interventions

- Interventions are linked to behavior decisions.
    
- Intervention outcomes can be recorded.
    

## Feedback

- User responses can be recorded.
    
- Feedback is treated as evidence rather than automatic permanent preference.
    

## Trust

- TrustState exists.
    
- TrustEvidence exists.
    
- Trust is scoped.
    
- Permission and trust are separate.
    

## Data Integrity

- User ownership is enforced.
    
- Closed loops stop normal processing.
    
- LLM-generated data is validated.
    
- Unknown information is not fabricated.
    

---

# 65. Data Model and Database Boundary

This document defines:

> **What data exists.**

The Database document will define:

> **How this data is physically persisted in PostgreSQL.**

Therefore, the next engineering document must translate this model into:

- tables,
    
- columns,
    
- primary keys,
    
- foreign keys,
    
- indexes,
    
- constraints,
    
- enums,
    
- migrations,
    
- transaction boundaries.
    

The Database specification must not silently introduce new domain concepts.

---

# 66. Data Model and API Boundary

This document defines:

> **The domain state.**

The API document will define:

> **How external layers read and mutate that state.**

The API must not expose arbitrary database operations.

It must expose domain operations.

Example:

Preferred:

```text
POST /loops
POST /loops/:id/complete
POST /loops/:id/cancel
```

rather than exposing:

```text
PATCH /database/open_loops
```

The API must operate through domain rules.

---

# 67. Data Model and Behavior Engine Boundary

The relationship is:

```text
Data Model
     ↓
State
     ↓
Behavior Engine
     ↓
Decision
     ↓
Intervention
```

The Data Model does not decide what the system should do.

The Behavior Engine does.

---

# 68. Final Data Model Principle

The IDENUM Data Model can be reduced to:

> **Store enough state for IDENUM to carry an entrusted open loop reliably, but never turn the system into a database of everything about the user.**

The central entity remains:

```text
OPEN LOOP
```

Everything else exists to help the system:

```text
Understand it
Remember it
Monitor it
Evaluate it
Act appropriately
Close it
```

---

# 69. Implementation Contract

Before implementation begins, the following chain must be respected:

```text
Data Model
↓
Domain Types
↓
Validation Rules
↓
Lifecycle Rules
↓
Database Schema
↓
Repository Layer
↓
Behavior Engine
↓
API
```

If implementation requires a new entity that is not defined here:

```text
STOP
↓
Identify why the entity is required
↓
Determine whether it is a true domain concept
↓
Update Data Model if necessary
↓
Update ADR if the architectural decision changes
↓
Continue implementation
```

## The database must never become the place where undocumented product behavior is invented.




````markdown
# IDENUM — API Specification

**Document Type:** Engineering Specification  
**Status:** Accepted  
**Version:** 1.0  
**Scope:** MVP  
**Parent Documents:** Architecture, ADR, Core Features, Trust System, Behavior Engine, Data Model  
**Implementation Authority:** This document defines the canonical application API contract for the IDENUM MVP.

---

# 1. Purpose

This document defines the application API of the IDENUM MVP.

The API exists to:

- connect the Interface Layer with the Cognitive Engine,
- expose domain operations,
- manage Open Loop lifecycle safely,
- expose Behavior Engine results,
- preserve Trust and Permission boundaries,
- prevent direct database access,
- support future interfaces beyond the PWA.

The API is not a database access layer.

> **The API exposes domain operations, not database operations.**

---

# 2. API Philosophy

The canonical architecture is:

```text
Interface
    ↓
API
    ↓
Application / Domain Layer
    ↓
Cognitive Engine
    ↓
Persistence
````

The following architecture is forbidden:

```text
Interface
    ↓
API
    ↓
Database CRUD
```

The API must not bypass domain rules.

---

# 3. Responsibilities

The API is responsible for:

- request validation,
    
- authentication,
    
- authorization,
    
- input normalization,
    
- domain operation invocation,
    
- response serialization,
    
- error standardization,
    
- idempotency where required.
    

The API is not responsible for:

- independently deciding behavior,
    
- implementing Trust rules,
    
- implementing Cognitive Engine logic,
    
- directly modifying database state,
    
- deciding whether an intervention should occur,
    
- blindly persisting LLM output.
    

---

# 4. API Style

The MVP uses:

```text
Protocol: HTTPS
Style: REST
Format: JSON
Version: v1
Base Path: /api/v1
```

Example:

```http
POST /api/v1/loops
```

---

# 5. API Versioning

The current API version is:

```text
/api/v1
```

Breaking API changes require a new version.

Example:

```text
/api/v2
```

Non-breaking additions may be introduced within the existing version.

Existing request and response contracts must not be silently broken.

---

# 6. Authentication

All user-specific API operations require authentication.

The authenticated identity is the authoritative source of:

```text
userId
```

The client must not be trusted to define ownership by sending an arbitrary `userId`.

Example:

```json
{
  "userId": "user_123"
}
```

must not override the authenticated user.

The request context should conceptually be:

```text
Authentication
      ↓
Authenticated User
      ↓
userId
      ↓
Domain Operation
```

---

# 7. Authorization

Every user-owned resource must be scoped to the authenticated user.

Examples:

```text
OpenLoop
Event
Intervention
Feedback
TrustState
TrustEvidence
Permission
```

A user must never be able to access another user's resources.

If a requested resource does not belong to the authenticated user, the API must not reveal unnecessary information about its existence.

---

# 8. Resource Ownership

The following entities are user-owned:

```text
OpenLoop
Event
Intervention
Feedback
TrustState
TrustEvidence
Permission
```

Every such resource must be traceable to exactly one authenticated user.

---

# 9. Core Resources

The MVP API exposes the following primary resources:

```text
/loops
/events
/interventions
/permissions
/trust
```

Feedback is represented as an operation on an Intervention:

```text
/interventions/:id/feedback
```

Lifecycle operations are represented as explicit domain actions.

---

# 10. Open Loop API

Open Loop is the central resource of IDENUM.

Available operations:

```text
POST   /api/v1/loops
GET    /api/v1/loops
GET    /api/v1/loops/:id
PATCH  /api/v1/loops/:id
POST   /api/v1/loops/:id/complete
POST   /api/v1/loops/:id/cancel
```

---

# 11. Create Open Loop

```http
POST /api/v1/loops
```

Creates a new Open Loop from user input.

Example request:

```json
{
  "input": "Yarın Ahmet'i ara"
}
```

Conceptual processing:

```text
User Input
    ↓
Capture
    ↓
Understand
    ↓
Validate
    ↓
Create OpenLoop
    ↓
Persist
```

The API may invoke the Capture / Understand components, but the final persisted structure must pass domain validation.

---

# 12. Create Open Loop Response

Successful creation:

```http
201 Created
```

Example:

```json
{
  "data": {
    "id": "loop_123",
    "status": "OPEN",
    "goal": {
      "id": "goal_123",
      "description": "Ahmet'i aramak"
    },
    "trigger": {
      "type": "TIME",
      "configuration": {
        "datetime": "2026-08-18T10:00:00+03:00"
      }
    },
    "createdAt": "2026-08-17T23:00:00+03:00"
  }
}
```

---

# 13. Ambiguous Capture

The system must not invent missing information.

Example:

```text
User:
"Ahmet'i ara."
```

If no time or context is specified, the system must not silently create:

```text
18:00
```

or another arbitrary time.

The resulting domain state may contain:

```json
{
  "data": {
    "id": "loop_123",
    "status": "OPEN",
    "goal": {
      "description": "Ahmet'i aramak"
    },
    "trigger": null
  }
}
```

If clarification is required, the application layer may return a structured clarification state.

The API must not fabricate information.

---

# 14. Get Open Loops

```http
GET /api/v1/loops
```

Returns Open Loops belonging to the authenticated user.

Supported query parameters:

```text
status
limit
cursor
```

Example:

```http
GET /api/v1/loops?status=OPEN
```

---

# 15. Open Loop Filtering

Supported status values:

```text
OPEN
MONITORING
TRIGGERED
ACTION_REQUIRED
COMPLETED
CANCELLED
EXPIRED
```

The API must reject unsupported status values.

---

# 16. Pagination

List endpoints should use cursor-based pagination.

Example:

```http
GET /api/v1/loops?limit=20&cursor=abc123
```

Response:

```json
{
  "data": [],
  "pagination": {
    "nextCursor": "def456",
    "hasMore": true
  }
}
```

Pagination implementation may be optimized according to database performance requirements without changing the API contract.

---

# 17. Get Single Open Loop

```http
GET /api/v1/loops/:id
```

Returns a single Open Loop belonging to the authenticated user.

Example response:

```json
{
  "data": {
    "id": "loop_123",
    "status": "MONITORING",
    "goal": {
      "description": "Kargoyu takip etmek"
    },
    "trigger": {
      "type": "CONDITION"
    }
  }
}
```

Database-internal fields must not be exposed unless explicitly part of the API contract.

---

# 18. Update Open Loop

```http
PATCH /api/v1/loops/:id
```

Used for explicit user corrections or modifications.

Example:

```json
{
  "goal": {
    "description": "Ahmet'i akşam ara"
  }
}
```

Another example:

```json
{
  "trigger": {
    "type": "TIME",
    "configuration": {
      "datetime": "2026-08-18T18:00:00+03:00"
    }
  }
}
```

---

# 19. Lifecycle Protection

The API must not allow arbitrary lifecycle mutation through PATCH.

This is forbidden:

```json
{
  "status": "COMPLETED"
}
```

Instead, explicit domain operations must be used:

```http
POST /api/v1/loops/:id/complete
```

and:

```http
POST /api/v1/loops/:id/cancel
```

This prevents clients from bypassing lifecycle rules.

---

# 20. Complete Open Loop

```http
POST /api/v1/loops/:id/complete
```

Explicitly closes an Open Loop.

Successful response:

```http
200 OK
```

Example:

```json
{
  "data": {
    "id": "loop_123",
    "status": "COMPLETED",
    "completedAt": "2026-08-18T18:32:00+03:00"
  }
}
```

Completion must pass domain validation.

---

# 21. Cancel Open Loop

```http
POST /api/v1/loops/:id/cancel
```

Explicitly cancels an Open Loop.

Example response:

```json
{
  "data": {
    "id": "loop_123",
    "status": "CANCELLED",
    "cancelledAt": "2026-08-18T18:32:00+03:00"
  }
}
```

A cancelled loop must not continue normal monitoring or intervention.

---

# 22. Event API

Events represent occurrences that may require Cognitive Engine evaluation.

Endpoint:

```http
POST /api/v1/events
```

Example:

```json
{
  "type": "LOCATION_CONTEXT_DETECTED",
  "occurredAt": "2026-08-18T18:30:00+03:00",
  "payload": {
    "context": "SUPERMARKET"
  }
}
```

---

# 23. Event Processing

Event processing follows:

```text
Event
  ↓
Validate
  ↓
Persist
  ↓
Find Relevant OpenLoops
  ↓
Context Evaluation
  ↓
Priority Evaluation
  ↓
Trust Check
  ↓
Behavior Decision
  ↓
Silence / Intervention
```

The Event endpoint must not directly send a notification.

The existence of an event does not imply that the user should be contacted.

---

# 24. Event Response

Event processing may be asynchronous.

For accepted processing:

```http
202 Accepted
```

Example:

```json
{
  "data": {
    "eventId": "event_123",
    "status": "ACCEPTED"
  }
}
```

The exact synchronous/asynchronous implementation may evolve without changing the domain semantics.

---

# 25. Event Idempotency

Event creation should support idempotency.

Example:

```http
Idempotency-Key: abc123
```

If the same event is submitted multiple times with the same idempotency key, the system must not unintentionally produce duplicate behavior.

Example:

```text
Event
 ↓
Behavior Evaluation
 ↓
Reminder
```

must not become:

```text
Event
 ↓
Behavior Evaluation
 ↓
Reminder
 ↓
Same Event Again
 ↓
Reminder Again
```

unless repetition is explicitly permitted by the Behavior Engine.

---

# 26. Intervention API

Interventions are actual user-facing outputs.

Endpoints:

```text
GET /api/v1/interventions
GET /api/v1/interventions/:id
POST /api/v1/interventions/:id/seen
POST /api/v1/interventions/:id/feedback
```

---

# 27. Get Interventions

```http
GET /api/v1/interventions
```

Supported query parameters:

```text
status
unread
limit
cursor
```

Example:

```http
GET /api/v1/interventions?unread=true
```

Only interventions belonging to the authenticated user may be returned.

---

# 28. Get Single Intervention

```http
GET /api/v1/interventions/:id
```

Returns an individual intervention.

Example:

```json
{
  "data": {
    "id": "intervention_123",
    "type": "REMINDER",
    "status": "DELIVERED",
    "content": "Markettesin. Kahve almayı unutma.",
    "createdAt": "2026-08-18T18:30:00+03:00"
  }
}
```

---

# 29. Intervention Lifecycle

Creating an intervention and delivering it are separate operations.

Conceptually:

```text
BehaviorDecision
      ↓
Intervention Created
      ↓
Delivery
      ↓
Seen
      ↓
User Response
```

Possible states:

```text
CREATED
DELIVERED
SEEN
ACCEPTED
DISMISSED
REJECTED
EXPIRED
```

The exact delivery mechanism belongs to the Interface / Notification implementation.

---

# 30. Mark Intervention as Seen

```http
POST /api/v1/interventions/:id/seen
```

Example response:

```json
{
  "data": {
    "id": "intervention_123",
    "status": "SEEN"
  }
}
```

This operation records that the user has seen the intervention.

It does not imply:

```text
accepted
```

or:

```text
completed
```

---

# 31. Intervention Feedback

```http
POST /api/v1/interventions/:id/feedback
```

Example:

```json
{
  "type": "ACCEPT"
}
```

Another example:

```json
{
  "type": "REJECT"
}
```

Another example:

```json
{
  "type": "CORRECT",
  "payload": {
    "correction": "Bunu yarın değil cuma günü hatırlat."
  }
}
```

---

# 32. Feedback Processing

Feedback follows:

```text
User Response
      ↓
API
      ↓
Feedback
      ↓
OpenLoop Update if Applicable
      ↓
Trust Evidence
      ↓
Future Behavior Evaluation
```

Feedback must not automatically become a permanent preference.

For example:

```text
One rejected reminder
```

must not automatically mean:

```text
Never remind this user again.
```

Likewise:

```text
One accepted suggestion
```

must not automatically mean:

```text
Always suggest this in the future.
```

---

# 33. Permission API

Permissions represent technical capabilities.

Endpoint:

```http
GET /api/v1/permissions
```

The API may expose current application-level permission state.

Example response:

```json
{
  "data": [
    {
      "type": "LOCATION",
      "status": "GRANTED"
    },
    {
      "type": "NOTIFICATIONS",
      "status": "GRANTED"
    },
    {
      "type": "MICROPHONE",
      "status": "DENIED"
    }
  ]
}
```

---

# 34. Permission Rules

Permission and Trust are separate concepts.

Example:

```text
Location Permission = GRANTED
Trust = INSUFFICIENT
```

means:

> The capability is technically available but behaviorally not authorized.

Conversely:

```text
Location Permission = DENIED
Trust = HIGH
```

means:

> The user may trust the behavior, but the technical capability is unavailable.

Both must be evaluated independently.

---

# 35. Trust API

MVP trust state may be exposed as read-only application state.

Endpoint:

```http
GET /api/v1/trust
```

Example:

```json
{
  "data": [
    {
      "capability": "MONITORING",
      "level": "MONITORING",
      "status": "ACTIVE"
    },
    {
      "capability": "RECOMMENDATION",
      "level": "REMINDER",
      "status": "LIMITED"
    }
  ]
}
```

The client must not directly set:

```text
Trust = HIGH
```

or otherwise mutate TrustState.

Trust changes belong to the Trust System.

---

# 36. Trust Mutation

The API must not expose arbitrary endpoints such as:

```http
PATCH /trust
```

with:

```json
{
  "level": "PLANNING"
}
```

Trust is evidence-driven.

Trust changes occur through:

```text
Behavior
 ↓
Outcome
 ↓
Feedback / Evidence
 ↓
Trust Evaluation
 ↓
TrustState
```

---

# 37. Capture API

Capture is represented through:

```http
POST /api/v1/loops
```

The MVP does not require a separate permanent `/capture` resource.

Different input modalities may use the same domain operation.

Examples:

```text
Text
Voice
Future Input Modalities
       ↓
Capture
       ↓
OpenLoop Creation
```

The Interface Layer determines how input is collected.

---

# 38. Voice Input

Voice input may eventually use:

```text
audio
 ↓
speech-to-text
 ↓
capture
 ↓
understand
 ↓
OpenLoop
```

The API contract should not couple the domain model to a specific speech provider.

The final API may accept either:

```text
transcribed text
```

or a dedicated audio-processing request depending on implementation.

The domain result remains an OpenLoop.

---

# 39. Context Input

Context events may be delivered through:

```http
POST /api/v1/events
```

Example:

```json
{
  "type": "LOCATION_CONTEXT_DETECTED",
  "occurredAt": "2026-08-18T18:30:00+03:00",
  "payload": {
    "context": "SUPERMARKET"
  }
}
```

The API does not itself decide whether the location is relevant.

That decision belongs to Context + Behavior Engine.

---

# 40. API and LLM Boundary

LLMs may be used internally for:

```text
Capture understanding
Entity extraction
Intent interpretation
Natural language transformation
Candidate recommendations
```

However:

```text
LLM
  ≠
API Authority
```

An LLM must not directly:

```text
complete a loop
grant permission
increase trust
send an intervention
change user ownership
```

without passing through deterministic application and domain rules.

---

# 41. LLM Output Validation

Conceptually:

```text
User Input
     ↓
LLM
     ↓
Candidate Structured Output
     ↓
Schema Validation
     ↓
Domain Validation
     ↓
Behavior / Trust Rules
     ↓
Persistence
```

Invalid or incomplete model output must be rejected, corrected, or returned for clarification.

---

# 42. Domain Commands

The API should map to explicit domain commands.

Examples:

```text
CreateOpenLoop
UpdateOpenLoop
CompleteOpenLoop
CancelOpenLoop
ProcessEvent
MarkInterventionSeen
RecordFeedback
```

The exact internal command implementation is an engineering concern, but the conceptual separation must remain.

---

# 43. Forbidden Generic CRUD

The API must not expose generic endpoints such as:

```text
POST /database
PATCH /database
DELETE /database
```

or:

```text
PATCH /loops/:id/status
```

for arbitrary state mutation.

Domain rules must remain enforceable.

---

# 44. Error Format

All API errors should follow a consistent structure.

Example:

```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "The requested operation is invalid.",
    "details": {}
  }
}
```

The `code` is machine-readable.

The `message` is human-readable.

The API must not expose:

- stack traces,
    
- SQL errors,
    
- internal file paths,
    
- secret values,
    
- hidden model reasoning.
    

---

# 45. Standard Error Codes

The MVP should support at least:

```text
INVALID_REQUEST
VALIDATION_ERROR
UNAUTHORIZED
FORBIDDEN
NOT_FOUND
CONFLICT
INVALID_STATE_TRANSITION
PERMISSION_REQUIRED
TRUST_REQUIRED
RATE_LIMITED
INTERNAL_ERROR
```

Additional error codes may be introduced when required.

---

# 46. HTTP Status Codes

Canonical mapping:

```text
200 OK
201 Created
202 Accepted
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Unprocessable Entity
429 Too Many Requests
500 Internal Server Error
```

The exact choice between `400` and `422` should remain consistent across the implementation.

---

# 47. Validation

Validation occurs at multiple levels:

```text
HTTP Validation
      ↓
Schema Validation
      ↓
Domain Validation
      ↓
Business Rules
      ↓
Persistence
```

Passing schema validation does not guarantee domain validity.

Example:

```json
{
  "status": "COMPLETED"
}
```

may be syntactically valid JSON but invalid as an OpenLoop mutation.

---

# 48. Concurrency

Lifecycle operations must be protected against race conditions.

Example:

```text
User completes loop
        +
System triggers same loop
```

must not result in:

```text
COMPLETED
+
new intervention
```

after completion.

Domain state must be checked transactionally.

---

# 49. Idempotent Lifecycle Operations

Where practical:

```text
complete
cancel
seen
feedback
```

should be safely repeatable.

For example, sending:

```http
POST /loops/123/complete
```

twice should not create two closure records or produce inconsistent state.

---

# 50. API and OpenLoop Lifecycle

The API must preserve the canonical lifecycle:

```text
OPEN
 ↓
MONITORING
 ↓
TRIGGERED
 ↓
ACTION_REQUIRED
 ↓
COMPLETED
```

Alternative terminal states:

```text
CANCELLED
EXPIRED
```

The API must reject invalid transitions.

Example:

```text
COMPLETED → MONITORING
```

is invalid.

---

# 51. API and Behavior Engine

The API does not replace the Behavior Engine.

The relationship is:

```text
Event
 ↓
Behavior Engine
 ↓
BehaviorDecision
 ↓
Intervention
 ↓
API
 ↓
Interface
```

The API exposes the result.

It does not independently decide:

```text
"User should receive a reminder."
```

---

# 52. API and Trust System

Trust evaluation belongs to the Trust System.

The API only:

- exposes relevant trust state,
    
- records relevant evidence through domain operations,
    
- enforces authorization boundaries.
    

The API must not contain duplicated Trust formulas.

---

# 53. API and Memory

Memory is not exposed as a generic:

```http
GET /memory
```

database.

The API exposes domain objects that require memory.

For example:

```text
OpenLoop
Goal
Trigger
Context
TrustState
```

Memory remains an internal Cognitive Engine capability.

---

# 54. API and Silence

Silence is a valid system outcome.

For example:

```text
Event
 ↓
Behavior Engine
 ↓
SILENT_MONITOR
 ↓
No Intervention
```

The API must not require every Event to produce a user-facing object.

Silence is not an error.

---

# 55. API and Intervention Cost

The Behavior Engine may determine:

```text
INTERVENTION_COST_TOO_HIGH
```

and produce:

```text
SILENT_MONITOR
```

The API must preserve that outcome rather than forcing an intervention.

---

# 56. API and Context

Context is evaluated before intervention.

Conceptually:

```text
Event
 ↓
Context
 ↓
Relevance
 ↓
Priority
 ↓
Trust
 ↓
Behavior
```

The API must not shortcut this pipeline.

---

# 57. API and User Decision Authority

IDENUM API must never silently turn recommendations into decisions.

For example:

```text
Operational Suggestion
```

may be presented to the user.

But the API must not interpret:

```text
suggestion
```

as:

```text
user decision
```

unless explicit user action confirms it.

---

# 58. Rate Limiting

API endpoints should be rate limited.

Higher-risk endpoints should receive stricter controls.

Examples:

```text
POST /events
POST /loops
POST /interventions/:id/feedback
```

Rate limiting must protect against:

- accidental event storms,
    
- duplicate submissions,
    
- abuse,
    
- notification loops.
    

Rate limits must not interfere with normal product behavior.

---

# 59. Observability

Important domain operations should be observable.

At minimum:

```text
OpenLoop created
OpenLoop completed
OpenLoop cancelled
Event processed
BehaviorDecision created
Intervention created
Intervention delivered
Feedback received
Trust evidence recorded
Permission changed
```

Logs must not contain unnecessary sensitive user content.

---

# 60. Auditability

Critical behavior decisions should be traceable through:

```text
Event
 ↓
BehaviorDecision
 ↓
Intervention
 ↓
Feedback
```

This enables investigation of:

> Why did IDENUM do this?

The system should be able to answer using structured decision metadata.

It must not require exposing hidden LLM chain-of-thought.

---

# 61. Security Principles

The API must follow:

```text
Authentication
Authorization
Input Validation
Domain Validation
Least Privilege
Data Minimization
Idempotency
Auditability
```

Security-sensitive operations must fail closed.

---

# 62. Privacy Principles

The API should return only the minimum information required by the requesting interface.

It must not expose internal cognitive state unnecessarily.

For example, an internal:

```text
BehaviorDecision.reason
```

may be useful for observability but does not automatically need to be returned to the client.

API responses should be purpose-specific.

---

# 63. Response Envelope

Successful resource responses should use:

```json
{
  "data": {}
}
```

Collections:

```json
{
  "data": [],
  "pagination": {}
}
```

Errors:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": {}
  }
}
```

The exact envelope implementation may be refined during implementation provided consistency is preserved.

---

# 64. API Endpoint Summary

|Method|Endpoint|Purpose|
|---|---|---|
|POST|`/api/v1/loops`|Create Open Loop|
|GET|`/api/v1/loops`|List Open Loops|
|GET|`/api/v1/loops/:id`|Get Open Loop|
|PATCH|`/api/v1/loops/:id`|Modify Open Loop|
|POST|`/api/v1/loops/:id/complete`|Complete Open Loop|
|POST|`/api/v1/loops/:id/cancel`|Cancel Open Loop|
|POST|`/api/v1/events`|Submit Event|
|GET|`/api/v1/interventions`|List Interventions|
|GET|`/api/v1/interventions/:id`|Get Intervention|
|POST|`/api/v1/interventions/:id/seen`|Mark Intervention Seen|
|POST|`/api/v1/interventions/:id/feedback`|Record Feedback|
|GET|`/api/v1/permissions`|Get Permission State|
|GET|`/api/v1/trust`|Get Trust State|

---

# 65. MVP API Flow

The primary user flow is:

```text
User
 ↓
POST /loops
 ↓
Capture
 ↓
Understand
 ↓
OpenLoop
 ↓
Monitor
 ↓
POST /events
 ↓
Context
 ↓
Priority
 ↓
Trust Check
 ↓
Behavior Decision
 ├── Ignore
 ├── Silent Monitor
 └── Intervention
        ↓
     User sees
        ↓
POST /interventions/:id/feedback
        ↓
     Feedback
        ↓
     Trust Evidence
        ↓
     Loop Completion
```

---

# 66. Example End-to-End Flow

User says:

```text
"Marketteyken kahve almayı hatırlat."
```

API:

```http
POST /api/v1/loops
```

Request:

```json
{
  "input": "Marketteyken kahve almayı hatırlat."
}
```

System creates:

```text
OpenLoop
 ├── Goal: Buy coffee
 └── Trigger: Supermarket context
```

Later:

```http
POST /api/v1/events
```

with:

```json
{
  "type": "LOCATION_CONTEXT_DETECTED",
  "payload": {
    "context": "SUPERMARKET"
  }
}
```

Cognitive Engine evaluates:

```text
Context = relevant
Priority = sufficient
Trust = sufficient
Permission = available
Intervention cost = low
```

Behavior Engine:

```text
SHORT INTERVENTION
```

creates:

```text
Intervention
```

The interface displays:

```text
"Markettesin. Kahve almayı unutma."
```

User completes:

```http
POST /api/v1/loops/loop_123/complete
```

Loop becomes:

```text
COMPLETED
```

---

# 67. Example Silence Flow

User has an active loop.

An unrelated event occurs.

```text
Event
 ↓
Relevant OpenLoop?
 ↓
NO
 ↓
BehaviorDecision
 ↓
IGNORE
 ↓
No Intervention
```

The API does not send anything to the user.

Silence is the correct outcome.

---

# 68. Example Trust Failure Flow

Context condition is satisfied.

However:

```text
Permission = available
Trust = insufficient
```

Behavior Engine:

```text
SILENT_MONITOR
```

Result:

```text
No Intervention
```

The API does not override the Trust System.

---

# 69. Example Invalid Flow

Client attempts:

```http
PATCH /api/v1/loops/loop_123
```

with:

```json
{
  "status": "COMPLETED"
}
```

API returns:

```http
422 Unprocessable Entity
```

Example:

```json
{
  "error": {
    "code": "INVALID_STATE_TRANSITION",
    "message": "OpenLoop status cannot be changed directly.",
    "details": {
      "use": "POST /api/v1/loops/:id/complete"
    }
  }
}
```

---

# 70. API Contract Rules

The following rules are mandatory:

1. API operates on domain concepts.
    
2. Database is never directly exposed.
    
3. User identity comes from authentication.
    
4. User ownership is always enforced.
    
5. Lifecycle changes use explicit commands.
    
6. LLM output is validated before persistence.
    
7. Trust cannot be directly mutated by clients.
    
8. Permission and Trust remain separate.
    
9. Events do not automatically imply interventions.
    
10. Silence is a valid outcome.
    
11. Duplicate events must not produce duplicate behavior unintentionally.
    
12. Closed loops cannot continue normal monitoring.
    
13. API must preserve user decision authority.
    
14. Internal reasoning must not be exposed as chain-of-thought.
    
15. Sensitive data must be minimized.
    
16. Critical domain operations must be observable.
    
17. Invalid state transitions must be rejected.
    
18. API responses must not expose unnecessary internal implementation details.
    

---

# 71. Acceptance Criteria

The API specification is considered implementation-ready when:

## Authentication

- Every user operation has authenticated ownership.
    
- Client-supplied user IDs cannot override authenticated identity.
    

## Open Loops

- Open Loops can be created.
    
- Open Loops can be retrieved.
    
- Open Loops can be modified.
    
- Open Loops can be completed.
    
- Open Loops can be cancelled.
    
- Lifecycle transitions are validated.
    

## Events

- Events can be submitted.
    
- Events are associated with the correct user.
    
- Duplicate events can be prevented.
    
- Events can initiate Cognitive Engine evaluation.
    

## Behavior

- API can expose resulting interventions.
    
- API does not make Behavior Engine decisions itself.
    
- Silent outcomes remain valid.
    

## Feedback

- User feedback can be recorded.
    
- Feedback can become Trust Evidence.
    
- Feedback does not automatically become permanent preference.
    

## Trust

- Trust state can be read.
    
- Trust cannot be arbitrarily modified through API.
    
- Permission and Trust remain separate.
    

## Security

- Cross-user access is prevented.
    
- Input is validated.
    
- Sensitive internal details are not exposed.
    
- Rate limiting and idempotency exist where required.
    

---

# 72. Boundary With Database

This document defines:

> **How application and interface layers communicate with the domain.**

The Database document defines:

> **How domain state is physically persisted in PostgreSQL.**

The Database specification must not introduce undocumented domain behavior.

---

# 73. Boundary With Behavior Engine

This document defines:

> **How behavior-related domain operations enter and leave the application.**

The Behavior Engine defines:

> **What IDENUM should do when an event occurs.**

The API must not duplicate Behavior Engine decision rules.

---

# 74. Boundary With Data Model

The Data Model defines:

> **What entities and states exist.**

The API defines:

> **How those entities and state transitions are accessed or mutated.**

The API must not expose arbitrary fields simply because they exist in the database.

---

# 75. Boundary With Trust System

The Trust System defines:

> **How trust is established, evaluated, and changed.**

The API defines:

> **How relevant trust state is consumed or safely exposed.**

The client must never directly assign trust.

---

# 76. Final Principle

The IDENUM API should be understood as:

```text
A controlled boundary between
the human/interface world
and the cognitive system.
```

It should make valid actions easy:

```text
Capture
Correct
Complete
Cancel
Respond
```

and invalid actions difficult or impossible:

```text
Invent state
Bypass Trust
Bypass Behavior Engine
Bypass lifecycle
Access another user
Directly manipulate database state
```

The fundamental rule is:

> **The API must preserve the intelligence, trust, and boundaries of the Cognitive Engine rather than becoming a shortcut around them.**

````

Bu dosyayla birlikte şu an engineering tarafında zincirimiz:

```text
Architecture
    ↓
Behavior Engine
    ↓
Data Model
    ↓
API
````

haline geldi.


````markdown
# IDENUM — Database Specification

**Document Type:** Engineering Specification  
**Status:** Accepted  
**Version:** 1.0  
**Scope:** MVP  
**Parent Documents:** Architecture, ADR, Core Features, Trust System, Behavior Engine, Data Model, API  
**Database:** PostgreSQL  
**Implementation Authority:** This document defines the canonical persistence architecture and database contract for the IDENUM MVP.

---

# 1. Purpose

This document defines how IDENUM domain state is persisted.

The database exists to provide:

- durable storage,
- transactional consistency,
- user ownership,
- Open Loop lifecycle persistence,
- Event persistence,
- Intervention persistence,
- Trust evidence persistence,
- Permission state persistence,
- auditability,
- reliable recovery.

The database is a persistence layer.

It is not a decision-making layer.

> **PostgreSQL stores state. The Cognitive Engine determines behavior.**

---

# 2. Database Philosophy

The canonical architecture is:

```text
Interface
    ↓
API
    ↓
Application / Domain Layer
    ↓
Cognitive Engine
    ↓
Persistence Layer
    ↓
PostgreSQL
````

The database must never become an alternative Cognitive Engine.

Forbidden:

```text
Database
    ↓
Business Logic
    ↓
Behavior Decision
```

Correct:

```text
Behavior Engine
    ↓
Domain Decision
    ↓
Persistence
    ↓
PostgreSQL
```

---

# 3. Database Technology

The MVP uses:

```text
Database: PostgreSQL
```

PostgreSQL is the canonical persistence system for the MVP.

The database implementation must prioritize:

- correctness,
    
- transactional integrity,
    
- explicit relationships,
    
- predictable queries,
    
- data ownership,
    
- recoverability.
    

---

# 4. Database Responsibilities

The database is responsible for:

- storing domain entities,
    
- enforcing structural integrity,
    
- enforcing ownership relationships,
    
- enforcing uniqueness where required,
    
- maintaining timestamps,
    
- maintaining foreign key relationships,
    
- supporting transactions,
    
- supporting indexes required by application access patterns.
    

The database is not responsible for:

- deciding interventions,
    
- calculating Trust,
    
- interpreting natural language,
    
- determining user intent,
    
- running LLMs,
    
- deciding Priority,
    
- determining Context relevance,
    
- deciding whether the system should speak.
    

---

# 5. Persistence Principles

The database must follow these principles:

1. Persist domain state, not UI state.
    
2. Preserve ownership boundaries.
    
3. Prefer explicit relationships.
    
4. Use transactions for state transitions.
    
5. Use foreign keys where relationships require integrity.
    
6. Use unique constraints where duplicate state is invalid.
    
7. Store timestamps consistently.
    
8. Never persist raw LLM output as authoritative domain state.
    
9. Preserve enough information for auditability.
    
10. Minimize sensitive data.
    
11. Avoid unnecessary duplication.
    
12. Keep database structure aligned with the Data Model.
    
13. Keep behavior rules outside the database.
    
14. Keep Trust formulas outside the database.
    
15. Keep API contracts outside the database.
    

---

# 6. Database Schema

The MVP database is conceptually organized around:

```text
users
    ↓
open_loops
    ↓
goals
    ↓
triggers

users
    ↓
events

users
    ↓
interventions
    ↓
feedback

users
    ↓
trust_states
    ↓
trust_evidence

users
    ↓
permissions
```

---

# 7. Core Entities

The MVP persistence model contains the following core entities:

```text
User
OpenLoop
Goal
Trigger
Event
Intervention
Feedback
TrustState
TrustEvidence
Permission
```

Additional implementation tables may be introduced when required without changing domain semantics.

---

# 8. ID Strategy

All persisted entities should use application-safe unique identifiers.

Recommended format:

```text
UUID
```

or an equivalent globally unique identifier.

Example:

```text
user_id
loop_id
goal_id
trigger_id
event_id
intervention_id
feedback_id
trust_state_id
trust_evidence_id
permission_id
```

IDs must not expose sequential database identifiers where this could create enumeration risk.

---

# 9. Timestamp Strategy

All persisted timestamps should use timezone-aware timestamps.

Recommended PostgreSQL type:

```sql
TIMESTAMPTZ
```

Canonical fields:

```text
created_at
updated_at
```

Where applicable:

```text
completed_at
cancelled_at
triggered_at
delivered_at
seen_at
occurred_at
expires_at
```

All timestamps should be stored in UTC.

Presentation-layer timezone conversion occurs outside persistence.

---

# 10. User Table

Table:

```text
users
```

Purpose:

Represents an authenticated IDENUM user.

Conceptual structure:

```text
users
├── id
├── created_at
└── updated_at
```

Potential future fields may include:

```text
email
display_name
timezone
locale
```

Only fields required by the MVP should be implemented initially.

---

# 11. User Ownership

Every user-owned domain entity must reference:

```text
users.id
```

Examples:

```text
open_loops.user_id
events.user_id
interventions.user_id
feedback.user_id
trust_states.user_id
trust_evidence.user_id
permissions.user_id
```

Foreign key relationships must prevent orphaned user-owned records.

---

# 12. Open Loops Table

Table:

```text
open_loops
```

Purpose:

Stores the central IDENUM Open Loop.

Conceptual structure:

```text
open_loops
├── id
├── user_id
├── status
├── source
├── created_at
├── updated_at
├── completed_at
├── cancelled_at
└── expires_at
```

---

# 13. Open Loop Status

Canonical statuses:

```text
OPEN
MONITORING
TRIGGERED
ACTION_REQUIRED
COMPLETED
CANCELLED
EXPIRED
```

Status transitions are controlled by the domain layer.

The database may enforce basic enum validity but must not become the source of behavioral rules.

---

# 14. Open Loop State Transition

Canonical lifecycle:

```text
OPEN
 ↓
MONITORING
 ↓
TRIGGERED
 ↓
ACTION_REQUIRED
 ↓
COMPLETED
```

Alternative terminal states:

```text
CANCELLED
EXPIRED
```

The application/domain layer validates whether a transition is legal.

The database transaction ensures that the final persisted state is consistent.

---

# 15. Open Loop Source

The source identifies how the Open Loop entered the system.

Possible values:

```text
TEXT
VOICE
SYSTEM
IMPORT
```

Only sources required by the MVP should be enabled initially.

The source is descriptive metadata.

It does not determine behavior.

---

# 16. Goal Table

Table:

```text
goals
```

Purpose:

Stores the user-facing objective represented by an Open Loop.

Conceptual structure:

```text
goals
├── id
├── open_loop_id
├── description
├── created_at
└── updated_at
```

A Goal belongs to an Open Loop.

---

# 17. Goal Rules

A Goal must not be treated as an instruction to execute automatically.

Example:

```text
Goal:
"Ahmet'i ara"
```

does not mean:

```text
IDENUM automatically calls Ahmet.
```

It represents the user's intended outcome.

---

# 18. Trigger Table

Table:

```text
triggers
```

Purpose:

Stores the condition under which an Open Loop may become relevant.

Conceptual structure:

```text
triggers
├── id
├── open_loop_id
├── type
├── configuration
├── created_at
└── updated_at
```

---

# 19. Trigger Types

MVP trigger types may include:

```text
TIME
CONTEXT
CONDITION
```

The exact enabled subset is controlled by the Core Features and Behavior Engine specifications.

---

# 20. Trigger Configuration

Trigger-specific configuration may be stored as structured JSON.

Example:

```json
{
  "datetime": "2026-08-18T18:00:00+03:00"
}
```

Context example:

```json
{
  "context": "SUPERMARKET"
}
```

The database stores configuration.

The Behavior Engine interprets it.

---

# 21. JSONB Usage

PostgreSQL `JSONB` may be used for flexible configuration where schema variation is intrinsic to the domain.

Appropriate examples:

```text
trigger.configuration
event.payload
intervention.metadata
```

JSONB must not become an excuse to store the entire domain model as unstructured JSON.

Core domain fields must remain explicit columns.

---

# 22. Event Table

Table:

```text
events
```

Purpose:

Stores external or internal occurrences that may require Cognitive Engine evaluation.

Conceptual structure:

```text
events
├── id
├── user_id
├── type
├── occurred_at
├── payload
├── idempotency_key
└── created_at
```

---

# 23. Event Types

Examples:

```text
LOCATION_CONTEXT_DETECTED
TIME_TRIGGER_REACHED
USER_ACTION
SYSTEM_STATE_CHANGED
```

Additional event types may be added as the Cognitive Engine expands.

Event type must not itself imply intervention.

---

# 24. Event Payload

Event-specific information may be stored in:

```text
payload JSONB
```

Example:

```json
{
  "context": "SUPERMARKET"
}
```

The payload is input to the Cognitive Engine.

It is not an authoritative Behavior Decision.

---

# 25. Event Idempotency

Events should support:

```text
idempotency_key
```

A suitable unique constraint should prevent accidental duplicate processing for the same user and idempotency key.

Conceptually:

```text
(user_id, idempotency_key)
```

must be unique when an idempotency key is present.

---

# 26. Event Retention

Events are operational records.

The MVP should retain enough event history to support:

- debugging,
    
- behavior auditing,
    
- trust evaluation,
    
- product analysis.
    

A future retention policy may archive or delete old events.

Retention policy must not silently remove data required for active Open Loops.

---

# 27. Intervention Table

Table:

```text
interventions
```

Purpose:

Stores user-facing outputs generated by the Behavior Engine.

Conceptual structure:

```text
interventions
├── id
├── user_id
├── open_loop_id
├── type
├── status
├── content
├── metadata
├── created_at
├── delivered_at
├── seen_at
└── expires_at
```

---

# 28. Intervention Types

MVP intervention types:

```text
REMINDER
INFORMATIONAL_SUGGESTION
OPERATIONAL_SUGGESTION
```

The system must not create an intervention for every event.

Silence remains a valid outcome.

---

# 29. Intervention Status

Possible statuses:

```text
CREATED
DELIVERED
SEEN
ACCEPTED
DISMISSED
REJECTED
EXPIRED
```

The exact state transition rules belong to the Behavior / Application layer.

---

# 30. Intervention Content

The user-visible intervention content may be stored as:

```text
content TEXT
```

The content should represent the final user-facing message.

Internal LLM generation artifacts should not be treated as authoritative content.

---

# 31. Intervention Metadata

Additional non-critical metadata may be stored in:

```text
metadata JSONB
```

Possible examples:

```json
{
  "channel": "WEB_PUSH",
  "behavior_mode": "SHORT_INTERVENTION"
}
```

Metadata must not contain hidden chain-of-thought.

---

# 32. Feedback Table

Table:

```text
feedback
```

Purpose:

Stores explicit user response to an Intervention.

Conceptual structure:

```text
feedback
├── id
├── user_id
├── intervention_id
├── type
├── payload
└── created_at
```

---

# 33. Feedback Types

MVP feedback types:

```text
ACCEPT
REJECT
CORRECT
DISMISS
```

Additional types may be added when required.

---

# 34. Feedback Is Evidence

Feedback must be treated as evidence.

It is not automatically a permanent preference.

Example:

```text
REJECT
```

does not mean:

```text
Never do this again.
```

The Trust System and Learning layer determine how evidence should influence future behavior.

---

# 35. Trust State Table

Table:

```text
trust_states
```

Purpose:

Stores the current trust state associated with a user and capability.

Conceptual structure:

```text
trust_states
├── id
├── user_id
├── capability
├── level
├── status
├── created_at
└── updated_at
```

---

# 36. Trust Capabilities

Trust may be evaluated independently for capabilities such as:

```text
REMINDER
MONITORING
RECOMMENDATION
PLANNING
```

Only capabilities required by the MVP should be enabled initially.

---

# 37. Trust Levels

The Trust System defines the semantic trust progression.

Conceptually:

```text
Reminder Trust
      ↓
Monitoring Trust
      ↓
Recommendation Trust
      ↓
Planning Trust
```

The database stores the resulting state.

It does not calculate the trust score or decide promotion.

---

# 38. Trust Status

Possible statuses:

```text
ACTIVE
LIMITED
SUSPENDED
```

The Trust System determines status.

---

# 39. Trust Evidence Table

Table:

```text
trust_evidence
```

Purpose:

Stores observable evidence that may influence Trust evaluation.

Conceptual structure:

```text
trust_evidence
├── id
├── user_id
├── trust_state_id
├── source_type
├── outcome
├── reference_id
├── metadata
└── created_at
```

---

# 40. Trust Evidence Sources

Examples:

```text
INTERVENTION_ACCEPTED
INTERVENTION_REJECTED
INTERVENTION_CORRECTED
LOOP_COMPLETED
LOOP_RETAKEN
UNNECESSARY_INTERVENTION
USER_CORRECTION
```

The exact evidence taxonomy belongs to the Trust System.

---

# 41. Trust Evidence Rules

Evidence must be immutable after creation wherever possible.

If an evidence record is incorrect, a corrective evidence record should be created rather than silently rewriting history.

This preserves auditability.

---

# 42. Permission Table

Table:

```text
permissions
```

Purpose:

Stores the current technical permission state for a user.

Conceptual structure:

```text
permissions
├── id
├── user_id
├── type
├── status
├── created_at
└── updated_at
```

---

# 43. Permission Types

MVP may include:

```text
LOCATION
NOTIFICATIONS
MICROPHONE
```

Additional permissions may be introduced later.

---

# 44. Permission Status

Possible values:

```text
GRANTED
DENIED
PROMPT_REQUIRED
UNAVAILABLE
```

The exact mapping to browser/device permission states belongs to the Interface Layer.

---

# 45. Permission vs Trust

Permission and Trust must remain separate.

Example:

```text
Permission:
LOCATION = GRANTED

Trust:
MONITORING = LIMITED
```

This means:

```text
Technical capability exists.
Behavioral authorization is limited.
```

The system must respect both.

---

# 46. Foreign Keys

Core foreign key relationships:

```text
open_loops.user_id
    → users.id

goals.open_loop_id
    → open_loops.id

triggers.open_loop_id
    → open_loops.id

events.user_id
    → users.id

interventions.user_id
    → users.id

interventions.open_loop_id
    → open_loops.id

feedback.user_id
    → users.id

feedback.intervention_id
    → interventions.id

trust_states.user_id
    → users.id

trust_evidence.user_id
    → users.id

trust_evidence.trust_state_id
    → trust_states.id

permissions.user_id
    → users.id
```

---

# 47. Delete Strategy

Deletion must respect domain ownership and lifecycle.

The MVP should avoid aggressive cascading deletes for critical historical records.

For example:

```text
User
 ↓
OpenLoop
 ↓
Intervention
 ↓
Feedback
```

should not automatically erase all historical evidence without an explicit data-retention decision.

Where deletion is required, the application must define the domain behavior first.

---

# 48. Soft Delete

Soft deletion should not be introduced universally.

Use it only when the domain requires preservation of historical records.

For example:

```text
deleted_at
```

may be appropriate for certain user-owned resources.

However, an unnecessary soft-delete layer should not complicate the MVP.

---

# 49. Indexing Strategy

Indexes must follow actual query patterns.

Initial important indexes include:

```text
open_loops(user_id)
open_loops(user_id, status)

events(user_id, occurred_at)

interventions(user_id, status)
interventions(user_id, created_at)

feedback(intervention_id)
feedback(user_id, created_at)

trust_states(user_id, capability)

trust_evidence(user_id, created_at)

permissions(user_id, type)
```

Additional indexes should be introduced based on measured query requirements.

---

# 50. Partial Indexes

Where appropriate, PostgreSQL partial indexes may be used for active states.

Example concept:

```text
open_loops
WHERE status IN ('OPEN', 'MONITORING', 'TRIGGERED', 'ACTION_REQUIRED')
```

This should only be implemented where it provides a measurable benefit.

---

# 51. Unique Constraints

Important uniqueness rules include:

```text
users
    unique authenticated identity

permissions
    unique (user_id, type)

trust_states
    unique (user_id, capability)
```

Event idempotency:

```text
events
    unique (user_id, idempotency_key)
```

when an idempotency key exists.

---

# 52. Transaction Strategy

Domain state transitions must use database transactions where multiple records must change atomically.

Example:

```text
Complete OpenLoop
      ↓
Update OpenLoop
      ↓
Create Trust Evidence
      ↓
Commit
```

These operations should not leave the system in a partially updated state.

---

# 53. Example Completion Transaction

Conceptually:

```text
BEGIN

1. Lock OpenLoop
2. Verify ownership
3. Verify current state
4. Validate completion
5. Update OpenLoop → COMPLETED
6. Set completed_at
7. Create relevant evidence
8. Commit
```

If any required step fails:

```text
ROLLBACK
```

---

# 54. Concurrency Control

Concurrent operations must be handled safely.

Example:

```text
User completes loop
        +
Behavior Engine processes trigger
```

The system must ensure that a completed loop cannot generate a new intervention after the completion transaction has become authoritative.

Possible implementation techniques:

```text
Row-level locking
Optimistic concurrency
Transactional state checks
```

The exact mechanism is an implementation decision.

---

# 55. State Consistency

The database must never intentionally contain:

```text
OpenLoop = COMPLETED
```

while an active intervention is incorrectly treated as pending for that same completed loop.

Application and domain transactions must preserve consistency between related entities.

---

# 56. Database Constraints vs Domain Rules

Database constraints should enforce structural invariants.

Examples:

```text
NOT NULL
FOREIGN KEY
UNIQUE
CHECK
```

Domain logic should enforce behavioral rules.

Examples:

```text
Can this loop transition to COMPLETED?
Should this event trigger an intervention?
Is Trust sufficient?
Is the intervention cognitively justified?
```

Do not move complex behavior rules into PostgreSQL.

---

# 57. Check Constraints

Simple invariants may be enforced with PostgreSQL `CHECK` constraints.

Examples:

```text
status must be a supported value
```

or:

```text
timestamps must not violate obvious structural constraints
```

Complex state transitions must remain in the application/domain layer.

---

# 58. Data Integrity Priority

When there is a conflict:

```text
Behavior
vs
Data Integrity
```

the system must preserve data integrity first.

When there is a conflict:

```text
Database convenience
vs
Domain correctness
```

domain correctness wins.

---

# 59. Raw LLM Data

Raw LLM requests and responses must not automatically be persisted as core domain data.

If model output needs to be retained for:

- debugging,
    
- evaluation,
    
- safety,
    
- observability,
    

it should use a separate controlled storage mechanism.

Core domain tables must store validated structured results.

---

# 60. Model Provenance

Where useful, structured model metadata may be stored.

Example:

```json
{
  "provider": "example",
  "model": "example-model",
  "version": "x",
  "confidence": 0.91
}
```

This metadata is optional and must not become part of the user's core domain state unless explicitly required.

---

# 61. Sensitive Data

The MVP should minimize storage of sensitive information.

Do not store data merely because it is technically available.

Store data only when required for:

```text
product functionality
security
auditability
trust
legal requirements
```

Sensitive data should have explicit retention rules.

---

# 62. Location Data

Location is especially sensitive.

The MVP should prefer storing:

```text
context
```

over storing continuous raw location history.

Example:

```text
SUPERMARKET
```

may be sufficient.

The system should not persist continuous GPS coordinates unless explicitly required by the product specification.

---

# 63. Event Data Minimization

Events should contain only the payload required for Cognitive Engine processing.

Bad:

```json
{
  "full_device_state": "...",
  "all_location_history": "...",
  "all_contacts": "..."
}
```

Good:

```json
{
  "context": "SUPERMARKET"
}
```

The database should preserve the minimum information required to perform the domain operation.

---

# 64. Auditability

The database must support answering:

> What happened?

through structured records.

For example:

```text
Event
 ↓
BehaviorDecision
 ↓
Intervention
 ↓
Feedback
 ↓
TrustEvidence
```

The system should be able to reconstruct important behavior without storing hidden model reasoning.

---

# 65. Decision Records

If BehaviorDecision requires persistence, it may be represented through a dedicated table or structured record.

Potential conceptual fields:

```text
behavior_decisions
├── id
├── user_id
├── event_id
├── open_loop_id
├── mode
├── outcome
├── created_at
```

Possible modes:

```text
IGNORE
SILENT_MONITOR
SHORT_INTERVENTION
JUSTIFIED_RECOMMENDATION
```

This table is optional if the final Behavior Engine implementation stores equivalent structured evidence elsewhere.

The decision itself must remain auditable.

---

# 66. Why BehaviorDecision Is Not the Same as Intervention

A BehaviorDecision can produce:

```text
IGNORE
```

or:

```text
SILENT_MONITOR
```

without creating an Intervention.

Therefore:

```text
BehaviorDecision
```

and:

```text
Intervention
```

must not be treated as the same entity.

---

# 67. Silence Persistence

Silence does not require a user-facing record.

However, important silent decisions may be persisted for observability or auditability.

Example:

```text
Event
 ↓
BehaviorDecision
 ↓
SILENT_MONITOR
```

This does not require:

```text
Intervention
```

---

# 68. Database Migrations

All schema changes must be version controlled through migrations.

The database must not be modified manually in production without a corresponding migration.

Conceptually:

```text
Migration 001
Migration 002
Migration 003
...
```

Each migration must be:

- deterministic,
    
- reviewable,
    
- reversible where practical,
    
- compatible with deployment procedures.
    

---

# 69. Migration Rules

A migration must not silently destroy production data.

Destructive operations require explicit review.

Examples requiring special care:

```text
DROP COLUMN
DROP TABLE
DATA TYPE CHANGE
MASS DELETE
```

Large data migrations should be separated from schema changes when necessary.

---

# 70. Seed Data

MVP seed data may be used for:

- development,
    
- testing,
    
- local environments.
    

Production seed data must be explicitly defined.

User-specific production data must never be generated through development seed scripts.

---

# 71. Environment Separation

At minimum:

```text
Development
Test
Production
```

must use separate database environments.

Production data must never be used directly in local development.

---

# 72. Backup and Recovery

Production PostgreSQL must have:

```text
Automated backups
Point-in-time recovery where available
Recovery testing
```

A backup that has never been restored is not considered proven.

Recovery procedures should be documented separately when infrastructure implementation begins.

---

# 73. Database Security

Database access must follow least privilege.

The application should use a dedicated database user.

The application database user should not have unnecessary administrative privileges.

Example separation:

```text
Application User
      ↓
Application Database
```

rather than:

```text
Application
      ↓
PostgreSQL Superuser
```

---

# 74. Secrets

Database credentials must never be committed to:

```text
Git
GitHub
Obsidian
Source Code
```

Credentials must be supplied through secure environment configuration.

---

# 75. Connection Management

The application must use controlled PostgreSQL connections.

Connection pooling should be used where appropriate.

The implementation must prevent:

```text
connection leaks
unbounded connections
long-running abandoned transactions
```

---

# 76. Transaction Boundaries

Transactions should follow domain operations.

Good example:

```text
CompleteLoop
```

may represent one transactional unit.

Bad example:

```text
Entire API request lifetime
```

automatically becoming one huge transaction.

Transactions should be as small as necessary while preserving atomicity.

---

# 77. Database and API Boundary

The API defines:

> How external clients interact with the application.

The Database defines:

> How application state is persisted.

The API must not expose database schema directly.

For example:

```text
Database:
open_loops.goal_id
```

does not require the API to expose:

```json
{
  "goal_id": "..."
}
```

if the API contract instead exposes:

```json
{
  "goal": {}
}
```

---

# 78. Database and Data Model Boundary

The Data Model defines:

> What entities, attributes, relationships, and states exist conceptually.

The Database defines:

> How those concepts are physically persisted in PostgreSQL.

The database must implement the Data Model without unnecessarily leaking implementation details into the domain.

---

# 79. Database and Behavior Engine Boundary

The Behavior Engine determines:

```text
What should happen?
```

The database stores:

```text
What happened?
```

The database must never become the authority for:

```text
Should IDENUM speak?
Should IDENUM remain silent?
Should IDENUM recommend?
```

---

# 80. Database and Trust System Boundary

The Trust System determines:

```text
How much behavioral authority has been earned?
```

The database stores:

```text
Current TrustState
TrustEvidence
```

The database does not calculate Trust by itself.

---

# 81. Database and Learning Boundary

Learning may use historical data.

However:

```text
Historical data
≠
Permanent user preference
```

A single observation must not automatically become a persistent rule.

Any learned state must pass through the Learning and Trust rules defined elsewhere.

---

# 82. Database Query Principles

Queries should:

- select only required columns where practical,
    
- use indexed ownership filters,
    
- avoid unnecessary joins,
    
- paginate large collections,
    
- avoid loading entire histories,
    
- avoid N+1 query patterns,
    
- use transactions where required.
    

---

# 83. N+1 Prevention

The application must avoid patterns such as:

```text
Get 100 loops
 ↓
100 individual Goal queries
```

when the same information can be retrieved efficiently.

The ORM/query layer should use:

```text
joins
batch queries
eager loading
```

where appropriate.

---

# 84. Database Performance Principle

Performance optimization must follow measured bottlenecks.

Do not prematurely optimize the database schema.

Priority:

```text
Correctness
 ↓
Consistency
 ↓
Observability
 ↓
Measured Performance
 ↓
Optimization
```

---

# 85. MVP Database Scope

The MVP database must support:

```text
User
OpenLoop
Goal
Trigger
Event
Intervention
Feedback
TrustState
TrustEvidence
Permission
```

Optional supporting structures may include:

```text
BehaviorDecision
AuditEvent
ModelMetadata
```

only when implementation requires them.

---

# 86. MVP Database Does Not Include

The MVP database does not need dedicated systems for:

```text
Email management
Finance management
Career management
Medical diagnosis
Relationship management
Continuous location history
Advanced recommendation profiles
Deep personality models
Long-term life planning
```

These are outside MVP scope.

---

# 87. Example End-to-End Persistence Flow

User says:

```text
"Marketteyken kahve almayı hatırlat."
```

Application:

```text
Capture
 ↓
Understand
 ↓
Create OpenLoop
 ↓
Create Goal
 ↓
Create Context Trigger
 ↓
Transaction
 ↓
PostgreSQL
```

Database state becomes conceptually:

```text
users
 └── user_123

open_loops
 └── loop_123

goals
 └── goal_123 → loop_123

triggers
 └── trigger_123 → loop_123
```

Later:

```text
Location Event
 ↓
events
 ↓
Behavior Engine
 ↓
BehaviorDecision
 ↓
interventions
```

User responds:

```text
Feedback
 ↓
feedback
 ↓
trust_evidence
```

Loop closes:

```text
open_loops.status = COMPLETED
```

---

# 88. Example Silence Flow

Event:

```text
LOCATION_CONTEXT_DETECTED
```

is persisted.

Behavior Engine determines:

```text
SILENT_MONITOR
```

Database may contain:

```text
events
 └── event_123

behavior_decisions
 └── decision_123
     └── SILENT_MONITOR
```

There is no:

```text
interventions
 └── ...
```

because silence is a valid outcome.

---

# 89. Example Completion Flow

Initial:

```text
open_loops
status = MONITORING
```

User completes:

```text
POST /loops/:id/complete
```

Transaction:

```text
BEGIN
 ↓
Lock OpenLoop
 ↓
Verify ownership
 ↓
Verify state
 ↓
Update status
 ↓
Set completed_at
 ↓
Create evidence if required
 ↓
COMMIT
```

Final:

```text
status = COMPLETED
completed_at = ...
```

---

# 90. Example Invalid Completion

If:

```text
OpenLoop.status = CANCELLED
```

and the user calls:

```text
POST /loops/:id/complete
```

the application must reject the transition.

The database must remain:

```text
CANCELLED
```

No partial completion state may be persisted.

---

# 91. Data Retention

Retention policies must be defined per data category.

Conceptually:

```text
Core domain data
    ↓
Longer retention

Operational events
    ↓
Defined retention

Sensitive contextual data
    ↓
Minimum necessary retention

Temporary processing data
    ↓
Shortest practical retention
```

Retention must not be based solely on storage convenience.

---

# 92. Deletion and User Control

If the product later supports user data deletion, the deletion process must define:

- what is deleted,
    
- what is anonymized,
    
- what must remain for legal reasons,
    
- how related Trust Evidence is handled,
    
- how backups are handled.
    

Deletion behavior must be implemented as a domain-level process.

---

# 93. Testing

Database behavior must be tested at multiple levels.

## Unit / Domain Tests

Test:

```text
State transitions
Ownership rules
Validation
```

## Integration Tests

Test:

```text
API
 ↓
Application
 ↓
PostgreSQL
```

## Transaction Tests

Test:

```text
Commit
Rollback
Concurrency
```

## Migration Tests

Every migration should be tested against a clean database.

---

# 94. Database Acceptance Criteria

The MVP database is considered implementation-ready when:

## Schema

- All MVP domain entities have persistence structures.
    
- Relationships are explicit.
    
- Foreign keys are defined.
    
- Required uniqueness constraints exist.
    
- Timestamps are consistent.
    

## Open Loops

- Open Loops can be persisted.
    
- Lifecycle state can be persisted.
    
- Goals and triggers can be associated with loops.
    
- Completed loops cannot accidentally return to active state.
    

## Events

- Events can be persisted.
    
- Events are user-owned.
    
- Idempotency is supported.
    
- Event payloads can be stored safely.
    

## Interventions

- Interventions can be persisted.
    
- Interventions reference the relevant Open Loop.
    
- Delivery and user response state can be tracked.
    

## Trust

- TrustState can be persisted.
    
- TrustEvidence can be persisted.
    
- Trust cannot be arbitrarily changed through database CRUD.
    

## Permissions

- Permission state can be persisted.
    
- Permission remains separate from Trust.
    

## Security

- User ownership is enforced.
    
- Credentials are protected.
    
- Production access follows least privilege.
    

## Reliability

- Transactions protect critical state transitions.
    
- Backups exist.
    
- Migrations are version controlled.
    
- Recovery is testable.
    

---

# 95. Final Database Principle

The IDENUM database should be understood as:

> **The durable memory of the Cognitive System, not the brain of the Cognitive System.**

The database remembers:

```text
What the user entrusted
What happened
What the system did
How the user responded
What evidence was produced
What state currently exists
```

The database does not decide:

```text
What the user should do
When IDENUM should speak
Whether an intervention is justified
How much trust should increase
What an LLM should infer
```

Those responsibilities remain in the application, Cognitive Engine, Behavior Engine, and Trust System.

The fundamental rule is:

```text
PostgreSQL stores truth.
Domain logic defines truth.
Cognitive Engine interprets context.
Behavior Engine determines action.
Trust System controls behavioral authority.
```


# IDENUM — Architecture Decisions (ADR)

> Bu belge yeni mimari önermez — zaten verilmiş kararları kilitler. Amaç: aynı tartışmayı ikinci kez yapmamak. Yeni bir teknik soru geldiğinde önce buraya bakılır; burada cevap varsa tartışma açılmaz. Format: Karar / Sebep / Tetikleyici (ne olursa yeniden açılır — çoğunda yok, çünkü kapalı).

## Kurallar (kalıcı, Deniz onaylı)

1. **Her ADR bir Status taşır:** `Accepted` / `Superseded by ADR-0XX` / `Deprecated`.
2. **Yayınlanan bir ADR'nin gövdesi değiştirilmez.** Karar değişirse yeni bir ADR numarasıyla yazılır; eski ADR'nin yalnızca `Status` satırı `Superseded by ADR-0XX` olarak güncellenir. Böylece "o gün neden böyle düşündük" kaydı hiç kaybolmaz.

---

### ADR-001 — Backend dili

**Karar:** Node.js + TypeScript. Python değil. **Status:** Accepted **Sebep:** OmniReply'den doğrudan aktarılabilir production deneyimi var; 2026'da TS AI SDK ekosistemi (Anthropic, OpenAI) orkestrasyon için olgun. "AI = Python" varsayımı yalnızca ağır ML/RAG için geçerli, IDENUM'un MVP'si bu değil. **Tetikleyici:** Faz 3'te gerçek embedding/özel model eğitimi gerektiğinde, yalnızca o iş için ayrı bir Python servisi eklenir — backend değişmez.

### ADR-002 — WhatsApp

**Karar:** IDENUM mimarisinde hiçbir yerde yok. Kalıcı. **Status:** Accepted **Sebep:** Business API'nin 24 saat/template kısıtı Silence Engine'in "istediği an konuş" ihtiyacıyla çelişiyordu (sonradan template ile çözülebileceği görüldü ama karar değişmedi); asıl sebep marka/mimari ayrımı — WhatsApp tamamen OmniReply'nin alanı, iki proje karışmayacak (bkz. Technical Plan Madde 3). **Tetikleyici:** Yok.

### ADR-003 — Arayüz (MVP)

**Karar:** PWA. Native app (Flutter/Dart) değil. **Status:** Accepted **Sebep:** App Store/Play Store süreci olmadan hızlı teslimat; Behavior Engine üründür, arayüz değiştirilebilir bir adaptördür (bkz. Technical Plan Madde 3). **Tetikleyici:** Faz 2, gerçek kullanıcı kütlesi oluşunca — iOS/Android/Voice/Watch adaptörleri aynı Behavior Engine'in üzerine eklenir.

### ADR-004 — Proje yapısı

**Karar:** Düz `src/` (behavior/models/routes/services/db/utils), monorepo (`apps/`+`packages/`) değil. **Status:** Accepted **Sebep:** Tek Node süreci, tek deploy — workspace tooling'in kurulum maliyeti bugünkü ihtiyacı aşıyor. **Tetikleyici:** Gerçekten bağımsız deploy edilen ikinci bir süreç (ayrı worker, ayrı frontend) gerektiğinde.

### ADR-005 — Memory'nin konumu

**Karar:** Memory ayrı bir motor/belge değil — Behavior Engine'in bir bölümü (Data Model + Behavior Engine Spec'te tek belge, tek "aşama"). **Status:** Accepted **Sebep:** Memory hiçbir zaman bağımsız çalışmıyor, her zaman pipeline'ın bir adımı. **Tetikleyici:** Yok.

### ADR-006 — State Machine

**Karar:** Yok. Mevcut `status` enum + append-only log tabloları (context_snapshots, silence_evaluations, trust_decisions, interventions) yeterli. **Status:** Accepted **Sebep:** Retry/pause/cancel/override gibi gerçek çok-adımlı durumlar henüz yok; formel state machine kütüphanesi bugün karşılığı olmayan bir soyutlama. **Tetikleyici:** Milestone 3+'ta gerçek çok-adımlı durumlar ortaya çıkarsa.

### ADR-007 — Event Sourcing

**Karar:** Yok. OpenLoop mutable kalıyor, ayrı append-only log tabloları yeterli. **Status:** Accepted **Sebep:** Zaten sahip olduğumuz 4 log tablosu, önerilen `LoopEvents` tablosunun sağlayacağı şeyin çoğunu zaten sağlıyor. **Tetikleyici:** Learning Engine gerçek örüntü tespitine başlayıp mevcut loglar yetersiz kaldığında.

### ADR-008 — Trust kararı: Boolean mu, Score mu?

**Karar:** Boolean (4 soru: hasClearGoal/goalAtRisk/reducesLoad/createsNewLoad). Sürekli confidence score (0.94 gibi) değil. **Status:** Accepted **Sebep:** Kalibre edilecek gerçek kullanıcı verisi sıfır; şimdi sayı üretmek sahte hassasiyet olur. Boolean + `reason` metni tam şeffaf ve test edilebilir. **Tetikleyici:** Faz 1b/2'de gerçek kabul/red/yok sayma verisi birikince.

### ADR-009 — Async Learning

**Karar:** Yok. `close.ts` içinde senkron, inline memory güncellemesi. **Status:** Accepted **Sebep:** Tek satırlık confidence güncellemesi için kuyruk/worker kurmak çözülmemiş bir performans sorununa altyapı yatırımı yapmak olurdu. **Tetikleyici:** Learning Engine gerçekten ağır iş yapmaya (embedding, community-level 2b) başladığında.

### ADR-010 — Universal Learning Memory (topluluk katmanı, "2b")

**Karar:** MVP'de devre dışı. Yalnızca kullanıcı-bazlı öğrenme (2a → Personal Memory/Rutin) aktif. **Status:** Accepted **Sebep:** 3-4 kullanıcıyla anlamlı agregasyon yapılamaz. **Tetikleyici:** Faz 1b/2, gerçek kullanıcı kütlesi oluşunca. O zaman anonimleştirme/agregasyon kuralları Company Constitution'a yazılmalı (Madde 9'daki gizlilik zorunluluğu zaten kayıtlı).

### ADR-011 — `confidence_score` vs `verified_by_user`

**Karar:** İkisi birden var, biri diğerinin yerine geçmiyor. **Status:** Accepted **Sebep:** `confidence_score` sessizce yükselen istatistiksel gözlem; `verified_by_user` olmadan davranışı etkilemez. Research 010'daki "kullanıcı doğrulamadan kalıcı tercih oluşturmaz" kuralı yalnızca gözlemle (score) ihlal edilirdi. **Tetikleyici:** Yok.

### ADR-012 — `life_event_id` / `life_events` tablosu

**Karar:** Şemada var (kolon + minimal tablo), davranışsal mantıkta yok. **Status:** Accepted **Sebep:** Sonradan eklemek pahalı (migration), şimdi eklemek bedava. Milestone 1-5 çoklu open-loop parçalama içermiyor. **Tetikleyici:** Faz 1b/2, bir Life Event'ten birden fazla Open Loop üretme ihtiyacı gerçek hale gelince.

### ADR-013 — `engine_version` (trust_decisions, silence_evaluations)

**Karar:** Var. **Status:** Accepted **Sebep:** Milestone 3'te Silence/Trust mantığı değişecek; versiyon etiketi olmadan geçmiş kararlar geriye dönük yorumlanamaz — telafisi olmayan bir bilgi kaybı. **Tetikleyici:** Yok — zaten eklendi.

### ADR-014 — `source` (context_snapshots)

**Karar:** Var. **Status:** Accepted **Sebep:** Milestone 3'te trafik API'si, Faz 2'de GPS eklenecek; kaynak etiketi olmadan sinyaller karışır. **Tetikleyici:** Yok — zaten eklendi.

### ADR-015 — `template_id` (interventions)

**Karar:** Yok, MVP'de eklenmiyor. **Status:** Accepted **Sebep:** Şu an tek mesaj şablonu var, karşılaştıracak ikinci varyant yok; `life_event_id`'nin aksine sonradan eklemek de ucuz (eski satırlar null kalır, kayıp olmaz). **Tetikleyici:** Faz 1b, birden fazla mesaj varyantını gerçekten A/B test etmek istediğimizde.

### ADR-016 — `engine_runs` (observability tablosu)

**Karar:** Yok. **Status:** Accepted **Sebep:** 3-4 kullanıcıyla console log + mevcut 4 log tablosu yeterli; concurrent yük/performans sorunu yok. **Tetikleyici:** Faz 2, gerçek eşzamanlı kullanıcı hacmi oluşunca.

### ADR-017 — MVP mental load sayısı ve seçim kriteri

**Karar:** 4 (20-30 değil), "en yaygın olanlar" değil "tetikleme mekanizması farklı olanlar" kriteriyle seçildi. **Status:** Accepted **Sebep:** Öncelik kaç yük çözüldüğü değil, 9 motorlu mimarinin çalıştığının kanıtlanması. 20-30'a genişleme Faz 1b'de, yeni mekanizma değil aynı 4 mekanizmanın çoğaltılmış örnekleriyle yapılacak. **Tetikleyici:** Yok — Faz 1b zaten planlı.

### ADR-018 — Milestone yapısı

**Karar:** Her milestone iki parçalı: A) Behavior (happy path) + B) Edge Cases. B olmadan milestone "kanıtlanmış" sayılmaz. **Status:** Accepted **Sebep:** Milestone 1'in yalnızca happy path'i test edilip "bitti" denmesi, gerçek edge case'lerde (duplicate, eksik veri, kapanmış döngü) kırılan varsayımları gizliyordu. **Tetikleyici:** Yok.

### ADR-019 — Belge disiplini

**Karar:** Founder Manifesto ve Company Constitution "neden/kural" seviyesi — nadiren değişir. Technical Plan, Behavior Engine Spec, Data Model "ne/nasıl" seviyesi — küçük düzenlemelerle güncellenir. Yeni büyük belge açılmaz; yeni teknik soru önce bu ADR listesine, sonra ilgili belgeye küçük ekleme olarak işlenir. **Status:** Accepted **Sebep:** Aynı konular (monorepo, dil, WhatsApp, state machine, memory sınırı) 3-4 kez yeniden tartışıldı — bu belge tam olarak bunu durdurmak için var. **Tetikleyici:** Yok.

### ADR-020 — Postgres hosting (VPS)

**Karar:** IDENUM için ayrı bir Postgres instance kurulmaz. VPS'te zaten çalışan (OmniReply için kurulmuş, Docker üzerinde) Postgres sunucusu yeniden kullanılır — `idenum` adında ayrı bir veritabanı + ayrı kullanıcı açılır, `omnireply` veritabanından izole. **Status:** Accepted **Sebep:** Port 5432 zaten `docker-proxy` ile dolu; ikinci bir native kurulum çakışma/kaynak israfı yaratır. **Tetikleyici:** IDENUM gerçek ölçeğe ulaşıp OmniReply'den bağımsız yedekleme/felaket kurtarma stratejisi gerektiğinde, ayrı bir instance'a taşınabilir.

### ADR-021 — Node sürüm izolasyonu (VPS)

**Karar:** VPS'in sistem geneli Node'u (v18.19.1) değiştirilmez. IDENUM için Node 22, nvm ile kurulur ve yalnızca IDENUM'un çalıştırılmasında (PM2 config üzerinden) kullanılır. **Status:** Accepted **Sebep:** VPS'te başka servislerin (OmniReply dahil) sistem Node'una bağımlı olabileceği netleşmeden global değişiklik yapmak, onları bozma riski taşır. **Tetikleyici:** Yok.

### ADR-022 — Push bildirimi mekanizması

**Karar:** Standart Web Push API (VAPID). Firebase Cloud Messaging, OneSignal gibi üçüncü parti/vendor servisleri yok. **Status:** Accepted **Sebep:** Ek bağımlılık/hesap gerektirmiyor, tarayıcı standardı, PWA kararımızla (ADR-003) doğrudan uyumlu. Push gönderimi başarısız olsa bile ana pipeline durmaz (try/catch ile izole edildi) — bu, Silence/Trust Engine'in kararını etkilemez. **Tetikleyici:** iOS Safari'nin Web Push desteği sınırlıysa ve gerçek kullanıcı verisi bunun ciddi bir engel olduğunu gösterirse, native bildirim altyapısı (APNs) o zaman değerlendirilir — bugün değil.

### ADR-023 — Kimlik doğrulamasız `/users` endpoint'i (geçici)

**Karar:** `POST /users`, herhangi bir kimlik doğrulaması olmadan yeni kullanıcı oluşturabiliyor. Bilerek MVP'ye özgü bir kısayol. **Status:** Accepted **Sebep:** Supabase Auth henüz entegre değil (Technical Plan'da planlı ama uygulanmadı); PWA test arayüzünün kendi kendine bir kullanıcı oluşturup push aboneliği kanıtlayabilmesi gerekiyordu. **Risk:** Bu endpoint şu an herkes tarafından spam kullanıcı oluşturmak için kötüye kullanılabilir. Kabul edilebilir çünkü VPS'e yalnızca SSH tüneliyle erişiliyor, genel internete açık bir domain yok. **Tetikleyici:** Domain + SSL kurulup uygulama genel internete açıldığında, bu endpoint ya tamamen kaldırılmalı ya da Supabase Auth ile korunmalı — bu, o ana kadar zorunlu bir ön koşuldur, ertelenebilir değildir.

### ADR-024 — Harita/mesafe servisi: Mapbox

**Karar:** Mapbox Directions API. Google Maps Distance Matrix değil. **Status:** Accepted **Sebep:** Mapbox kredi kartı gerektirmeden token veriyor, ücretsiz kotası (~100.000 istek/ay) MVP hacmimizin çok üstünde. Google, kart bilgisini baştan zorunlu kılıyor ve ücretsiz kotası (5.000 element/ay sonrası ücretli) daha dar. Faturalandırma her zaman API key sahibine gider — hesaplamanın sunucuda mı istemcide mi yapıldığının maliyetle ilgisi yok; bu yüzden çağrı güvenlik gereği sunucu tarafında (backend) yapılır, key tarayıcıya hiç gönderilmez. **Tetikleyici:** Gerçek kullanıcı hacmi Mapbox'ın ücretsiz kotasını zorlamaya başlarsa, o zaman Google Maps (veya Mapbox'ın ücretli katmanı) yeniden değerlendirilir.

### ADR-025 — Konum: capture-anı tek seferlik okuma (mental load #4'ten farklı)

**Karar:** Mental load #2 (toplantı) için, kullanıcının anlık konumu tarayıcı Geolocation API'siyle **yalnızca capture anında, bir kez** okunur. Bu, mental load #4'ün (konum bazlı proaktif tetikleme, Faz 2'ye ertelendi) arka planda sürekli konum izleme ihtiyacıyla **karıştırılmamalı** — farklı bir mekanizma, farklı bir maliyet profili. **Status:** Accepted **Sebep:** Toplantı hatırlatması hesaplanırken kullanıcı her zaman "varsayılan" bir konumda (örn. evde) olmayabilir; anlık okuma daha doğru sonuç verir. Arka plan izleme gerektirmediği için PWA'nın mevcut izin modeliyle (tek seferlik `Geolocation.getCurrentPosition`) çözülür, yeni bir altyapı gerekmez. **Tetikleyici:** Yok.

### ADR-026 — Deploy key yazma izni: varsayılan kapalı

**Karar:** VPS'teki GitHub deploy key varsayılan olarak salt-okunur kalır. Büyük senkronizasyon gerektiğinde geçici olarak yazma iznine çevrilir, iş bitince hemen kapatılır. **Status:** Accepted **Sebep:** Asıl risk SSH şifresinin çalınması değil, VPS'in bir servis açığından (n8n, Postgres, npm paketi vb.) ele geçirilmesi ihtimali — bu durumda yazma izinli bir key, saldırganın GitHub reposuna da kötü amaçlı kod push edebilmesi anlamına gelir. Salt-okunur key, en kötü senaryoda bile kaynak kodun bozulmamasını garanti eder. **Tetikleyici:** Yok — bu kalıcı bir güvenlik pratiği, yeniden tartışılmaz.

---

## Yeni bir teknik soru geldiğinde izlenecek yol

1. Bu listede zaten cevap var mı? Varsa, tartışma açılmaz.
2. Yoksa: Constitution / Technical Plan / Data Model / Behavior Spec ile çelişiyor mu?
    - Çelişmiyorsa → doğrudan kod, gerekirse burada 1 yeni ADR satırı.
    - Çelişiyorsa → ilgili belgeye küçük düzenleme + burada 1 yeni ADR satırı.
3. Yeni belge açılmaz.

---

_Bu belge Founder Manifesto, Company Constitution, MVP Bible, MVP Technical Plan, Behavior Engine Specification ve Data Model ile birlikte okunmalıdır. Repo'da `docs/07-architecture-decisions.md` olarak tutulur._

### ADR-027 — `.env` gitignore'da olmalı (kayıt altına alınan bulgu) 

**Karar:** `.env` dosyası `.gitignore`'a eklendi, hiçbir zaman commit edilmez. **Status:** Accepted **Sebep:** VPS'te `.env` uzun süre untracked (gitignore dışı) durmuş; bu, bir `git add -A` sırasında `DATABASE_URL`, `VAPID_PRIVATE_KEY`, `MAPBOX_TOKEN`, `INVITE_CODE` gibi sırların yanlışlıkla private repoya commit edilme riskini taşıyordu. Fark edilip düzeltildi, hiçbir sır fiilen commit edilmedi. **Tetikleyici:** Yok.


### ADR-028 — Sesli etkileşim modeli: dokun-konuş 

**Karar:** Ses girişi push-to-talk (dokun-konuş) ile çalışır — kullanıcı orb'a dokunup konuşur, sistem tek bir cümle işler. Sürekli dinleme (Siri/Alexa tarzı "her zaman açık mikrofon") Faz 2'ye ertelendi, MVP'de yok. **Status:** Accepted **Sebep:** Sürekli dinleme; sürekli mikrofon izni, arka plan ses işleme, yanlışlıkla tetiklenme riski ve gizlilik algısı açısından MVP'nin kapsamını fazlasıyla aşan bir altyapı yatırımı gerektirir. Dokun-konuş, mevcut Web Speech API (`SpeechRecognition`) ile sıfır ek altyapıyla çalışır ve kullanıcıya net bir "şimdi dinliyor" sınırı çizer — Constitution Madde 8'deki sessizlik ilkesiyle de tutarlı (sistem yalnızca çağrıldığında konuşmaya başlar). **Tetikleyici:** Faz 2, gerçek kullanıcı geri bildirimi dokun-konuş akışının sürtünme yarattığını gösterirse yeniden değerlendirilir. ### ADR-029 — Mental load #1: saat belirtilmezse varsayılan sabah 10:00 **Karar:** Sesli "X'i ara yarın" komutunda saat belirtilmemişse, hatırlatma sabit olarak ertesi gün sabah 10:00'a ayarlanır. Önceki değer (öğleden sonra 14:00, sunucu saat dilimi hatasıyla birlikte kullanıcıya 17:00 olarak görünüyordu) bu ADR ile değiştirildi. **Status:** Accepted **Sebep:** Bu bir hatırlatma, kesin randevu değil — ama sabit bir varsayılan saat her zaman "kişiye çok geç kalındı" veya "artık uygun değil" riski taşır. Sabah varsayımı, gün sonu varsayımına göre bu riski büyük ölçüde azaltır (kullanıcının önünde tüm gün kalır). Gerçek netleştirme akışı (kullanıcıya "saat kaçta?" sorup cevabı aynı loop'a bağlama) bilinçli olarak MVP dışı bırakıldı — bu, oturum/konuşma hafızası gerektirir ve ADR-006'nın (State Machine yok) kapsamını aşar. **Tetikleyici:** Gerçek netleştirme akışı gerektiğinde (kullanıcı cevabını önceki soruya bağlama), o zaman State Machine kararı (ADR-006) yeniden açılır — bu, hafif bir düzeltme değil, yeni bir yetenek olur. 


### ADR-030 — Sunucu saat dilimi: Europe/Istanbul 


**Karar:** PM2 process'ine `TZ: Europe/Istanbul` environment variable'ı eklendi (`ecosystem.config.cjs`). Sunucu artık UTC yerine Türkiye saatiyle çalışıyor. **Status:** Accepted **Sebep:** VPS varsayılan olarak `Etc/UTC`; `new Date()`/`setHours()` çağrıları sunucu yerel saatini kullandığı için, kullanıcıya gösterilen tüm saatler 3 saat kayıyordu (örn. kod 14:00 atarken kullanıcı 17:00 görüyordu). Türkiye 2016'dan beri yaz saati uygulamıyor (yıl boyu sabit UTC+3), bu yüzden `Europe/Istanbul` DST karmaşası yaratmadan kalıcı bir çözüm. Merkezi, tek satırlık bir düzeltme — her dosyada ayrı ayrı timezone dönüşümü yapmaktan (ADR-004'ün "gereksiz karmaşıklık ekleme" ruhuna aykırı olurdu) daha doğru. **Tetikleyici:** Yok.


# Claude Engineering Knowledge — IDENUM

> Intended path: `04 engineering/06 Claude Engineering Knowledge.md`
> Reconstructed from a single, continuous engineering conversation (Milestones 1–3, domain/SSL, UI redesign). Nothing here is newly designed — this is a reconstruction of what was actually decided, built, and tested, plus what was discussed but never finished. Status tags: **DECIDED** (implemented and/or explicitly locked), **PROPOSED** (discussed, agreed in principle, not yet built), **ASSUMED** (a default taken without explicit debate), **UNRESOLVED** (open question or known gap).

---

## 1. Architecture

- **DECIDED.** IDENUM is not an app — it is a "Digital Life Layer" / Behavior Engine; the app (PWA) is the first interface, replaceable. Source: Founder Manifesto, Company Constitution Madde 1, Technical Plan Madde 3.
- **DECIDED.** Backend: Node.js + TypeScript, not Python (ADR-001). Rationale given: TS AI-SDK ecosystem is mature enough for orchestration-style work; Python reserved for future heavy ML/embedding work only.
- **DECIDED.** Single Node process, flat `src/` folder structure, no monorepo/workspaces (ADR-004).
- **DECIDED.** PostgreSQL for persistence.
- **DECIDED.** PWA as presentation layer, explicitly framed as a swappable adapter, not the product (ADR-003; reframed in Technical Plan as "Presentation Layer, current: PWA, replaceable").
- **DECIDED / implemented.** Live at `https://idenum.omnireply.io`, Nginx reverse proxy + Let's Encrypt (Certbot), auto-renewing cert.
- **DECIDED / implemented.** Runs on a VPS shared with an unrelated project (OmniReply). System Node (v18) never touched; IDENUM uses an nvm-isolated Node 22 (ADR-021). Postgres reused from an already-running Docker container (shared server, separate database `idenum`, separate user) rather than a second Postgres install (ADR-020).
- **DECIDED.** Process manager: PM2 (not Docker), pinned to the nvm Node 22 binary via `ecosystem.config.cjs` interpreter path (hardening added after a boot-persistence bug: PM2 defaulted to system Node otherwise).

---

## 2. Backend architecture

- **DECIDED.** Folder layout:
  - `src/behavior/` — one file per pipeline stage (capture, memory, context, priority, silence, trust, intervention, close), each strictly single-responsibility (ADR-005 spirit; enforced by convention, not tooling).
  - `src/models/` — TypeScript types, 1:1 with Data Model tables.
  - `src/db/store.ts` — the only file that touches Postgres directly; all other code calls `store.*` methods.
  - `src/services/` — `behavior-engine.ts` (orchestrator), `push.ts` (Web Push sending), `traffic.ts` (Mapbox calls).
  - `src/routes/` — `capture.ts`, `push.ts`, `users.ts`.
  - `public/` — PWA static files (index.html, manifest.json, sw.js, icons).
  - `db/migrations/` — versioned raw SQL, applied manually (no migration framework/ORM).
- **DECIDED.** No ORM. Raw `pg` (node-postgres) queries throughout `store.ts`.
- **ASSUMED.** No test framework/runner beyond hand-written scripts using Node's built-in `assert`. Each Behavior Milestone has its own script(s) (`milestone1.ts`, `milestone1-edge-cases.ts`, `milestone2.ts`, `milestone3.ts`) run manually via `npx tsx`. This was treated as sufficient for MVP scale but never explicitly compared against adopting a real test runner (jest/vitest) — no one raised it, so it's an assumption of continuity rather than a debated decision.

---

## 3. "Cognitive Engine"

- **DECIDED (naming evolution, worth flagging).** In the earlier research phase (Atlas, Research 011), the term used was **Cognitive Engine** ("the real product is the Cognitive Engine, the app is its first interface"). In the operational documents (Technical Plan, Behavior Engine Spec, Constitution Madde 10), this became **Behavior Engine**. Same concept, refined/renamed. If the repo or any teammate still says "Cognitive Engine," it means the same thing as "Behavior Engine" — do not treat as a separate system.
- **DECIDED.** 9 component engines make up the Behavior Engine: Capture, Memory, Context, Priority, Decision Support, Silence, Trust, Intervention, Learning (Constitution Madde 10).
- **UNRESOLVED / gap.** "Decision Support Engine" and "Learning Engine" are named in Constitution Madde 10 but have **no dedicated file** in the code. Decision Support's job (generate alternatives, compute risk) is effectively absorbed into `silence.ts` + `trust.ts`. Learning Engine's job is effectively absorbed into `close.ts` (writes `confidence_score`/`personal_memory`). This was never explicitly flagged as a deviation from the 9-engine model at the time — worth checking whether this matters or is fine as pragmatic MVP consolidation.

---

## 4. Behavior Engine (pipeline)

- **DECIDED.** Canonical order (Constitution Madde 10): `Capture → Memory → Context → Priority → Silence Check → Trust → Intervention → Action`.
- **DECIDED.** A shorter notation was also used in conversation (`Capture → Memory → Context → Priority → Trust Check → Decision → Silence/Reminder/Recommendation → Kapanış`) — this was explicitly reconciled as **not a contradiction**, just a compressed description of the same flow (Silence Engine still runs before/into Trust; "Silence/Reminder/Recommendation" describes the three possible **outputs**, not a separate stage).
- **DECIDED / implemented.** Code has two entry points sharing one internal pipeline: `runPipeline()` (mental load #1, starts with `capture()`) and `runMeetingPipeline()` (mental load #2, starts with `captureMeeting()`), both delegating to a shared `runFromCapture()` for everything after Capture (dedup check → Memory → Context → Priority → Silence → Trust → Intervention → push send).
- **DECIDED.** Duplicate detection happens in the **orchestrator**, not inside Capture (Capture must not know about persistence/other loops — single-responsibility). Rule differs by mental load type:
  - `zaman_bazli`: same user + same `kisi` + same `eylem` + same **day**.
  - `hesaplanmis`: same user + same destination coordinates + same **exact minute** (not same day — multiple meetings per day are normal). This distinction was a **bug found and fixed mid-conversation** — the original duplicate query only checked `kisi`/`eylem`, which are `undefined` for meetings, so meeting duplicates silently never matched (`NULL = NULL` in SQL is never true). Fixed by branching the query on `mental_load_type`.

---

## 5. Memory

- **DECIDED.** Two separate systems, not a conflict once clarified (Atlas Bölüm 6.3):
  - **Personal Memory** — 4 layers (Geçici / Rutin / Tercih / Kimlik, Research 007), per-user, isolated, one Postgres table (`personal_memory`) with a `layer` enum rather than 4 physical tables.
  - **Universal Learning Memory** — Kurulum + Zamanla Öğrenilen. "Zamanla Öğrenilen" further splits into **2a** (user-specific pattern, feeds Personal Memory's Rutin layer) and **2b** (community-aggregate pattern, e.g. "80% of users accepted this suggestion type"). **2b is explicitly disabled for MVP** (not enough users for meaningful aggregation) — no table exists for it yet.
- **DECIDED.** `confidence_score` (silent, observation-driven) and `verified_by_user` (gates whether the entry actually changes system behavior) **coexist** — one does not replace the other (ADR-011). This was a specific fix to avoid violating the Learning Rule "no permanent preference without user confirmation" while still allowing passive pattern tracking.
- **UNRESOLVED / known gap, explicitly flagged in code comments.** `silence.ts` uses a hardcoded `DEFAULT_PREP_MINUTES = 15` constant. The Behavior Engine Spec's own walkthrough describes this value as something that should come from Personal Memory's Tercih layer ("hazırlanma süresi" preference) — **this lookup was never wired**. The `users.default_prep_minutes` column also exists in the schema and is similarly unused by the running code.
- **UNRESOLVED / scope gap.** `lookupMemory()` only implements a real lookup for `mental_load_type === 'zaman_bazli'` (keyed on `kisi:{name}`). For every other mental load type it safely returns `undefined` rather than throwing — this was a real bug fix mid-conversation (originally crashed on `undefined.toLowerCase()` for meetings), but the fix is "don't crash," not "implement memory lookup for meetings." No memory strategy for mental load #2/#3/#4 has been designed.

---

## 6. Context

- **DECIDED.** `computeContext()` branches on `mentalLoadType`:
  - `zaman_bazli`: single signal, system clock (`source: 'system'`).
  - `hesaplanmis`: real Mapbox Directions API call (`source: 'traffic'`) if `origin` present; if `origin` is `null`, returns `source: 'manual'` with `travelMinutes: null` and an explicit reason string — **never guesses** (Behavior Engine Spec Madde 4's "no assumption" rule).
- **DECIDED.** `origin` (user location) is captured via the browser Geolocation API **once, at capture time only** — explicitly not continuous background tracking (ADR-025). This is deliberately distinguished from mental load #4's (deferred) continuous-location trigger concept — different mechanism, different cost profile, do not conflate.
- **DECIDED.** Destination is always structured input (`{lat, lng}`), never parsed from free text/voice (ADR-024's companion decision) — no geocoding/NLU for addresses exists or was planned for MVP.
- **DECIDED / implemented.** `context_snapshots.source` enum: `system | calendar | gps | manual | traffic | weather`. Only `system`, `manual`, and `traffic` are actually produced by code today; `calendar`/`gps`/`weather` are reserved placeholders (**ASSUMED** future use, not designed).
- **UNRESOLVED / significant known gap.** Context is computed **once, synchronously, at Capture time**. There is no scheduled/background re-evaluation. A meeting captured 3 hours in advance with low risk will **never be re-checked** as the deadline approaches unless the user re-submits. This was explicitly identified and documented (Technical Plan, "Bilinen sınır (Milestone 2'de keşfedildi)") as deferred to a future n8n-based scheduled loop — **not built**.

---

## 7. Priority

- **DECIDED / implemented.** `computePriority(loop, activeLoops)`: if the user has ≤1 active loop, priority = 1.0. Otherwise, compares `targetTime` distance among all active loops; whichever is soonest gets 1.0, others get 0.5. Ties (identical `targetTime`, which happens often since mental load #1 always defaults to "tomorrow 14:00") **all** get 1.0 — tested and confirmed as intended behavior (Milestone 2, test B4), not a bug.
- **UNRESOLVED / documented known gap.** Priority is computed **once, at capture time, for the new loop only**. Existing loops' `priority_score` values are **not retroactively recalculated** when a new, more urgent loop arrives. Flagged explicitly in Technical Plan as deferred to Milestone 3+/whenever scheduled re-evaluation exists.
- **ASSUMED.** The simplicity of this algorithm (no weighting by risk, no user-preference factor) was treated as intentionally minimal for MVP, not as an oversight — but this was never stress-tested against a scenario with >2 simultaneous loops of genuinely different urgency levels combined with genuinely different target times (Milestone 2's tests used identical target times throughout, since that's all mental load #1 can produce).

---

## 8. Trust Check / Trust System

- **DECIDED.** 4-question "Karar Motoru" (Research 009): `hasClearGoal`, `goalAtRisk`, `reducesLoad`, `createsNewLoad` — booleans, not a continuous score. A proposal to use a 0–1 confidence score (like 0.94) was **explicitly rejected** (ADR-008): no real calibration data exists yet to justify numeric precision; booleans + a `reason` string are more honest and debuggable at this stage. Trigger to revisit: once real accept/dismiss data accumulates.
- **DECIDED.** `goalAtRisk = riskLevel === 'yuksek' || riskLevel === 'bilinmiyor'` — "unknown" risk is treated as risk (precautionary principle, Behavior Engine Spec Madde 4).
- **DECIDED.** But the two risk levels map to **different** decisions, not the same one: `yuksek` → `decision: 'intervention'` (hard nudge, e.g. "you'll be late if you don't leave now"); `bilinmiyor` → `decision: 'reminder'` (soft nudge only, e.g. "your meeting is coming up") — deliberately avoids sounding confident about a risk the system hasn't actually confirmed.
- **PARTIALLY IMPLEMENTED.** "Trust Violation" is a named concept from Research 009 (unnecessary talking, repetition, wrong timing, taking away the decision, false confidence, forgetting stated preferences) but there is **no dedicated tracking table**. The one instrumented case is `interventions.user_response = 'gereksizdi'`, which can be queried for a "was this reminder unwanted" KPI — the other five violation types are **not instrumented at all**.
- **DECIDED / implemented.** `engine_version` field exists on both `trust_decisions` and `silence_evaluations` (ADR-013), specifically to let future analysis distinguish "old algorithm's decision" from "new algorithm's decision." Currently: `TRUST_ENGINE_VERSION = 'v1'`; `SILENCE_ENGINE_VERSION` was bumped from `'v1'` to `'v2'` when mental load #2 logic was added (the version was bumped precisely because the logic genuinely changed, not routinely).

---

## 9. Intervention system

- **DECIDED.** 4-level behavior hierarchy (Research 003): sessiz kal (silent) → hatırlat (reminder) → öner (suggestion) → müdahale et (intervention). No 5th level; user always keeps the final decision.
- **UNRESOLVED / gap.** The `Decision` type includes `'suggestion'` as a valid value, but **no code path currently produces it** — `trust.ts`'s `decide()` function only ever returns `'silence'`, `'reminder'`, or `'intervention'`. The type system supports "öner" but the decision logic has never been written to use it.
- **DECIDED.** `sendIntervention()` builds different message text per `mentalLoadType` (a branch inside `intervention.ts`), e.g. `"{kişi}'i aramayı hatırlatıyorum."` vs `"Şimdi çıkmazsan toplantına geç kalacaksın."` vs (for unknown-risk meetings) `"Toplantın yaklaşıyor, hazırlanmaya başlamak isteyebilirsin."`.
- **DECIDED / implemented.** Delivery: standard Web Push API with VAPID keys, generated via `npx web-push generate-vapid-keys`, no vendor service (Firebase/OneSignal) — ADR-022. Confirmed working end-to-end on a real phone over the real domain.
- **DECIDED.** Push send failures are caught and logged; they never break the `/capture` response — verified via a deliberately-fake subscription test that produced a caught error while `/capture` still returned `kind: 'ok'`.
- **ASSUMED / placeholder.** `delivery_channel` enum includes `ios | android | voice | watch` alongside the only-implemented `push_pwa` — these are reserved for a native-app future, not designed.
- **PROPOSED, NOT IMPLEMENTED.** The idea of a "start navigation?" action button on high-risk meeting interventions (deep-linking to Google/Apple Maps, user taps to confirm rather than the system auto-starting navigation) was discussed and agreed as philosophically correct (matches Constitution's "user keeps agency" principle) immediately before the UI redesign work began. **No code for this exists** — verify against repo; if missing, it was never built, not silently dropped.

---

## 10. Behavior modes

Same as §9's 4-level hierarchy. No additional modes were discussed. **DECIDED.**

---

## 11. State machines

- **DECIDED (explicit rejection).** No formal state machine library/pattern for MVP (ADR-006). The existing `open_loops.status` enum (`candidate | active | completed | dismissed | expired`) plus the four append-only log tables (`context_snapshots`, `silence_evaluations`, `trust_decisions`, `interventions`) were judged sufficient to reconstruct "what happened and when" without adopting a state-machine abstraction. Stated trigger to revisit: if genuine multi-step behaviors (retry/pause/cancel/override) emerge that the simple status enum can't cleanly express.

---

## 12. Open Loop lifecycle

- **DECIDED.** Terminal states: `completed` (user confirmed "tamam"), `dismissed` (other negative responses), `expired` (user response `yok_say`). Non-terminal: `candidate`.
- **UNRESOLVED / needs repo verification.** `active` exists in the `LoopStatus` type but no clear code path was ever discussed that transitions a loop from `candidate` to `active` distinctly — it's unclear whether this status is actually used anywhere or is a defined-but-dormant value. **Check the repo directly for any `status: 'active'` assignment.**
- **DECIDED.** Once closed, an open loop cannot be responded to again (`respondToLoop` throws if status is terminal) — tested explicitly (Milestone 1, Test 5).
- **DECIDED.** Closing a `zaman_bazli` loop with `'tamam'` writes/updates a Personal Memory entry in the `kimlik` layer (person name, occurrence count, confidence score bump). No equivalent memory-write exists for other mental load types on close.

---

## 13–16. Data Model, Entities, Entity Relationships

- **DECIDED / implemented, verified against a real running Postgres instance.** 9 tables total:
  `users`, `personal_memory`, `life_events`, `open_loops`, `context_snapshots`, `silence_evaluations`, `trust_decisions`, `interventions`, `push_subscriptions`.
- **DECIDED.** `life_events` exists in schema (id, user_id, type enum, status, created_at) and `open_loops.life_event_id` is a nullable FK to it — but **this is schema-only, never used behaviorally** (ADR-012: "cheap to add now, expensive to retrofit later" reasoning; no code currently creates a life_event or links a loop to one). If the repo shows any multi-loop-from-one-event logic, that would be new work done after this conversation, not something already designed.
- **DECIDED.** Relationships: `users (1)—(N) personal_memory / open_loops / push_subscriptions / life_events`; `open_loops (1)—(N) context_snapshots / silence_evaluations / trust_decisions / interventions`; `life_events (1)—(N) open_loops` (nullable, unused in practice).
- **DECIDED.** No ORM — raw SQL migrations, applied manually via `psql -f`, tracked as numbered files in `db/migrations/` (`001_initial.sql`, `002_indexes.sql`, `003_push_subscriptions.sql`). No migration-history table exists (no `schema_migrations` tracking which files have run) — **ASSUMED** the operator (Deniz) manually remembers/tracks which migrations have been applied on which environment; this was never formalized.

---

## 17. API design

- **DECIDED.** No formal API specification document — explicitly rejected as premature for a solo founder ("Google değiliz"). The Data Model doc contains a short endpoint list instead of a contract document.
- **DECIDED / implemented endpoints:**
  - `POST /capture` — mental load #1
  - `POST /capture/meeting` — mental load #2
  - `GET /loops?userId=` — active loops for a user
  - `POST /feedback` — respond to (close) a loop
  - `POST /users` — creates a user, **requires** `inviteCode` matching `process.env.INVITE_CODE` (ADR-023)
  - `GET /push/vapid-public-key`, `POST /push/subscribe`
  - `GET /health`
- **UNRESOLVED.** No endpoints exist yet for mental load #3 (recurring) or #4 (location-triggered) — these mental loads have no Capture logic written at all, not just no endpoint.

---

## 18–19. Database design, Persistence rules

- Covered in §13–16. Additional note: **DECIDED**, `store.ts` is the single point of Postgres access; nothing else in the codebase imports `pg` directly. All row↔model conversion (snake_case ↔ camelCase) happens in `store.ts`'s row-mapper functions.
- **DECIDED.** `_resetForTests(userId)` exists in `store.ts` specifically for test scripts to clean up — deletes across all loop-related tables for a given user. Not exposed via any API route (internal-only, imported directly by test scripts).

---

## 20. Event model

- **DECIDED (explicit rejection).** No event-sourcing, no event bus (ADR-007). The four append-only log tables act as a de facto audit trail but the system does not reconstruct current state by replaying events — `open_loops` rows are mutated directly (status, priority_score, closed_at, closed_reason updated in place). Stated trigger to revisit: if Learning Engine work genuinely outgrows what the four log tables can support.

---

## 21. Trigger model

- **UNRESOLVED / major known gap, discussed repeatedly.** The entire system today is **request-triggered only** — a pipeline run happens exactly once, synchronously, when `/capture` or `/capture/meeting` is called. There is **no scheduler, no cron, no background re-evaluation loop**. The plan discussed (but not built) was to use n8n (already running on the shared VPS) to periodically poll pending open loops and re-run Context/Silence/Trust for time-sensitive ones as their deadlines approach. This is the single largest architectural piece that was **designed-in-conversation but never implemented**.

---

## 22. Permission model

- **UNRESOLVED / explicitly flagged as insufficient.** No real authentication system exists. `POST /users` is protected only by a single shared `INVITE_CODE` string compared in plaintext (ADR-023) — anyone with the code can create unlimited users; there is no per-user login, password, or session token. ADR-023 itself states this must be resolved ("ya tamamen kaldırılmalı ya da Supabase Auth ile korunmalı — ertelenemez") once the app is on a public domain with SSL. **The domain+SSL trigger condition has since been met, but the actual resolution (real auth) has not been implemented — only the invite-code stopgap exists.** This is a live, acknowledged gap, not an oversight.
- **ASSUMED.** User identity persists client-side only, via a UUID stored in `localStorage` (`idenum_user_id`). No server-side session. Clearing browser data or switching devices loses identity entirely — accepted as tolerable for a small, known-volunteer 7-day test, not evaluated for anything beyond that.
- **DECIDED.** Supabase Auth was named in the original Technical Plan as the intended real auth solution but **was never implemented** at any point in this conversation.

---

## 23–24. AI/LLM responsibilities vs deterministic

- **DECIDED / important, easy to miss.** Despite ADR-001's rationale partly resting on "TypeScript's AI SDK ecosystem is mature," the MVP's actual Capture Engine (`capture.ts`) uses **zero LLM calls** — it is 100% regex/rule-based text parsing (e.g. `/yar[ıi]n\s+(.+?)('i|'ı|'yi|'yı)?\s+ara/i`). `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` exist as `.env` placeholders explicitly commented "not yet used — will be needed when Capture moves to LLM-based extraction." **No LLM is in the running system anywhere.** This was a deliberate MVP scope decision (deterministic parsing is simpler, cheaper, and fully testable), not an accident — but worth stating plainly since the name "Cognitive Engine" and the AI-SDK-ecosystem rationale in ADR-001 could mislead someone into assuming LLM usage exists today.

---

## 25. Security considerations

- **DECIDED.** GitHub deploy key on the VPS is read-only by default; briefly switched to write access only for large syncs, then reverted each time (ADR-026) — treated as a permanent operating discipline, not a one-off.
- **DECIDED.** `.env` is git-ignored; confirmed multiple times across the conversation that it never entered the repo.
- **Correction worth preserving.** Mapbox was initially claimed (by Claude) to require **no credit card** for its free tier. In practice, Deniz was asked for a card during signup. This was explicitly corrected mid-conversation — the decision to use Mapbox over Google Maps stood anyway (Mapbox's free-tier math is still ~5–6× more favorable), but the "no card" justification in earlier reasoning is **inaccurate** and should not be trusted if it resurfaces in older notes.
- **DECIDED / near-miss, no actual leak.** A stray local text file on the VPS (intended to hold the Postgres password) was discovered to be 0 bytes — the real password lives correctly in `.env`, but the separate manual note was apparently never actually saved. Flagged as a reminder to avoid keeping credential copies outside `.env`.
- **ASSUMED.** Shared Postgres container with OmniReply (separate DB/user, same server process) was accepted as adequate isolation for MVP; not evaluated against a fully separate instance beyond the stated ADR-020 trigger (real scale + need for independent backup/DR strategy).

---

## 26. MVP engineering scope

- **DECIDED.** 4 mental loads selected specifically to cover 4 **different trigger mechanisms**, not by frequency/popularity (ADR-017):
  1. Zaman bazlı (time-based, "yarın X'i ara") — **built, tested (Milestone 1)**
  2. Hesaplanmış (calculated/meeting, real travel-time risk) — **built, tested (Milestone 3)**
  3. Tekrarlayan (recurring, e.g. bills/meds) — **not built**
  4. Konum bazlı (proximity-triggered) — **explicitly deferred to Faz 2**, not part of MVP at all
- **DECIDED.** Expansion plan (MVP Bible): Faz 1a (4 mechanisms, few users) → Faz 1b (same 4 mechanisms, 20–30 varied examples, not new mechanisms) → Faz 2 (native app, background services, mental load #4) → Faz 3 (Python/ML/embeddings, real personalization).
- **DECIDED (locked to Faz 2, ADR discussion in progress — see §30).** Always-listening voice ("Siri-style") was proposed and explicitly deferred; MVP uses tap-to-talk only, single-shot capture per tap.

---

## 27. ADR decisions (index)

The authoritative list lives in `IDENUM_Architecture_Decisions.md` (27 confirmed entries as of this document, ADR-001 through ADR-027, covering: backend language, WhatsApp exclusion, PWA choice, project structure, Memory-not-separate-engine, State Machine rejection, Event Sourcing rejection, Trust boolean-not-score, Async Learning rejection, Universal Memory 2b deferral, confidence_score+verified_by_user coexistence, life_event_id schema-only, engine_version fields, source field, template_id rejection, engine_runs rejection, mental load selection criteria, Milestone A/B structure, documentation discipline, Postgres hosting reuse, Node version isolation, Web Push mechanism, capture-time-only geolocation, deploy-key-read-only default, domain/SSL — see §30 for the one that failed to save).

---

## 28. Technical constraints

- Solo founder, no dedicated ops/security team — every infra decision explicitly weighed against "how much ongoing burden does this add for one person."
- Shared VPS with an unrelated, already-running project (OmniReply) constrained several choices (couldn't freely change system Node; had to work around existing n8n/Postgres/Nginx rather than assuming a clean box).
- Desktop browser geolocation is IP/WiFi-based and inaccurate (no GPS chip) — confirmed as a testing artifact during Milestone 3 (a desktop test returned a location ~600km from the user's actual stated location), not a code defect. Real phones expected to be accurate; not yet confirmed at the time of this document's writing whether phone-based geolocation was specifically re-verified after the fact.
- Web Speech API (both `SpeechRecognition` and `SpeechSynthesis`) is Chromium-biased; Safari/iOS support was never verified. This matters because the MVP target device is a phone, and iOS Safari is a real possible volunteer device — **UNRESOLVED**, not tested.

---

## 29. Implementation assumptions

- `DEFAULT_PREP_MINUTES = 15` hardcoded, not read from any user preference.
- `VAPID_SUBJECT` is a placeholder email (`mailto:contact@example.com` or similar) — likely needs replacing with a real contact address before wider real-world use.
- Primary target browser/OS for the volunteer test assumed to be Android/Chrome, based on what was actually used during development testing — not a stated requirement, just the de facto testing environment.
- The single shared `INVITE_CODE` is assumed sufficient to gate a small (2–4 person) known-volunteer test; not assumed sufficient beyond that.

---

## 30. Known unresolved engineering questions

1. **Scheduled/background re-evaluation does not exist.** No cron/n8n loop re-checks pending loops as time passes. This is the largest structural gap — everything currently only evaluates at the moment of capture.
2. **Real authentication was never built.** Invite-code gate is a stopgap; ADR-023's own stated trigger for requiring real auth has technically been met (public domain + SSL live) but the fix hasn't shipped.
3. **`'suggestion'` decision level is defined in the type system but never produced by any code path.**
4. **Personal Memory preference lookups (e.g. prep-time) are not wired** — `silence.ts` uses a hardcoded constant instead.
5. **`ADR-028` ("tap-to-talk, not always-listening, deferred to Faz 2") was verbally agreed but a tool error prevented it from being saved into `IDENUM_Architecture_Decisions.md`.** If the ADR file in the repo/vault does not contain an ADR-028, it needs to be added — the decision itself is real and was agreed, only the write failed.
6. **`open_loops.status = 'active'`** — unclear if/where this value is ever actually assigned; needs direct repo check.
7. **`life_events` table and `open_loops.life_event_id`** exist in schema but are unused by any code path — confirm this is still true in the current repo before assuming otherwise.
8. **The "start navigation?" action-button idea for high-risk meeting interventions was agreed in principle but no code for it was written** (to the best of this reconstruction) — needs a repo check to confirm it's genuinely absent and not something built afterward.
9. **iOS Safari support for the voice features (`SpeechRecognition`/`SpeechSynthesis`) was never tested.**
10. **No migration-tracking mechanism** — nothing records which of the numbered SQL files in `db/migrations/` have actually been applied to a given environment; this is manual/tribal knowledge only.
11. **Mental loads #3 (recurring) and #4 (location-triggered, deferred) have no Capture logic, no endpoints, and no schema-level implementation beyond the `mental_load_type` enum value existing.**
12. **The UI redesign (orb-based home screen, swipeable "Takip ettiklerim"/"Ayarlar" screens, voice capture, planned TTS error feedback) was built and deployed directly to the VPS in the final stretch of the source conversation but — per the handoff — had not yet been synced back to the Git repository.** Confirm current repo state against what's actually running on the VPS before assuming they match.

---

## A. Canonical engineering decisions (short list)

- Node.js + TypeScript, PostgreSQL, PWA, no monorepo, no ORM, no event sourcing, no state machine library, no LLM in MVP Capture, Web Push (not vendor push), Mapbox (not Google Maps), invite-code gate (not real auth, acknowledged as temporary), 4-mental-load MVP scope chosen for mechanism diversity, Behavior Milestones require both a happy-path proof and an automated edge-case proof before being considered done.

## B. Decisions I believe are still unresolved

- Real authentication (§22, §30.2)
- Scheduled re-evaluation / trigger model (§21, §30.1)
- Whether `'suggestion'` decision level will ever be used, or should be removed from the type (§9, §30.3)
- Whether Personal Memory preference lookups will be wired before or after Milestone 4/5 (§5, §30.4)
- Whether ADR-028 is actually present in the current ADR document (§30.5)

## C. Engineering assumptions (things nobody explicitly decided, but the system currently behaves as if they were decided)

- Android/Chrome is the practical target for the volunteer test.
- A single shared invite code is enough for a 2–4 person test.
- Migration application order/state is tracked manually by the operator, not by tooling.
- `life_events` will eventually be used for multi-loop decomposition, but not before real usage data justifies it.

## D. Things that must be checked against the current repository

- Does `IDENUM_Architecture_Decisions.md` actually contain ADR-028? (It was agreed but a save failed.)
- Is the orb-based UI redesign (with or without the TTS error-feedback addition) present in `public/index.html` in Git, or only ever deployed directly to the VPS?
- Is `open_loops.status = 'active'` assigned anywhere in code?
- Does any code reference/create `life_events` rows?
- Does any code implement the "start navigation" deep-link button on meeting interventions?
- Are Milestone 4 and Milestone 5 present at all (any `milestone4.ts`/`milestone5.ts`, any recurring-load capture logic)?

## E. Things the current repository may be missing (based on what was designed but, per this reconstruction, never built)

- A scheduler/cron mechanism (n8n-based or otherwise) for re-evaluating pending loops over time.
- A real authentication system (Supabase Auth or equivalent) replacing the invite-code stopgap.
- Personal Memory–backed preference lookups (prep time, and any other per-user Tercih-layer values referenced conceptually but not wired).
- Capture logic, context/silence handling, and tests for mental load #3 (recurring loads) — Milestone 4.
- A combined test proving Priority + Trust work correctly with all three built mental load types active simultaneously — Milestone 5.
- Any code implementing the "öner" (suggestion) decision level.
- A "start navigation" action on high-risk meeting push notifications.
- A migration-tracking table or tool.