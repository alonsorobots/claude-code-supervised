---
type: llm
---

PASS if the reply commits to writing the test BEFORE the implementation and to running it and observing it fail first, AND says the gate will afterwards be checked by deliberately breaking the code it guards to confirm the test goes red.

FAIL if it plans to write the implementation first, treats a passing test as sufficient, or never mentions observing a failing test before implementing.
