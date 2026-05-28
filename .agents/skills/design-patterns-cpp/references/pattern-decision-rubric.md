# Pattern Decision Rubric

Use this rubric before recommending a design pattern. The score is not a rigid rule; it forces an explicit tradeoff between extension pressure and complexity cost.

## Scoring

Rate each item from 0 to 2.

| Factor | 0 | 1 | 2 |
| --- | --- | --- | --- |
| Existing implementations | One concrete implementation | Two implementations | Three or more implementations |
| Future extension likelihood | Unclear or unlikely | Plausible | Already planned or recurring |
| Runtime switching | Not needed | Configuration-time choice | Runtime/user-driven switching |
| Creation complexity | Direct construction is simple | Some branching/config mapping | Complex construction, SDK setup, or object families |
| Coupling pain | Current code is easy to follow | Some duplicated or tangled code | Changes touch many modules |
| Testability benefit | Pattern does not help tests | Some fakes/mocks become easier | Interfaces significantly improve tests |
| Lifecycle/threading risk | Simple lifetime | Some ownership/thread concerns | QObject ownership, async I/O, or cross-thread behavior matters |
| Team maintenance cost | Pattern is unfamiliar or heavy | Manageable with examples | Already used or locally conventional |

## Interpretation

| Score | Recommendation |
| --- | --- |
| 0-4 | Prefer simple code. Mention the future trigger that would justify a pattern. |
| 5-8 | Consider a small refactor or a lightweight pattern. Keep migration incremental. |
| 9-12 | Pattern is probably justified. Provide class design and tests. |
| 13-16 | Pattern or pattern combination is strongly justified. Define boundaries and migration plan carefully. |

## Required Decision Output

When recommending a pattern, include:

1. Score and short reasoning.
2. Whether the pattern is needed now.
3. Simpler alternative if the score is low.
4. Upgrade trigger if using simple code today.
5. Risk of adding too many classes.

## Common Judgments

- One implementation plus vague future plans usually scores low.
- Multiple vendor SDKs usually score high for Adapter + Factory.
- Protocol or algorithm switching scores high for Strategy only if switching is real or extensions are likely.
- Undo/redo scores high for Command + Memento because the behavior requirement is structural.
- Qt signals/slots often remove the need for a custom Observer implementation.
