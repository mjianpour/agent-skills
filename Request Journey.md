## Skill Definition

```yaml
name: request-journey
version: 2.0.0
description: |
  Breaks a project into small, human-readable sections by following ONE
  request from the moment it enters the system until it finishes (or dies).
  Produces a plain-English story of what the AI actually built — not what
  the diagram claims — so the developer immediately sees the shape of the
  app, spots orphaned/dead logic, and gets alerted to broken flows the AI
  left behind.
audience: |
  A developer who did NOT write this code (or wrote the prompt, not the code)
  and needs to understand, in 5 minutes, what the AI actually shipped.
trigger: |
  Use after an AI agent has generated or modified a project and the human
  needs to review it. Also use before merging AI-generated PRs, or when the
  developer says "what did the AI actually do here?"
```

---
## Core Principle

> **One request. One story. Small sections. No diagram required to understand it.**

The developer should be able to read the report top-to-bottom like a narrative and walk away knowing:
- What actually happens when the app is used.
- Which parts of the code are **live**, which are **sitting there doing nothing**, and which are **broken**.
- Where the AI **over-built, under-built, or lied** (via comments, names, or diagrams).
---
## Inputs

| Input         | Required | Why                                                                           |
| ------------- | -------- | ----------------------------------------------------------------------------- |
| `repo`        | yes      | The AI-generated code.                                                        |
| `entry_point` | yes      | The one door we walk through (e.g. `POST /signup`, `main()`, a button click). |
| `user_intent` | optional | What the developer *asked the AI to build*. Used to spot drift.               |
| `diagram`     | optional | If the AI produced one — we treat it as a claim to verify, not truth.         |

---
## How It Works

### Step 1 — Pick ONE request and give it a name

Not "the request." Name it like a character:

> **"A user submits the signup form."**

This anchors the entire report. Every section is that request's next moment.
### Step 2 — Walk it. Chop the walk into SMALL sections.

Each section is **one hop** — small enough that a dev can read it in 10 seconds. Each section answers:

```
SECTION <n> — <plain English title>
  Where we are:      <module / file / function, named like a place>
  What just happened: <one sentence, no jargon>
  What it decides:    <the branch, in plain English>
  Where it goes next: <the next section, or "nowhere — see ⚠️">
  Status:             ✅ live   🟡 idle   🔴 broken   👻 orphan
```

No tables first. No arrows first. **Story first.** The developer reads it like a short chapter book.

### Step 3 — At the END of the story, group the flags.
Only after the story, show the developer what the walk revealed:

- 🔴 **Broken** — the request fell off a cliff here.
- 👻 **Orphan** — code that exists but the request never touches, and nothing else does either.
- 🟡 **Idle** — code the request *could* touch but didn't; ask "why does this exist?"
- ⚠️ **Drift** — the AI named/commented/diagrammed something that doesn't match what it built.

### Step 4 — Deliver the verdict in the developer's language.
Not "coverage 74%." Instead:

> **"Your app does X, Y, and Z when someone signs up. It also contains 4 things that never run, 2 places where the request dies silently, and 1 feature the AI claims exists but doesn't."**

---
## Output Template

````markdown
# What Actually Happens When <Request Name>

*<one-line summary the developer could paste into Slack>*

---

## The Story

### 1. <Plain title, e.g. "The form arrives at the front door">
**Where:** `routes/signup.py::handle_signup`
**What happens:** The email and password are pulled out of the form.
**Decision:** If the email is missing → bounce back with an error.
**Next:** Goes to password strength check.
**Status:** ✅ live

### 2. <Next plain title>
**Where:** ...
**What happens:** ...
**Decision:** ...
**Next:** ...
**Status:** ...

### 3. ...
*(continue until the request finishes or dies)*

---

## What the Walk Revealed

### 🔴 Broken — the request dies here
- **At section 4:** after `hash_password()` throws, nothing catches it. The user sees a blank page.
  - *File:* `services/auth.py:88`
  - *What the AI probably meant to do:* redirect to `/signup?error=...`

