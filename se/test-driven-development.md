# Test-Driven Development (TDD)

**Date:** 2026-06-29

---

## The Red-Green-Refactor cycle

TDD is built around a short, repeating loop of three steps:

1. **Red** — Write a test for behavior that doesn't exist yet. Run it and watch it fail. A failing test proves the test itself is actually checking something.
2. **Green** — Write the minimum amount of code needed to make the test pass. No more, no less — resist the urge to add extra functionality here.
3. **Refactor** — Clean up the implementation (and/or the test) now that it's covered by a passing test. Improve names, remove duplication, simplify logic — all while keeping the test green.

Then repeat the cycle for the next bit of behavior.

---

## Why it works

- Writing the test first forces you to think about the API/interface before the implementation.
- "Just enough code to pass" keeps scope tight and avoids speculative, unused code.
- The refactor step is safe because the test suite catches regressions immediately.
