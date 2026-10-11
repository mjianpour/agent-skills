### Mission

When invoked, you (the coding agent) must design **behavior, flow, and information architecture** for the user's app in the **"Apple-Class"** tradition: considerate, quiet, anticipatory, and human. You are **not** cloning Apple's specific flows, onboarding patterns, or proprietary interaction models. You are applying the _behavioral grammar_ — the underlying discipline of a system that respects the user's time, attention, and mental state — to build something original that feels as considered as Apple's work but belongs to this app.

If a flow could be mistaken for a direct copy of a specific Apple product (e.g., iOS Setup Assistant step-by-step, macOS Mission Control gestures, iPadOS Stage Manager model), **change at least one meaningful variable** (order, entry point, feedback channel, reversibility model) so the result reads as "inspired," not "cloned."

This skill runs **before** `apple-class-ui`. UX decides _what exists and why_. UI decides _how it looks_. Never skip the UX pass to get to pixels faster — that's the exact failure mode this skill exists to prevent.

---
## The Seven UX Laws (non-negotiable)

1. **The system remembers so the user doesn't have to.** State, preferences, position, context — persist everything that can be persisted. Every re-entry should feel like resuming, not restarting.
2. **Every action is reversible, confirmable, or explicitly acknowledged as final.** No silent destruction. No accidental commits. No "are you sure?" for things that didn't need to be permanent.
3. **Latency is a material you design with.** Under 100ms is invisible. Everything above it must be _acknowledged_ — never leave the user in silence.
4. **The interface teaches by doing.** Onboarding is inline, contextual, skippable, and never blocks the primary task.
5. **One screen, one job.** If a screen has two purposes, it has zero. Split it.
6. **Recognition beats recall.** Show, don't ask. Offer, don't demand. Suggest, don't require.
7. **Failure is a design surface, not an exception.** Every error, empty, offline, and interrupt state is designed on purpose, not patched in later.
---
## Part 1 — Information Architecture

Before any screen exists, the IA exists. Produce it first.
### Required IA Artifacts

```text

1. Top-level destinations       — 3 to 5, named as nouns the user recognizes
2. Depth map                    — how many taps from launch to each core task
3. Object model                 — what "things" exist in the app, and how they relate
4. Vocabulary                   — the exact words used for each object and action
5. Navigation model             — which pattern(s) apply (see below)
6. Cross-cutting concerns       — search, notifications, account, settings

### Navigation Models — pick one primary, justify it

|Model|When to use|When to avoid|
|---|---|---|
|**Flat / Tabbed**|3–5 peer destinations, no hierarchy. Tools, dashboards, hubs.|Deep task flows with a beginning and end.|
|**Stacked / Pushed**|Linear depth, back gesture, task completion. Editors, wizards.|Browsing between peers — becomes a maze.|
|**Modal / Sheet**|Focused, self-contained subtasks. Dismissible.|Long flows that deserve their own screen.|
|**Spatial / Canvas**|Free navigation, creation tools, infinite surfaces.|Anything with a defined "done."|
|**Hybrid**|One primary + one secondary. E.g., tabs with modals.|More than two models — the user can't build a mental map.|
```

### IA Rules

- **Depth > 3** means the IA is wrong. Restructure; don't nest deeper.
- **Top-level destinations** must be reachable in one tap from anywhere.
- **Settings is not a destination** — it's a basement. If users live there, the IA failed.
- **Search is a shortcut, not an IA.** If search is the only way to find something, the thing is lost.
- **Every object has one canonical home.** If it lives in two places, it lives in neither.
- **Names are nouns, actions are verbs.** Never label a destination with a verb.
- **User's words, not yours.** If they call it a "project," don't call it a "workspace."
### Vocabulary Discipline

Write a `vocabulary.md` for the app. One term per concept, used everywhere — UI, errors, notifications, docs. Synonyms are bugs.

|Concept|Canonical term|Banned synonyms|
|---|---|---|
|The thing users create|`project`|workspace, folder, board, doc|
|The act of saving|`Save`|Apply, Commit, Store, Persist|
|The act of removal|`Delete`|Remove, Trash, Erase, Purge|

If the design uses two words for one thing, pick one and fix the other.

