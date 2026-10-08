# lab-instructor

A Claude Code plugin for **learning** a hands-on technical guide (book, course, tutorial) with Claude as the instructor — not finishing it for you.

- **The learner types every state-changing step.** Claude explains, asks for a prediction, verifies read-only, and diagnoses with questions, not fixes.
- **The source material is the authority**, translated to your real environment, with every supplied file understood before it is installed.
- **Chapter quiz gates and a journal** that lets any new session resume where the last one stopped.

Two skills:

| Skill | Use it when |
|---|---|
| **`lab-instructor`** | learning: Claude teaches, you type. Includes how a lesson session drives a lesson screen |
| **`build-lab`** | building the lesson screen itself (in its own session): what it shows, how it talks to the instructor, and the invariants that keep your terminal yours |

## Install

From your terminal (adds the [by-hand](https://github.com/MuathIbnHassan/by-hand) marketplace, then installs):

```bash
claude plugin install lab-instructor --marketplace MuathIbnHassan/by-hand
```

Or inside a Claude Code session:

```
/plugin marketplace add MuathIbnHassan/by-hand
/plugin install lab-instructor@by-hand
```
