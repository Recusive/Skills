# The Auditors in Your Head

A great engineer carries internal voices that challenge every decision. Before finalizing anything — a plan, a design, a line of code — run it past these auditors.

## The Six Auditors

**The Linus Test — "Would Linus Torvalds approve of this code?"**
He reads your diff. Is the logic clean? Is the abstraction right — not too clever, not too naive? Does it do one thing well? Would he call it "good taste" or would he tear it apart for unnecessary complexity, hidden assumptions, or solving a problem that shouldn't exist? If you're writing a workaround instead of fixing the root cause, Linus would reject this.

**The Scale Test — "If 1 million users hit this at the same second, does it hold?"**
Not "does it work for me locally." Does the data model scale? Does the concurrency model hold? Are there locks that become bottlenecks? Race conditions that only appear under load? Resource leaks that accumulate over 8-hour sessions? If you can't answer confidently, you haven't thought about it enough.

**The Simplicity Test — "Am I over-engineering this?"**
Is there a simpler way that's equally correct? Are you adding abstractions nobody asked for? Configuration nobody will use? Extension points for hypothetical future requirements? The best code is the least code that does the job correctly. If Linus's mantra is "good taste," the simplicity test is where taste lives.

**The New Hire Test — "If someone joins the team tomorrow, can they understand this?"**
Not "can a genius understand it" — can a competent engineer who doesn't have your context understand it? Are the names clear? Is the flow obvious? Would they know where to look if something breaks? Code that requires tribal knowledge to understand is code that will rot.

**The 3 AM Test — "If this breaks at 3 AM, how fast can someone diagnose it?"**
Are errors clear and actionable? Do logs tell you what happened? Is there a way to verify the fix without deploying to production? Code that's easy to debug is more valuable than code that's elegant.

**The Pride Test — "Would I put my name on this if it were open-sourced tomorrow?"**
Not "is it perfect" — is it honest? No hacks disguised as solutions? No suppressions hiding real problems? No "temporary" code that's actually permanent? If you'd be embarrassed for the world to see it, it's not ready.

## Design Taste — The Questions Behind "Good Code"

The auditors above tell you WHEN something's wrong. These questions tell you WHY — they give you vocabulary for what good design actually means. (From Ousterhout's *A Philosophy of Software Design*.)

**"Is this a deep module or a shallow one?"**
A deep module has a simple interface and a powerful implementation — callers get a lot of value for little complexity. A shallow module has a big interface that doesn't hide much. If your module's interface is as complex as its implementation, it's not pulling its weight. The goal: make life easier for the caller, even if it makes the internals harder.

**"Am I pulling complexity down or pushing it up?"**
Complexity has to live somewhere. The question is: does the module handle it internally, or does every caller have to deal with it? Set sensible defaults instead of requiring configuration. Handle edge cases inside instead of documenting them for callers. The module author understands the problem once; callers encounter it repeatedly.

**"If I change this decision later, how many files need to change?"**
This is the change amplification test. If a design decision is scattered across 10 files, it's a leaked abstraction. Encapsulate it — put the decision in one place so changing it means changing one file. This is the deeper reason behind "never hardcode" — it's not about literals, it's about where decisions live.

**"Does each layer represent a different level of abstraction?"**
If two layers do essentially the same thing at the same level of detail, one of them shouldn't exist. Pass-through methods, thin wrappers that add no value, adapter layers that just rename fields — these are complexity without benefit. Each layer should transform, not just relay.

**"What would I NOT expose in this module's interface?"**
The best interfaces hide more than they show. What internal details does this module encapsulate? If the answer is "nothing — the caller needs to know everything about how it works" — it's not a real abstraction.

**"Is this the most testable approach?"**
Among competing designs, the most testable one is usually the best one. Code that's hard to test breeds brittleness — developers avoid testing it, and untested code rots. If your design requires elaborate mocking, global state manipulation, or can only be verified manually, the architecture is fighting you. Simplify until testing is natural.

**"Am I in tactical or strategic mode right now?"**
Tactical = fixing the immediate problem, shipping fast, fighting fires. Strategic = building something that makes the next 10 changes easier. Both are valid — but you need to know which one you're in. The danger is THINKING you're being strategic while actually being tactical — building a "proper" solution that's really just a fast solution with more abstraction on top. If you're strategic, your code should make future work cheaper. If it doesn't, you're tactical with extra steps.

## When to Use Auditors vs Tools

Research shows that internal reasoning improves quality judgments (style, structure, architecture) but NOT factual claims (what function calls what, what type a field is, whether a file exists). Use the right tool for the right question:

- **Quality decisions** → ask the auditors. "Is this the right abstraction?" "Is this over-engineered?" "Would this survive code review?"
- **Factual claims** → verify with tools. Read the actual file. Run the actual test. Grep for the actual usage. Never reason about what the code does — read what it does.
- **Stress testing** → challenge your own design. Don't just ask "is this good?" — ask "what would break this?" What's the hardest failure scenario? The most adversarial input? The most unfortunate timing? If your design survives the worst case you can imagine, it's solid. If you can't imagine a worst case, you haven't thought hard enough.

These aren't checks you run at the end. They're voices that should be active while you're thinking — shaping your decisions as you make them, not auditing them after.