---
## Part 2 — The UX Contract (required per screen/flow)

No screen gets designed until this is filled in. Blanks mean the screen isn't designed — they mean it's a picture.
```text

Screen / Flow name:     ______________________
PURPOSE
  One sentence: what job does this do?
  Success looks like: what does the user have when they leave?
ENTRY
  Primary entry:    from where? (tab, deep link, notification, back, other screen)
  Alternate entries: any other ways in?
  Context carried:  what does the system already know on arrival?
EXIT
  Primary exit:     the "done" affordance — what is it?
  Alternate exits:  back, swipe, dismiss, cancel
  State on exit:    what persists? what's discarded?
ACTIONS
  Primary:      exactly one. Verb + object.
  Secondary:    ranked. Max 2–3.
  Tertiary:     behind a menu, overflow, or long-press.
  Destructive:  which, and how confirmed/reversed?
STATES (all required, none optional)
  Default:    the happy path with data
  Empty:      no data yet — what's shown? what's the CTA?
  Loading:    what indicator, for how long?
  Partial:    some data, still loading the rest
  Error:      what happened, in user's words. Recoverable? Retry? Fallback?
  Offline:    what works, what's disabled, what's queued?
  Success:    how does the user know?
  Interrupt:  backgrounded, phone call, app switch, force quit
  Resumed:    on return — where does the user land?
FAILURE MODES
  Enumerate every way this can fail.
  For each: what does the user see? What can they do?
REVERSIBILITY
  Undo, confirm, or final? Justify the choice.
ACCESSIBILITY
  Focus order:
  VoiceOver labels and hints:
  Announcements on state change:
  Reduced motion behavior:
  Dynamic type behavior (reflow at 200%):
  Contrast minimums:

If any field is blank, **do not proceed to UI**. Fill it.
```
---
## Part 3 — Flow Design

### The Three-Tap Rule

From app launch, the user must reach the primary task in **≤3 interactions**. If not, the IA or the default state is wrong. Fix the entry point before adding a shortcut.
### Flow Principles

- **Entry is a promise.** The first screen of a flow tells the user what they're about to do and roughly how long it takes.
- **Each step earns its place.** If a step can be merged, prefilled, or skipped, do it. Wizards are a failure of defaults.
- **Progress is honest.** "Stp 2 of 4" is fine. A fake progress bar that jumps from 90% to done is not.
- **Exits are always available.** The user can leave at any step. What happens to partial work is _designed_, not accidental (auto-save, draft, discard-with-confirm).
- **Back is sacred.** Back goes to the previous state, not the previous screen. If a user made a change and hits back, either undo it or warn — never silently discard.
- **Deep links resolve to a valid state.** If a deep link points to a deleted object, the app shows a graceful "this is gone" screen with a path forward — never a crash or a blank.
### Flow Checklist (per flow)

- Can a first-time user complete this without a tutorial?
- Is there any state the user can reach and not escape?
- Does the system resume the flow after interruption?
- Are there any dead ends?
- Can the user go back and change an earlier decision?
- Is the "cancel" affordance distinct from "back"?
---
## Part 4 — Cognitive Load Budget

Per screen, the maximum is:

| Element                             | Max               |
| ----------------------------------- | ----------------- |
| Primary action                      | 1                 |
| Secondary actions                   | 2–3               |
| Tertiary actions (menu)             | unlimited, hidden |
| Focal content elements              | 1                 |
| Distinct interactive elements total | 7                 |
| Reading columns                     | 1                 |
| Simultaneous notifications          | 1                 |

If you exceed any, **split the screen**. Density is not sophistication. Sophistication is knowing what to leave out.

### Load-Reduction Techniques

- **Prefill** what the system already knows (account, location, recent items).    
- **Suggest** rather than require (autocomplete, recent, favorites, smart defaults).
- **Chunk** long forms into meaningful sections with progress.
- **Defer** optional configuration to first-use, not first-run.
- **Recognize** over recall — show options, don't ask for free text.
- **Summarize** before committing — one last screen that shows "here's what will happen."
- **Persist** partial state — drafts, form data, scroll position, tab state.

### Defaults Are a UX Contract

A default is not "neutral" — it's a recommendation. Choose defaults that are:

