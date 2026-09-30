# Authoritative sources by ecosystem

These are starting points. Always link the **specific section**, not the site's front page.

## Rust

| Need | Source |
|---|---|
| Concepts, first contact | The Book: https://doc.rust-lang.org/book/ |
| Exact language semantics (desugaring, method resolution, derives) | The Reference: https://doc.rust-lang.org/reference/ |
| Library behavior, trait impls, method bounds | std docs: https://doc.rust-lang.org/std/ |
| Real implementation, installed locally | `$(rustc --print sysroot)/lib/rustlib/src/rust/library/{core,alloc,std}/src/` (install with `rustup component add rust-src`) |
| Behavior changes between editions | Edition Guide: https://doc.rust-lang.org/edition-guide/ |
| Naming and API design (`as_`/`to_`/`into_`, `iter`, conversions) | API Guidelines: https://rust-lang.github.io/api-guidelines/ |
| Unstable features: why something is nightly-only | The `#[unstable(feature = "...", issue = "N")]` attribute in the source, then https://github.com/rust-lang/rust/issues/N |
| Design rationale | RFCs: https://rust-lang.github.io/rfcs/ |
| Hands-on examples | Rust by Example: https://doc.rust-lang.org/rust-by-example/ |
| Error codes | `rustc --explain EXXXX` |
| Macro expansion / desugaring | `cargo expand` (cargo-expand), or the Playground's "Show HIR/MIR" tools |
| Third-party crates | https://docs.rs/<crate>, and the crate's source at `~/.cargo/registry/src/*/` |

## Solidity / EVM

| Need | Source |
|---|---|
| Language semantics | https://docs.soliditylang.org/ (select the version the project pins) |
| Well-established implementations | OpenZeppelin Contracts source and docs |
| Standards | EIPs: https://eips.ethereum.org/ |
| Tooling | Foundry Book: https://book.getfoundry.sh/ |

## Solana / SBPF

| Need | Source |
|---|---|
| Protocol changes and feature gates | SIMDs: https://github.com/solana-foundation/solana-improvement-documents |
| VM / SBPF | Local clone of the upstream repo, cited as file:line |

## Other ecosystems (quick pointers)

- **Python:** docs.python.org (language reference and library reference), PEPs, CPython source for built-ins.
- **TypeScript / JS:** the TypeScript Handbook, MDN, and the ECMAScript spec (tc39.es) for exact semantics.
- **Go:** go.dev/ref/spec, pkg.go.dev, Effective Go.

## Checking claims about other projects

Before saying "crate X does Y", check it: read its docs.rs page or source in `~/.cargo/registry/src/`, or search the upstream repo. If you can't check it, say "I believe X does Y, but I haven't checked", and turn the check into a task for the learner.
