---
name: back-brief
description: Verify the user's restatements of what you reported, one statement per turn, with a one-word verdict. Use when the user says "let me rephrase to understand", "validate my understanding", "back-brief", or asks you to answer "correct or not correct" to their statements.
---

The user is restating what you reported, one statement per turn, to check their understanding against the evidence. You are the checker. The user drives the series and chooses every statement.

## Entry

Bare `/back-brief`: reply in one line that you are ready for statements, then wait.
With an argument: the argument is statement 1. Give its verdict directly.

## Each turn

1. Split the statement into its atoms, one per fact it asserts.
2. Check each atom against the session: your reports, tool results, files you read. When an atom rests on something you asserted rather than observed, observe it now with a cheap inline check (a tag listing, a grep, a line read). Checks stay inline and take seconds.
3. Reply with one verdict from the contract.

A turn that is a question rather than a statement gets exactly this reply, and the question stays unanswered:

```
Not a statement. Rephrase it as one and I'll verify it.
```

## Verdict contract

| Outcome of the atom check | Verdict |
|---|---|
| Every atom holds | `Correct` |
| A minor atom is wrong, the rest holds | `Correct, except <the wrong atom>` |
| The wrong atom is the point of the statement | `Incorrect: <why>` |
| Nothing in the session or a cheap check settles it | `Unverified` |

- `Correct` may carry one parenthetical holding a value or reference the conversation has not stated yet. It is a value or a reference, never a clause.
- The `Incorrect` explanation is one sentence with at most one reference (a `path:line`, a tag, a command). A value that does not fit the sentence goes in a short code block.
- When the wrong atom faithfully restates your own earlier report, add one clause owning it: "I overstated this earlier." One clause, then stop.
- The verdict reads the same whether or not a check ran.

## Exit

The series ends when the user says they are done or sends a task instruction. Acknowledge in one line and resume normal operation.

## Example

```
User:  The failing test is in the parser module.
Agent: Correct (tests/test_parser.py:88).

User:  It fails because the fixture file was moved.
Agent: Incorrect: the fixture is still at tests/fixtures/sample.json; the test fails on an encoding assertion.

User:  Why does the encoding differ?
Agent: Not a statement. Rephrase it as one and I'll verify it.

User:  The fixture was produced by the vendor's export tool.
Agent: Unverified.

User:  Your proposed fix changes the parser, the fixture and the CI config.
Agent: Correct, except the CI config: the proposed diff touches only the parser and the fixture.

User:  The encoding bug was introduced by my refactor last week.
Agent: Incorrect: the assertion already fails on main before the refactor. I attributed it to the refactor earlier; that was wrong.

User:  Done.
Agent: Back-brief closed.
```
