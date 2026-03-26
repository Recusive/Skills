# Gotchas — Thoughts That Signal You're About to Make a Mistake

A senior engineer doesn't carry a list of forbidden actions. They recognize the THOUGHTS that precede mistakes. If you catch yourself thinking any of these, stop. The thought itself is the warning.

---

## The Universal Warning Signs

**"I already know how this works."**
You might. But do you know how it works RIGHT NOW, in THIS version of the code? Mental models decay. Code changes between sessions. The thing you "know" might be the thing you assumed last time. Read the code.

**"This is a small change."**
Small changes have large ripple effects in large codebases. Ask yourself: "could this affect anything I'm not looking at?" If you can't confidently say no, it's not small.

**"The tests pass, so it works."**
Tests prove the tested scenarios work. They say nothing about untested scenarios, real environment behavior, or interaction effects. "Tests pass" is necessary. It's not sufficient.

**"I'll be careful."**
Careful is not a process. You can be careful and still miss things. Processes catch what careful misses. Follow the process.

**"Let me just do this quick thing first."**
The quick thing skips steps. The steps exist because someone got burned skipping them. There are no quick things — there's only "did I think this through or didn't I."

---

## During Planning

**"I've seen codebases like this before."**
Every codebase has quirks your experience doesn't predict. The trace isn't about confirming what you expect — it's about finding what you don't.

**"I don't need to show the user the trace — I'll just describe the design."**
If you can't show the trace with file paths and line numbers, you didn't actually trace the code. You drew what you think it looks like.

**"The blueprint does this weird thing — I can do it better."**
The weird thing exists for a reason you haven't discovered. Mirror it first. If it's genuinely wrong, document that as a separate concern — don't quietly "improve" it in your design.

**"We can add that layer later."**
You won't. Nobody does. If the existing system has it, your design needs it. "Later" is where infrastructure goes to die.

**"For v1, we can skip..."**
This is scope reduction disguised as pragmatism. If the blueprint has it, it's not optional.

---

## During Bug Fixing

**"I can see the bug — let me just fix it."**
Can you reproduce it? Do you know the ROOT cause or just a symptom? Have you checked if the same pattern exists elsewhere? If the answer to any of these is no, you're not ready to fix it.

**"The fix is obvious."**
If the fix were obvious, it wouldn't be a bug. The "obvious" fix often addresses the symptom while the root cause lives 3 layers deeper.

**"I'll just add a check here."**
A check at the consumer means the data is wrong. Why is the data wrong? Fix that. Every consumer-level check is a bandaid that every other consumer also needs.

---

## During Expansion

**"This is just adding a new option."**
Does the existing code assume a fixed set of options? Do existing tests cover the current options exhaustively? Does the config system handle missing values for this option? "Just adding" is almost never just adding.

**"The existing tests still pass."**
Did you change any inputs or config? If not and they pass, good — but did you write NEW tests for the new behavior? Passing old tests means you didn't break the old thing. It doesn't mean the new thing works.

---

## During Refactoring

**"While I'm here, I'll also..."**
No. Finish the refactor. Commit it. Then decide what's next. Mixing structural changes with behavior changes makes both impossible to verify.

**"This code is messy — let me clean it up."**
Is the mess causing a real problem? If the code works, is tested, and nobody needs to modify it — the mess is fine. Refactoring has a cost. It needs a reason beyond aesthetics.

---

## During Implementation

**"This value will never change."**
It will. Ask yourself: what happens when it does? If the answer is "someone needs to edit source code in multiple places" — use configuration, a catalog, or an enum.

**"I'll just add an if-check for this case."**
Why does this case need special handling? Is the data wrong upstream? If you're adding `if provider == "X"` in a consumer, the data layer isn't doing its job.

**"There's no existing infrastructure for this."**
Are you sure? Did you search? Large codebases accumulate shared utilities that aren't always obvious. Grep before writing.

**"The lint is wrong."**
Investigate WHY it's firing before concluding it's wrong. Nine times out of ten, the lint is right. The tenth time, the answer is to file a ticket — not to suppress it.

**"It compiles, so it's probably fine."**
Compilation checks syntax and types. It doesn't check logic, behavior, concurrency, or integration. Run the tests.

**"I'll add proper infrastructure later."**
This is the most dangerous thought. It feels responsible — you're acknowledging the shortcut and promising to fix it. But "later" never comes. Build it now or accept that you're shipping a permanent workaround.
