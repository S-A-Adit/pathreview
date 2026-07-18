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