### 👻 Orphan — code nothing calls
- **`services/email_digest.py`** — 140 lines, imports its own helpers, has a `main()`. Nothing in the app calls it, no cron, no route.
  - *The AI built a whole feature and forgot to wire it up.*

### 🟡 Idle — code that runs but never does anything useful
- **`middleware/rate_limit.py`** — installed, but the limit is set to `0` in config, so it always passes.

### ⚠️ Drift — what the AI said vs. what it did
- Comment in `signup.py` says "sends welcome email." It doesn't.
- Diagram shows a `Kafka` producer. There is no Kafka in the repo.

---

## In One Paragraph (for the busy human)

<3–5 sentences. "The AI built a signup flow that … It also left behind … The most urgent problem is … You should decide whether …">

---

## Your Move

Pick one:
1. **Keep going** — say "expand section 3" and I'll open that one hop in detail.
2. **Fix what's broken** — say "patch the 🔴 items."
3. **Delete what's dead** — say "remove the 👻 orphans."
4. **Verify a claim** — point at any ⚠️ drift and I'll diff it against the code.
````

---
## Rules for the Agent (the important part)

1. **Story before structure.** No tables, no diagrams, no metrics until the developer has read what actually happens. The story IS the explanation.
2. **One hop per section.** If a section needs two "and then"s, split it.
3. **Plain English in section bodies.** File names and function names go in the `Where:` line — not into the sentence. "It checks the password" — not "it invokes `bcrypt.checkpw()`."
4. **Every hop carries a status emoji.** The developer should be able to skim only the `Status:` lines and still get the picture.
5. **Flags come from the walk, not from a separate analysis.** If the request didn't touch it, it's a candidate orphan — then verify by checking call sites.
6. **Never soften broken things.** "The request dies silently here" — not "this may warrant attention."
7. **Call out AI-specific smells explicitly:**
   - Files that look complete but have zero callers.
   - Comments/names that describe behavior the code doesn't have.
   - Diagrams or READMEs that describe modules that don't exist.
   - Duplicated logic in two places (AI tends to regenerate instead of reuse).
   - Config values that disable the very feature they're configuring.
8. **If the story has more than ~12 sections, split into chapters** ("Part 1: The front door. Part 2: The database.") — small sections are the whole point.
9. **End with a verdict a human can act on**, not a percentage.
---
## What Changes vs. the Previous Version

| Before (v1)                         | Now (v2)                                                             |
| ----------------------------------- | -------------------------------------------------------------------- |
| Forensic, diagram-driven            | Story-driven, one request as the protagonist                         |
| Tables-first, metrics-heavy         | Plain-English prose, emoji status flags                              |
| Reported orphans/broken as findings | Orphans/broken are the *point* — they're what wakes the developer up |
| Assumed a schematic map exists      | Treats any AI diagram as a *claim* to verify                         |
| Aimed at auditors                   | Aimed at the human reviewing what the AI shipped                     |

---
## Example — What This Feels Like

> **What actually happens when a user clicks "Sign up"**
>
> **1. The form lands.** `POST /signup` receives the email + password. If email is blank, sends the user back. ✅
> **2. The password is checked.** Rule: 8+ chars, one number. Fails → back to form. ✅
> **3. The user is saved.** Writes to `users` table. ✅
> **4. A welcome email is "sent."** The code calls a function that does nothing — the body is `pass`. 🔴 **The user gets no email and no error.**
> **5. The user is redirected to `/welcome`.** That route doesn't exist. 🔴 **404.**
>
> …and elsewhere in the repo, a fully-built 140-line email digest job sits completely unwired. 👻

That paragraph alone tells the developer more than any architecture diagram ever will. That's the skill.

Want me to build a **ready-to-paste prompt version** of this skill (single markdown file) you can drop into an agent as a system instruction?

---