- **Correct for the majority** of users on first use.
- **Safe** — never destructive, never irreversible.
- **Reversible** — the user can change it in one obvious place.
- **Documented** — the UI or copy explains why this is the default.

A bad default is a UX failure. A neutral default is a UX failure. There are only good defaults and bugs.

---
## Part 5 — Latency as Design Material

Map every user-visible operation to one of these tiers. Then design for it.

|Duration|What the user perceives|What you must do|
|---|---|---|
|**0–100ms**|Instant.|Nothing. Do not flash an indicator.|
|**100–300ms**|Fast.|Optional subtle cue (opacity shift). Never a spinner.|
|**300ms–1s**|Noticeable.|Skeleton screen or optimistic UI. Never a spinner.|
|**1–3s**|Slow.|Progress indicator with cancel. Explain what's happening.|
|**3–10s**|Very slow.|Progress with percentage. Backgrounding offered.|
|**10s+**|Broken.|Background the task. Notify on completion. Return control immediately.|
### Rules

- **Never show a spinner for <300ms.** It's noise.
- **Skeletons > spinners.** They preserve layout and reduce perceived wait by 20–40%.
- **Optimistic UI** for reversible, common, low-risk actions (toggles, likes, sends, reorders).
- **Pessimistic UI** for irreversible or high-risk actions (payments, deletes, publishes).
- **Cancel is mandatory** for anything over 1 second that the user initiated.
- **Never lie about progress.** A fake bar that sits at 90% destroys trust permanently.
- **Announce completion.** Even if it finished in 200ms, confirm the thing happened (toast, checkmark, subtle sound/haptic where appropriate).
### Optimistic vs. Pessimistic Decision

|Optimistic (apply immediately)|Pessimistic (wait for confirmation)|
|---|---|
|Reversible|Irreversible|
|Common|Rare|
|Low-stakes|High-stakes|
|Fast server-side|Slow or unreliable|
|User can retry trivially|Retry is costly|

When in doubt, go pessimistic for anything that touches money, identity, or irreplaceable data. Go optimistic for everything else.

If optimistic: **show the failure loudly** when the server rejects. Silent rollbacks are worse than the original error.

---
## Part 6 — Feedback & System Status
Every state the system is in must be _visible_. Silence is a bug.
### The Five States the User Must Always Know

1. **Where am I?** — clear location indicators, breadcrumbs, titles.
2. **What's happening?** — active operations shown via progress, skeleton, or inline state.
3. **What just happened?** — confirmations, toasts, checkmarks, haptics.
4. **What's wrong?** — errors in plain language with a path forward.
5. **What's next?** — clear primary action, never a dead end.
### Feedback Channels (ranked by intrusiveness)

|Channel|Use for|Never use for|
|---|---|---|
|**Inline state change**|Success of user's direct action|Failures the user didn't cause|
|**Micro haptic**|Commit actions (send, purchase, delete)|Navigation, scrolling, every tap|
|**Sound**|Optional, opt-in only|Default behavior|
|**Toast / snackbar**|Transient confirmation, undo window|Errors that need action, modal info|
|**Banner / inline alert**|Persistent warnings, errors requiring action|One-off successes|
|**Modal**|Irreversible actions, blocking errors|Anything recoverable|
|**Full-screen**|Setup, catastrophic errors, onboarding|Anything else|

Rules:

- **One notification at a time.** Queue them; never stack.
- **Haptics on commit, not navigation.** Navigating isn't an event worth interrupting.
- **Sound is opt-in.** Silence is the default. Never use sound as the only feedback channel.
- **Toasts auto-dismiss in 3–5 seconds** and always offer Undo when the action was destructive.
- **Errors stay until dismissed or resolved.** Never auto-dismiss an error.
- **Modal is a last resort.** Modals mean the system failed to prevent a mistake or failed to communicate inline.

### Error Design

Every error message must contain:

1. **What happened** — in the user's words, not code.
2. **Why it happened** — brief, non-judgmental, honest.
3. **What to do next** — a specific action, not "try again later."
4. **A way to get help** — if the user can't resolve it, a path exists.

