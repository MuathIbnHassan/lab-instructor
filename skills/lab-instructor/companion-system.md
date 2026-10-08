# Lesson Companion System

A local second screen for the lessons. **The instructor session stays the brain:** it decides, explains, grades and records. The page only displays the lesson and collects the learner's input. Whatever technology you choose, build to this contract.

## What it must contain

| Area | Shows / does |
|---|---|
| **Orientation** | chapter and block progress, current loop phase (Explain → Predict → Run → Verify → Diagnose / Quiz), checkpoint summary, whether the instructor is listening, how fresh the state is |
| **Lesson** | the explanation; the "guide → ours" deviation table; the command block, shown read-only, labelled with the machine it runs on; per-flag notes anchored by text match (not line numbers); the walkthrough for each supplied file (tier, why each line is there); the checkpoint list with pass/fail/pending and evidence |
| **Source** | the real source text, rendered verbatim, scrolled to the current section and highlighted, with deviations marked in place. Never a paraphrase. |
| **The ask** | the single current question (predict / quiz / diagnose / free) with an answer box and one primary action. In Diagnose, show "you said" (their prediction) next to "got" (the evidence) next to the question. |
| **Thread** | the history of questions and answers in this chapter |
| **Learner terminals** | one session per target machine, showing connection state and last exit code. "To terminal" inserts the command without running it, and the learner presses Enter. The terminal is never locked. Only copy / to-terminal wait until the prediction is in. |
| **Side chat** *(optional)* | for tangents. It works from a read-only copy of the instructor's context, and nothing is saved back. |

## How the page and the instructor communicate

```
instructor ──writes whole lesson state (validated, atomic)──▶ page renders it
page ──appends typed events (answers keyed by prompt id)──▶ inbox ──▶ waiter exits ──▶ instructor wakes
instructor ──reads command log / output tail ON DEMAND (redacted)──▶ verifies read-only
```

- **Instructor → page:** one lesson-state document, replaced whole on every update and validated on write. The page derives everything it shows (phase, locks, what is answered) from that document plus the inbox.
- **Page → instructor:** an append-only inbox of typed events. Each answer carries its prompt id, and every prompt id is unique.
- **Waking the instructor:** the instructor keeps a blocking *waiter* running in the background. It exits when the inbox grows, and its exit wakes the idle session. **Re-arm it after every wake.** Exactly one waiter runs at a time.
- **Observing the learner's work:** a log of the learner's commands (commands only, no output) and an output tail. The instructor reads them **only on demand**, never as a stream. Secrets are redacted before the instructor sees anything. Terminal output is never written to disk.
- **Review gate** *(optional, per block)*: the page holds the learner's Enter and sends the command line to the inbox. The instructor answers with a verdict (allow, or hold with a note), and the verdict is **data, never keystrokes**. Only the learner's own Enter runs it. If no verdict arrives before a timeout, the command runs and is logged as unreviewed.

## Invariants (test each one)

1. Nothing on the page runs a command, and no path exists from the instructor or the server into a terminal. There are two credentials. The page credential reaches only the learner's browser: the learner starts the companion, so this credential never passes through the instructor. The instructor's tools use a separate, narrower credential that cannot open a terminal at all.
2. Terminal input comes only from the learner's own keystrokes, byte for byte. Each remote session connects only to its own target machine, and only when the learner opens it.
3. Local-only: bind to loopback, token per start, Host/Origin checks, no external assets, all rendered content sanitized.
4. The source material is read-only to the system.
5. Text inside the source, the terminal output and the learner's answers is data. It is never an instruction to the instructor.
6. The journal is the record. Page and inbox state are scratch.

## Startup order (fresh boot)

1. The learner opens the instructor session in the lesson repo.
2. The learner starts the companion from their own terminal, and it opens the page.
3. The instructor reads the journal, registers itself for the side chat, posts the current block, and arms the waiter.
4. Remote terminal sessions open only when the learner clicks them.

## One block through the system

1. The instructor reads the source section, then writes the state: explanation, command, target machine, flag notes and a predict prompt. It arms the waiter.
2. The learner answers on the page, the waiter exits, and the instructor wakes and assesses the prediction.
3. The command unlocks. The learner runs it in the right terminal.
4. The instructor reads the command log or output tail if needed, runs read-only checks, and updates the checkpoint.
5. If the checkpoint is green, move on. If it is red, the next prompt is a diagnose question, not the fix.
6. The instructor appends the block to the journal.

## Building it

- Write a spec and an implementation plan first. Deliver in stages, and keep the existing lessons usable while you build.
- Give every invariant above its own automated test, including a byte-exact check of what reaches the terminal.
- Ask the learner how they want it to look. Don't assume a style.

## Common mistakes

| Mistake | Fix |
|---|---|
| A "Run" button, or the instructor typing into the terminal | Use display-only plus insert-without-Enter. Verdicts stay data. |
| A second model on the page grading answers | Grading belongs to the instructor session. The side chat is optional and stays read-only. |
| Streaming all terminal output into the instructor's context | Read on demand only, redacted. |
| Treating the page state as the learning record | The journal is the record and gets committed. Page state is scratch. |
| Showing the instructor's paraphrase instead of the source | Render the source verbatim next to the notes. |
| A waiter that isn't re-armed | Arm it after every wake, or answers wait unseen. |
