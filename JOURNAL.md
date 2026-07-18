# PathReview Development Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/47

**Issue title:** Agent state isn't persisted across API restarts, causing in-progress reviews to be lost

**Tier:** [ ] Tier 1  [ ] Tier 2  [x] Tier 3

**Problem summary:**
Currently, the execution state of the portfolio review agent is stored only in-memory. If the FastAPI backend server restarts or crashes while a review is in progress, all state associated with the active evaluation is lost, and the review must be restarted from scratch. A successful fix will persist the agent's state to a database or cache (such as PostgreSQL or Redis) so that in-progress review sessions can be fully restored and resumed across API restarts.

**Branch name:** fix/47-agent-state-persistence

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

---

### Is This Issue Right for Me?

#### Part 1 — Understanding the Issue
- **Can I explain what this issue is asking for in my own words?**
  - [x] I can explain the problem and the expected behavior in 2–3 sentences without reading the issue.
  - *Reasoning/Notes:* Currently, when a user triggers a portfolio review, the agent's run state is kept strictly in-memory. If the FastAPI backend server restarts or crashes mid-review, that state is lost, leaving the review permanently in a "pending" or incomplete state with no way to recover. A successful fix will serialize and persist the agent's progress (either in PostgreSQL or Redis) so it can resume after a restart.
- **Do I understand which part of the app is affected?**
  - [x] I've located the relevant files and confirmed they exist in the codebase.
  - *Reasoning/Notes:* Affected areas include `api/routes/reviews.py` (which manages review creation and status), the database models in `core/models/` (specifically the reviews table structure), and potentially the session store in `agent/memory/session_store.py` (which uses Redis).
- **Do I understand what "done" looks like?**
  - [x] I can describe a concrete before-and-after: what the user sees before the fix and what they see after.
  - *Reasoning/Notes:* 
    - *Before:* If the server restarts during a review, the UI shows a stuck loading spinner or fails, and querying the API shows a lost state.
    - *After:* If the server restarts, the backend reload triggers state retrieval from database/Redis, allowing the agent to pick up where it left off, and the user eventually sees a completed review.

#### Part 2 — Tier Fit
- **Is the tier a realistic match for where I am right now?**
  - [x] I'm not choosing a Tier 3 issue to "challenge myself" if I haven't completed a Tier 1 or 2 first.
  - *Reasoning/Notes:* I have prior experience with large codebases, python concurrency/async systems, and database design. Persisting complex agent state is a Tier 3 challenge that matches my technical background.

#### Part 3 — Codebase Readiness
- **Can I find the relevant code?**
  - [x] I've found and read the specific code the issue references (not just the file — the function or section).
  - *Reasoning/Notes:* Found the active endpoints in `api/routes/reviews.py`, specifically `create_review` and how it schedules the background task, and the schema definitions.
- **Do I understand the surrounding code well enough to change it safely?**
  - [x] I've read enough surrounding context that I can write a rough plan for the fix without looking anything up.
  - *Reasoning/Notes:* Yes, the plan involves adding state-serialization capabilities to the agent orchestrator, storing this state representation in the postgres database `reviews` record or a Redis session cache on each state transition, and restoring from this persisted state upon starting the background task if it already exists.
- **Have I read the relevant test file?**
  - [x] I've found the test file for my module and read at least one test end-to-end.
  - *Reasoning/Notes:* I reviewed the tests under `tests/unit/` (specifically `test_reviews.py` or similar).

#### Part 4 — Scope and Time
- **How many others are already working on this issue?**
  - [x] I've checked the issue comments and the ledger's Claims count, and I'm fine with how many others are on this issue.
  - *Reasoning/Notes:* The ledger lists 3 claims for this issue, which is standard and gives room for peer review.
- **Is the scope realistic for Weeks 8–9?**
  - [x] I've estimated the time this will take and I'm confident I can complete it before the Week 9 deadline.
  - *Reasoning/Notes:* Estimated 10-15 hours of development, testing, and documentation, which is well within my availability.
- **Are there any blockers or dependencies?**
  - [x] This issue has no open blockers or dependencies on other unresolved issues.
  - *Reasoning/Notes:* No other issue blocks agent state persistence implementation.