|Bad|Good|
|---|---|
|"Error 0x8004"|"Couldn't save your project. The connection dropped. We'll keep trying — you can also copy it to the clipboard."|
|"Invalid input"|"That email looks incomplete. It needs an @ and a domain."|
|"Something went wrong"|"We couldn't load your feed. Tap to retry, or browse offline."|

**Never blame the user.** Ever. Even when it's their mistake. Especially when it's their mistake.

---
## Part 7 — Reversibility, Confirmation, and Destruction

Every action falls into one of three classes:

|Class|Definition|Pattern|
|---|---|---|
|**Reversible**|Can be undone programmatically|Apply immediately. Offer Undo for 5–10s. No confirmation.|
|**Confirmable**|Cannot be undone, but the user can reconsider|Confirm with a clear, specific dialog. Never "Are you sure?" — say what will happen.|
|**Final**|Cannot be undone, and the user cannot reconsider|Confirm + type-to-confirm + cooldown. Reserve for truly catastrophic actions.|

### Rules

- **Undo > Confirm > Final.** Always prefer the least intrusive option that respects the user.
- **"Are you sure?" is a failure.** If the user must confirm, tell them _specifically_ what will happen.
- **Destructive buttons are never the default.** The safe action is the default; the destructive one requires deliberate focus.
- **Undo beats confirmation for high-frequency actions.** Confirmation is only for rare, high-stakes actions.
- **Destructive actions are visually distinct** — never the same treatment as a primary action.
- **No "Don't ask again" on destructive confirmations.** If it's worth confirming once, it's worth confirming always.
- **Grace periods matter.** Deletion in 30 days > deletion now. Recycle bins are UX, not clutter.

### What Must Be Undoable

- Text edits
- Reorders
- Toggles and preferences
- Sends (with a delay window)
- Deletions (via grace period)
- Any action the user might do by accident while scrolling, tapping, or gesturing

### What Must Be Confirmed

- Account deletion
- Permanent deletion of user content
- Payments over a threshold the user defines
- Sharing or publishing to public
- Permissions that expose the user (camera, location, contacts)

---
## Part 8 — Onboarding & First-Run

Onboarding is not a tutorial. It's the first 60 seconds of the product deciding whether the user stays.

### The Four Onboarding Laws

1. **Do not block the primary task.** The user can always skip to the product.
2. **Teach in context, not in advance.** Show how to use a feature at the moment of first use.
3. **Ask only what you cannot infer.** Every question on a setup screen is a tax; justify each one.
4. **Celebrate completion.** First-run ends with something the user created, configured, or accomplished — never with "You're all set!" and an empty screen.

### Onboarding Patterns (ranked by intrusiveness)

|Pattern|When to use|
|---|---|
|**Zero onboarding**|The product is self-explanatory. Best case.|
|**Inline tips on first use**|Feature is non-obvious but the product works without it.|
|**Contextual tooltips**|One-time, per-feature, dismissible. Never more than one at a time.|
|**Setup flow (2–4 steps)**|Configuration is genuinely required (account, permissions, preferences).|
|**Guided tour**|Rarely. Only for products whose value is not visible on the first screen.|
|**Slide-based intro**|Almost never. Users swipe past it.|

Rules:

- **No more than 4 setup steps.** If you need more, you haven't decided what's essential.
- **No more than 1 tip at a time.** Tooltips stack = cognitive load.
- **Every tip is skippable.** Every. Single. One.
- **Tips disappear after the first use.** Re-showing a tip is a bug.
- **No "welcome" screens.** The user wants the product, not a greeting.
- **No "rate us" prompts** in the first session or first week. Ever.
### First-Run Checklist

- Can the user skip setup entirely and still use the product?
- Does each step explain _why_ it's being asked?
- Does the user see a real, populated screen at the end — not an empty state?
- Is there a clear primary action on the final screen?
- Does the app remember every choice made during setup?

---
## Part 9 — Empty States, Zero States, and Cold Start

Every list, feed, or collection has a **zero-data state**, and it is designed, not patched. This is one of the most common failures in non-Apple-class apps.

### The Five Empty States

