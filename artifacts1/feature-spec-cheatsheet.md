Feature Specification Cheat Sheet

User story = the wish. Feature spec = the answer a teammate can build from.


1)The six questions

 Who triggers this feature? Name the role.
 What does it produce? Name observable outputs only.
 When can it run? List preconditions and time constraints.
 Where does it read state from? Name every source.
 How does it fail? Name each failure mode separately.
 Why does each rule exist? A rule without a reason is probably wrong.

2)The five blocks

Inputs-everything the feature reads. User-supplied inputs are the easy half. The silent ones (clock, current user, prior record state, config flags) are the hard half.

Outputs-everything the feature changes or produces. State changes, messages, emails sent. If a tester cannot observe it, it is not an output.

Normal flow-the happy path as 5–15 numbered steps. If you need 30 steps, you have two features.

Branches-every fork, walked step by step the same way you walked the normal flow. Keep asking "and then?" until each branch ends at an observable output.

Rules-constraints written as one-line conditions. Cutoffs, ownership, uniqueness, priority order.


3)The three gaps

a) Gap 1-The unwritten input

Smell: "the system just knows" or "obviously the system has..."

The spec is reading something it never named. Name every silent input explicitly, even when it feels too obvious to write down. The clock is an input. The current user is an input. The prior state of the record is an input.

Before you move on, ask: did you name the clock, the authenticated user, the prior record state, any config flag?

b) Gap 2-The unwalked branch

Smell: "if it fails, show an error" or "otherwise..."

One sentence is covering several distinct paths. Walk each one separately. For every fork, ask "and then?" until the branch produces something a tester can observe. If you pick any state and ask "what does this action do here?" and the answer is "I don't know," you found an unwalked branch.

c) Gap 3-The implicit rule

Smell: "obviously you cannot..." or "well, you wouldn't..." or "of course..."

Everyone assumed it, nobody wrote it. Write it as a one-line condition under Rules. One-line conditions are testable. Sentences that start with "obviously" are not.


Review before handing the spec over

1. Is there any sentence that depends on something not listed in Inputs?
2. Does any branch say "show error" without saying which error and what state the system is left in?
3. Did you say "obviously" or "of course" anywhere, out loud or in your head? Write that rule down.


If your feature has multiple states

Sketch a small lifecycle before hunting Gap 2. Each state with no outgoing arrow for a given action is a candidate unwalked branch. Each transition arrow-ask "what rule must hold for this to fire?" and you will find Gap 3.


What the spec does not cover

The spec describes what the feature does, not how it is coded. No database calls, no URL structure, no UI component choices, no framework decisions. If your spec locks in a design choice, remove it.

Three I almost missed today

After each spec session, write one entry per gap in your own words.

- Unwritten input I nearly left silent:
- Branch I nearly stopped at "show an error":
- Rule I nearly left as "obviously you cannot":
