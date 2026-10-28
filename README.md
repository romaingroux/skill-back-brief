# back-brief

A [Claude Code](https://claude.com/claude-code) skill that lets you check your understanding of what the agent reported. You restate the report in your own words, one statement per turn, and the agent answers each statement with a one-word verdict.

The name comes from the military practice of the same name: the receiver of an order repeats it back in their own words so the giver can catch what was misheard. Here the roles are reversed. The agent gave the report, you repeat it back, and the agent catches what you misread. It also catches what the agent itself got wrong: a long report can rest on a claim the agent never verified, and a short restatement of that claim forces the check.

## What it does

You type `/back-brief`, the agent says it is ready, and you start sending statements. Each one is split into its atoms, every atom is checked against the session (and against the disk or a tool when the agent only asserted it earlier), and the agent replies with one of four verdicts:

```
Correct
Correct, except <the wrong atom>
Incorrect: <one sentence>
Unverified
```

- **Correct** may carry one parenthetical with a value or reference not yet stated in the conversation.
- **Correct, except** names the single minor atom that fails in an otherwise right statement.
- **Incorrect** is one sentence with at most one reference. When the agent's own earlier report caused the error, one added clause says so.
- **Unverified** means nothing in the session or a cheap check settles the statement.

Questions are refused. The skill only verifies statements, so "why does X happen?" gets a one-line request to rephrase it as a statement. The agent never proposes statements of its own.

The series ends when you say you are done or give the agent a task. It acknowledges in one line and resumes normal operation, with no summary.

## Example

```
User:  The failing test is in the parser module.
Agent: Correct (tests/test_parser.py:88).

User:  It fails because the fixture file was moved.
Agent: Incorrect: the fixture is still at tests/fixtures/sample.json; the test fails on an encoding assertion.

User:  Why does the encoding differ?
Agent: Not a statement. Rephrase it as one and I'll verify it.

User:  Done.
Agent: Back-brief closed.
```

## Invocation

The skill can be invoked by hand with `/back-brief`, optionally with a first statement as argument:

```
/back-brief
/back-brief The failing test is in the parser module.
```

It can also be triggered by the agent when you write phrases such as "let me rephrase to understand", "validate my understanding", or ask for "correct or not correct" answers.

## Installation

Clone the repository anywhere and run the install target:

```bash
git clone https://github.com/romaingroux/skill-back-brief
cd skill-back-brief
make install
```

This copies the skill files into `~/.claude/skills/back-brief`. After a `git pull`, run `make install` again to update the installed copy. To install into a single project instead, point `SKILLS_DIR` at that project's skills folder:

```bash
make install SKILLS_DIR=/path/to/project/.claude/skills
```

`make uninstall` removes the installed copy.

## Files

- `SKILL.md` contains the skill definition: frontmatter, the per-turn procedure, the verdict contract and a worked example.
- `Makefile` installs and uninstalls the skill.
- `LICENSE` is the GNU GPL v3 text.

## License

This project is licensed under the GNU General Public License v3.0 or later. See `LICENSE`.
