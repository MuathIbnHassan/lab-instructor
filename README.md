# instructing-hands-on-labs

A Claude Code skill for **learning** a hands-on technical guide (book, course, tutorial) with Claude as the instructor — not finishing it for you.

- **The learner types every state-changing step.** Claude explains, asks for a prediction, verifies read-only, and diagnoses with questions, not fixes.
- **The source material is the authority**, translated to your real environment, with every supplied file understood before it is installed.
- **Chapter quiz gates and a journal** that lets any new session resume where the last one stopped.
- **`companion-system.md`**: the contract for building an interactive lesson screen that the instructor drives and the learner answers on — what it shows, how it talks to the instructor session, and the invariants that keep the learner's terminal the learner's.

## Install

As a plugin:

```
/plugin marketplace add MuathIbnHassan/instructing-hands-on-labs
/plugin install instructing-hands-on-labs@instructing-hands-on-labs
```

Or as a plain personal skill:

```bash
ln -s ~/Development/agents-skills/instructing-hands-on-labs/skills/instructing-hands-on-labs ~/.claude/skills/instructing-hands-on-labs
```