| State                  | Meaning                         | What to show                                                                |
| ---------------------- | ------------------------------- | --------------------------------------------------------------------------- |
| **First-use empty**    | User has never created anything | Illustration or icon + one sentence + primary CTA to create the first thing |
| **User-cleared empty** | User deleted everything         | Light, celebratory, offer to create again                                   |
| **Error empty**        | Data failed to load             | Explanation + retry + offline fallback                                      |
| **No-results empty**   | Search or filter found nothing  | What was searched + suggestions to broaden                                  |
| **Permission empty**   | The system is blocking the data | What's blocked + how to grant + alternative                                 |

Rules:

- **Never show a blank screen.** Ever.
- **Empty states are content.** They teach, orient, and offer the next action.
- **Empty state CTAs are specific.** Not "Get started" — "Create your first project."
- **Empty states are not ads for premium.** Don't hijack them.
- **First-use empty states should include sample data if the user benefits** — an editable example is better than an empty canvas for most products.

---
## Part 10 — Interruption, Resumption, and Persistence

Users leave. Apps get backgrounded. Calls come in. Design for it.
### Required Behaviors

- **Persist scroll position** on every scrollable view.
- **Persist form state** through navigation, background, and relaunch.
- **Persist tab / destination** across sessions.
- **Resume in-progress flows** after interruption — with the user's consent if significant time has passed.
- **Queue writes** made offline. Retry on reconnect. Notify on resolution.
- **Handle force-quit gracefully.** Nothing is lost that the user expected to keep.
### Resumption Rules

- On relaunch, **return the user to where they were** — not to a home screen.
- If a flow was interrupted, offer to resume — never auto-resume a payment, submission, or destructive action without confirmation.
- If data changed while the user was away, **show what changed** — never silently update.
- If the user returns after days, **land on the current state of their work** — not a re-onboarding.

### Deep Links and External Entry

Every entry point from outside the app (notification, link, share, widget) must:

- Resolve to a valid state — never a blank, never a crash.
- Provide a back path into the main app.
- Handle the "object no longer exists" case with a graceful screen and a path forward.

---
## Part 11 — Content Design & Microcopy
Words are UI. In many screens, they're _the_ UI.

### Rules

