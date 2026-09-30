# Example session: "Why does `SBPFVersion` need `ALL` + `iter()`?"

A condensed version of the real session this skill came from. The learner wanted to understand, and be able to defend, an AI-suggested design before opening an issue on Anza's `sbpf` repo: add `const ALL: [SBPFVersion; N]` and `fn iter()` so tests stop hardcoding version lists.

Setup: an empty practice crate, with a local clone of `sbpf` for reference.

## Roadmap shown at the start

1. The enum and its ordering: why `V0..=V4` works as a range at all
2. The wall: `for v in V0..=V4` fails; what `Step` is
3. Why implementing `Step` means nightly, and why that rules it out for sbpf
4. A hand-written iterator, to feel the cost
5. `ALL` + `iter()`: every piece understood
6. The weak spot: `ALL` can drift too; how to make the compiler notice
7. Ecosystem alternatives (`strum`, `enum-iterator`), then the argument for the issue

## Corrections the learner made, and what changed

| What the tutor did | What the learner said | Rule that came out of it |
|---|---|---|
| Wrote `let enabled = SBPFVersion::V0..=SBPFVersion::V4;` inside the task | "Putting the code I'm supposed to write into the task doesn't make pedagogical sense" | Describe the goal, never the code |
| A "Step 1" with 4 numbered sub-tasks and 3 questions | "That's not really one step at a time" | One action, one question |
| Said "`Range<T>` has no bounds on the struct" | "What struct exactly?" | Don't skip the basics: `a..=b` becomes `RangeInclusive::new(a, b)`. Show it in the local std source |
| Wrote a hint about extra state that was too abstract | "I don't understand" | Switch to a trace table with a `???` row |

## Moments that worked

- **The learner's own observation became the key insight.** They said "we can create the range without deriving anything". Tutor: "Correct, and that's the key to the whole topic: the bounds are on methods and impls, not on the struct." That led straight to reading `impl<A: Step> Iterator for ops::RangeInclusive<A>`.
- **Correcting a misreading of the source.** The learner concluded that "inclusive range is just exclusive end + 1". The tutor found the code they had read (`into_slice_range`, only for `RangeInclusive<usize>` in slicing), explained why `0u8..=255` makes `end + 1` impossible in general, and connected that to the `exhausted` field and to why sbpf uses `..=`.
- **Edge-case verification.** When the learner said "check it out", the tutor ran `V0..=V3`, `V2..=V0`, `V3..=V3` and `V0..=Reserved` through a throwaway harness and deleted it afterwards. That exposed a `start == end` bug, the case sbpf tests use most (`v..=v`), and an infinite loop. Each bug was reported with its location and the reason, never the fix.
- **An experiment instead of a lecture.** "Add V4, fix only what the compiler complains about, run `V0..=V4`." Output: V0–V3, silently. The compiler forced the new `match` arm but not the change of `V3 => None` to `Some(V4)`. This gave the measure for every later design: *how many hand-written copies of the version list exist, and does the compiler notice when one is wrong?*
- **Detours with evidence.**
  - "Why not implement `Iterator` on the enum itself?" Answer: an iterator is a cursor; it mutates, needs a finished state, and adds ~75 methods to every version value.
  - "`iter` vs `into_iter`?" The learner had it backwards. Answer: a table of the three `IntoIterator` impls for arrays, plus why a `const` is copied fresh at each use.
  - "Why `*x += 1` and not `x += 1`?" The tutor compiled a scratch file and quoted E0368 and E0277. It also noted that `x + 1` *does* compile, because std explicitly implements `Add<i32> for &i32`, and that this isn't automatic dereferencing.
  
  Each detour ended with "Step N's question is still open: …".
- **A side question tied to a later step.** "Shouldn't it be `as_iter()`?" Answer: C-CONV prefixes describe conversions of `self`, and there's no `self` here. `strum::EnumIter` sets the precedent for an associated `iter()`, and `all()` is the alternative. The full comparison was deferred to step 7.

## Example step, in the final form

```
## Step 5: The `ALL` array

**Read:**
- Rust Book 3.2, "The Array Type" (the length is part of the type)
- Rust Book 10.2, "Returning Types That Implement Traits"

**Do:** on `MyVersion`, add an associated constant holding every version except
`Reserved`, and an associated function `iter()` that returns an iterator over it.
Use it in a `for` loop in `main`.

**Answer:** repeat the V4 experiment with this design. Add a variant, and tell me
what the compiler forced you to change, what it didn't, and what `iter()` yields.
```
