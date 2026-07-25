# Solution Plan: Agent State Persistence

## Overview
Currently, the `Orchestrator` only persists the agent's progress to the `SessionStore` (Redis) at the very end of the execution run. If the server restarts or crashes during a long-running review, all intermediate tool results are lost. The goal is to persist the agent state incrementally after each tool's execution.

## Concrete Steps
1. **Modify Orchestrator's Tool Execution Loop**:
   - Update `agent/orchestrator.py`, specifically the `run` method.
   - Inside the loop over the `plan` (where `_execute_tool` is called), move or duplicate the session state persistence logic to run after each tool completes successfully.
   - After catching any exceptions during tool execution, we should also persist the failure state to ensure the final state of that tool run is recorded.

2. **Refactor State Persistence Logic (Optional but recommended)**:
   - Create a helper method in `Orchestrator` (e.g., `_persist_state(profile_id, session_state, results)`) to keep the code DRY, since it will be called inside the loop (for success and error cases) and potentially at the end of execution.

3. **Update Unit Tests**:
   - The newly created `tests/unit/test_orchestrator_reproduction.py` currently has a failing test `test_orchestrator_should_persist_incrementally`.
   - Fixing the codebase should make this test pass. Ensure all existing tests in `tests/` remain green.

## Files to Touch
- `agent/orchestrator.py`: Modify the `run` loop to persist `session_state` incrementally.
- `tests/unit/test_orchestrator_reproduction.py`: Validate that the newly added test passes after the fix.

## Risks and Unknowns
- **Performance Overhead**: Calling `session_store.set` (which involves a Redis network call and JSON serialization) after every tool execution adds some overhead. Given Redis is typically fast and we are dealing with a few long-running tasks, this is an acceptable tradeoff for resilience.
- **Race Conditions**: If there are concurrent updates to the same session store for the same `profile_id`, they could overwrite each other. Assuming reviews for a single profile run sequentially, this shouldn't be an issue.