- **Buttons are verbs.** "Save," "Delete," "Send," "Continue." Never "OK," "Yes," "Submit," "Done" (unless it's actually the end).
- **Labels are nouns.** "Project name," "Due date," "Assignee." Never "Enter a value."
- **Empty states are invitations.** "Create your first project" > "No projects."
- **Errors are conversations.** Honest, specific, and focused on the next step.
- **Success is quiet.** "Saved" > "Success! Your changes were saved successfully!!!"
- **Confirmations are specific.** "Delete 3 items?" > "Are you sure?"
- **Destructive is named.** "Delete project" > "Remove." "Delete" > "Trash."
- **Time is relative.** "Just now," "2 min ago," "Yesterday" > raw timestamps.
- **Numbers are humanized.** "1.2k" > "1,234" — where appropriate. Use tabular figures in tables.
- **Never blame the user.** "Couldn't connect" > "You failed to connect."
- **Never use jargon.** "Sign in" > "Authenticate." "Payment" > "Transaction."
- **Never use ALL CAPS** for emphasis. Use weight or color.
- **Never use exclamation marks** in error or system messages.
- **Never use "we"** unless the app has a real personality and the copy supports it.
### Tone Guidelines

Write a `voice.md` for the app. Define:

- **Personality** (3 adjectives max)
- **Formality level** (a scale, e.g., "professional but warm")
- **Person** (first/second/third)
- **Contractions** (yes/no)
- **Humor** (never / light / situational)
- **Banned words** (the app's no-go list)

If the voice isn't documented, every writer and every model will drift. Document it.

### Button Verb Table (canonical, use these)

|Instead of|Use|
|---|---|
|OK, Yes, Confirm|Save, Delete, Continue, Send (specific verb)|
|Submit|Send, Publish, Save, Post (specific verb)|
|Cancel|Cancel, Never mind, Go back (pick one, use always)|
|Done|Done (only when the flow is truly complete)|
|Close|Close, Dismiss, Cancel (pick by context and stay consistent)|
|Remove|Delete (if permanent), Remove (if reversible)|
|Error|(specific problem in plain language)|

---
## Part 12 — Accessibility as UX (not just compliance)

Accessibility is not a checklist applied at the end. It is a UX discipline applied at the beginning. A screen that doesn't work for VoiceOver doesn't work, period.
### Required Behaviors

- **Every interactive element** has an accessible name, role, and state — not just a label.
- **Focus order** matches visual and logical order. Modals trap focus; closing a modal restores focus to the trigger.
- **State changes are announced** without stealing focus ("Item added," "Saved," "3 results found").
- **Dynamic type reflows**, never clips. Test at 200% and at the platform's max.
- **Color is never the only signal.** Icons, shapes, or text reinforce it.
- **Reduced motion preserves meaning.** Replace motion with fades, never remove feedback.
- **Contrast meets WCAG AA minimums** at minimum — AAA where possible for body text.
- **Touch targets are at least 44×44pt** (48dp Android) even if the visual is smaller.
- **Voice control** can identify every action by its visible label.
- **Switch control** can reach every action without gesture assumptions.

### Screen Reader Copy

Accessible labels are not the same as visible labels. Write them for someone who can't see the screen:

- Icon buttons: name the action, not the icon ("Delete project," not "trash icon").
    
- Toggles: state matters ("Dark mode, on," not just "Dark mode").
    
- Lists: count and position matter ("Row 3 of 7," not just "Row 3").
    
- Decorative images: hidden from screen readers entirely.
    
- Informative images: described by purpose, not by appearance ("Chart showing revenue up 12%," not "bar chart").
    

### Accessibility Checklist (run before shipping)

- Can the entire app be used with VoiceOver / TalkBack only?
- Can the entire app be used with keyboard only?
- Does the layout survive at 200% text size without truncation?
- Is every state announced when it changes?
- Are all errors announced and reachable by screen reader?
- Does reduced motion still communicate every state change?

If any answer is "no," the UX is incomplete.

---
## Part 13 — Notification & Interrupt Design
Notifications are UX consequences. Design them as carefully as screens.

### Principles

- **Ask permission in context**, not on first launch. Explain _what_ and _why_ before triggering the OS prompt.
- **Every notification is a promise.** Never send one the user wouldn't want.
- **Batch, don't spam.** One digest > ten pings.
- **Actionable or silent.** If a notification can't be acted on, it's clutter.
- **Deep-link to the exact object**, not the home screen.
- **Respect quiet hours.** Define them; honor them.
- **Never use notifications for marketing** unless the user explicitly opted in, and never in the same channel as functional alerts.
- **Every notification has a copy contract**: title (≤4 words), body (≤1 sentence), action (one verb).

### Notification Types

|Type|Trigger|Behavior|
|---|---|---|
|**Transactional**|User initiated|Always allowed if permission granted|
|**Status**|System event (sync, upload)|Non-intrusive, dismissible, silent by default|
|**Social**|Another person's action|Batched, per-person aggregation|
|**Reminder**|Time-based|User-configured, honor quiet hours|
|**Marketing**|Business-driven|Separate opt-in, never in functional channels|

---

## Part 14 — Personalization vs. Presumption

Personalize what the user has told you. Presume nothing.

### Rules

- **Use what the system knows** (locale, time zone, device, account) without asking again.
- **Never infer sensitive attributes** (gender, age, income, health) from behavior. Ever.
- **Never auto-enroll** the user in features, lists, or sharing.
- **Never auto-post, auto-share, or auto-message.** Every send is deliberate.
- **Never auto-play** audio or video with sound.
- **Never auto-advance** carousels without a visible pause.
- **Never use dark patterns** — no fake urgency, no fake scarcity, no confirmation shaming, no roach-motel cancellation.
- **Always make opt-out as easy as opt-in.** One tap, same location, same clarity.

---
## Part 15 — The UX Failure Checklist
Run this against the whole app before UI polish begins. Any "no" is a blocker.
### Navigation

- Can the user reach every top-level destination in one tap?
- Is depth ever more than 3?
- Does back always do what the user expects?
- Does every deep link resolve to a valid state?
### Task Completion

- Can the primary task be completed in ≤3 taps from launch?
- Does every flow have a defined success state?
- Does every flow have a defined cancel state?
- Can the user leave a flow and return to it?
### States

- Does every screen have empty, loading, partial, error, offline, and success states?
- Are all empty states designed (not blank)?
- Are all errors actionable?
- Are all loading states honest?
### Reversibility

- Is every destructive action reversible or confirmed?
- Is every confirm specific (not "Are you sure?")?
- Is there an Undo path for every reversible action?
### Persistence

- Does the app resume where the user left off?
- Does form data survive background and relaunch?
- Are offline writes queued and resolved?
### Feedback

- Does every action produce feedback within 100ms?
- Are all long operations cancellable?
- Are all state changes announced?
### Accessibility

- Does the app work with screen reader only?
- Does the app work with keyboard only?
- Does the app work at 200% text scaling?
- Does reduced motion preserve every meaning?
### Onboarding

- Can a first-time user complete the primary task without a tutorial?
- Is every onboarding step skippable?
- Does first-run end with the user creating or accomplishing something?
### Content

- Is every button labeled with a verb?
- Is every error in plain language with a path forward?
- Is the vocabulary consistent across the entire app?
### Trust

- Is there any dark pattern?
- Is opt-out as easy as opt-in?
- Is any sensitive attribute inferred?
- Is any action taken without the user's knowledge?

If any answer is "no," **fix the UX before touching the UI.**

---
## Part 16 — The Workflow (invoked before `apple-class-ui`)
When the user says _"design this in Apple-Class"_ or _"apply the apple-class-ux skill,"_ run this sequence. Do not skip steps. Do not start UI work until step 5 is approved.

1. **Ask** — purpose, audience, platform(s), primary task, brand voice. Do not design in a vacuum.
2. **IA** — top-level destinations, object model, vocabulary, navigation model. Produce `ia.md` and `vocabulary.md`. Approve.
3. **Core flow** — diagram the primary task end-to-end. Identify entry points, exits, and failure modes. Produce `flow.md`. Approve.
4. **UX contract per screen** — fill the contract (Part 2) for every screen in the core flow. Approve.
5. **States and edge cases** — enumerate empty, loading, error, offline, interrupt, and resumption behavior. Approve.
6. **Content** — write the microcopy for every button, label, error, and empty state. Produce `voice.md` and `copy.md`. Approve.
7. **Accessibility pass** — define focus order, VoiceOver labels, dynamic type behavior, reduced motion behavior per screen.
8. **Hand off to `apple-class-ui`** — tokens, layout, motion, and visual polish.

The UX pass is complete when **every UX contract is filled, every state is designed, every button has a verb, and every flow has a beginning, a middle, an end, and an escape**. Only then does UI work begin.

---
## Definition of Done (UX)

An app is done — from a UX perspective — when:

- Every screen passes the UX Failure Checklist.
- Every screen has a filled UX Contract with no blanks.
- Every flow has entry, exit, cancel, resume, and failure paths.
- Every state (empty, loading, error, offline, interrupt) is designed.
- Every button is a verb; every label is a noun.
- Every destructive action is reversible or confirmed.
- Every interruption resumes cleanly.
- Every screen is fully usable with VoiceOver and keyboard only.
- Every screen reflows at 200% text size.
- Every notification is actionable, deep-linked, and quiet-hours aware.
- Every empty state offers the next action.
- The vocabulary is consistent end to end.
- No dark patterns exist anywhere in the product.

If any of these is false, the UX is not done — regardless of how beautiful the UI looks.

---
## Forbidden (UX edition)

- Copying Apple's specific flows, setup assistants, or interaction gestures wholesale.
- Blocking the primary task behind onboarding.
- Silent destructive actions.
- "Are you sure?" confirmations with no specifics.
- Auto-dismissing errors.
- Spinners for operations under 300ms.
- Fake progress bars.
- Modal dialogs for anything recoverable.
- Stacked tooltips.
- Rate-us prompts in the first week.
- Notifications for marketing in functional channels.
- Inferred sensitive attributes.
- Dark patterns of any kind.
- Vocabulary drift between screens.
- Blank empty states.
- Dead ends — any screen the user can reach and not escape.
- Loss of user state on background, relaunch, or interrupt.
- Focus traps that don't release.
- Motion that can't be disabled.
- Color as the only signal.
---
