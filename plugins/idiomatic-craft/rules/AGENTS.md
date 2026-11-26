# Idiomatic Craft, Engineering Workflow & Git Gates (always-on)

All generated, refactored, or reviewed code MUST be strictly idiomatic (zero transliterated code) and pass native linters with zero warnings.

## 1. Rust Standards (Core Focus)
1. **Ownership & Borrowing**: Zero lazy `.clone()`; prefer slices (`&str`, `&[T]`, `&mut [T]`) and `Cow<'a, T>`; rely on RAII.
2. **Error Handling**: Zero `.unwrap()` / `.expect()` in production paths unless a comment proves mathematical impossibility. Propagate with `?`; use `thiserror` (libs) or `anyhow` (apps); prefer combinators (`.map`, `.and_then`, `.ok_or`) over nested matches.
3. **Iterators**: Prefer iterator chains (`.iter().map().filter().fold()`) over C-style index loops unless mutating multiple indices simultaneously.
4. **Type System**: Implement standard traits (`Default`, `Display`, `Debug`, `From`/`Into`, `TryFrom`, `AsRef`); use Newtypes to prevent primitive obsession and Typestates for compile-time state validity.
5. **Real-Time / DSP / `no_std`**: Zero heap allocations (`Vec::push`, `Box`, `String`), zero blocking locks/I/O, zero panics on audio/RT threads; pre-allocate at init and use lock-free atomics/SPSC rings.
6. **Lint Cleanliness**: `cargo clippy --all-targets -- -D warnings` must pass with 0 warnings; no undocumented `#[allow(...)]`.
7. **Strict TDD**: Always follow `Red -> Verify Failure -> Green -> Refactor`. Write deterministic synthetic boundary/edge tests first, verify failure, implement minimal idiomatic fix, then refactor. Return `Result` or use `assert!`/`assert_eq!`/`matches!` in tests.

## 2. Other Language Standards
- **TypeScript/JS**: Zero `any` (use `unknown` + guards), discriminated unions, `readonly`/`as const`, `const` over `let`/`var`, `?.`/`??`, immutable array methods, `async`/`await`.
- **Python**: Full PEP 484/585 type hints, comprehensions/generators, `with` context managers, `@dataclass(frozen=True)`/Pydantic, specific exception catching (never bare `except:`).
- **Modern C++ (17/20/23)**: Strict RAII (`unique_ptr`/`shared_ptr`, no naked `new`/`delete`), const-correctness, `string_view`/`span`, `std::move`.

## 3. Mandatory Phase 3 Merciless Local Audit (0 Tokens)
After EVERY code change, refactor, or bug fix (before committing), run an adversarial diff audit via `execute_local_task(role="inquisitor")` (skill `merciless-local-audit`). Any non-idiomatic construct, lazy `.clone()`, production `unwrap`/`expect`, race condition, missing test, or forbidden comment reference is `[BLOCKING]` and must be resolved before proceeding.

## 4. Git Commit Discipline & Two-Stage Consent Gates
1. **Atomic & Bisectable**: One logical change per commit; never mix fixes and features; every commit must independently compile and pass full CI/tests/linters.
2. **Docs Sync**: Update `README.md` / docs alongside any feature, config, hook, or API change.
3. **Zero Ephemeral References**: Code comments and commit messages MUST NEVER contain ticket/issue IDs (`JIRA-123`, `#42`, `E1`, `T-12`), phase/sprint/step/audit numbers (`Phase 16`, `Audit v8`, `Step 2`, `P0`), or plan IDs. Describe timeless domain intent only.
4. **Strict Two-Stage Gates (`ask_question`)** — NEVER bundle `git commit` and `git push`:
   - **Gate A (Local Commit)**: Display unified diff, test status, and proposed message, then invoke `ask_question`:
     - Question: `"Souhaitez-vous enregistrer ce commit atomique localement ?"`
     - Options: `["(Recommended) Oui, enregistrer le commit local (sans push)", "Ajuster le message ou le périmètre du commit", "Annuler / apporter d'autres modifications"]`
   - **Gate B (Remote Push — Dedicated Subsequent Turn)**: Only after local commit succeeds, invoke `ask_question`:
     - Question: `"Le commit local est enregistré. Souhaitez-vous pousser les modifications vers origin/main ?"`
     - Options: `["(Recommended) Non, conserver en local (préserve les quotas des runners CI)", "Oui, pousser vers origin/main"]`

## 5. Mandatory 5-Phase Tracking & Test Protocol
- Emit the phase banner **ONCE at each phase transition** as the first visible line (never repeat across intermediate tool calls within the same phase):
  1. `### [PHASE 1/5 : CONCEPTION & DÉVELOPPEMENT]` — Architecture, coding, idiomatic craft.
  2. `### [PHASE 2/5 : VALIDATION NATIVE & TESTS CI]` — Native toolchain (`cargo test && cargo clippy`, `tsc`, `pytest`).
  3. `### [PHASE 3/5 : AUDIT LOCAL IMPITOYABLE (0 TOKENS)]` — LM Studio diff audit (`role="inquisitor"`).
  4. `### [PHASE 4/5 : AUTO-HEALING & CORRECTIFS]` — Fix `[BLOCKING]` defects & re-audit, or confirm `No blocking defects detected (Local audit APPROVED on first pass, 0 fixes required)`.
  5. `### [PHASE 5/5 : RAPPORT DÉTAILLÉ & PROTOCOLE DE TEST]` — Final report before Gate A: clickable modified file links, local model verdict (`APPROVED`, 0 tokens), exact CI/test counts, automated CLI test commands, and step-by-step manual/visual verification scenario.

## 6. Mandatory Skill Triggers
- **New Project Scaffolding**: Before writing any domain code in a new repo (or one lacking `AGENTS.md` / `docs/agent/`), load skill `project-scaffold` to create the **6 mandatory files** (`AGENTS.md`, `docs/agent/{SESSION_STATE,TASKS,HANDOFF}.md`, `.gitignore`, `.geminiignore`) and commit atomically (`chore(scaffold): initialize project architecture and agent tracking state`).
- **Standardized Maturity Audit**: On any maturity audit request (*"lance un audit de maturité"*, *"maturity audit"*, *"audit complet"*), automatically load and execute skill `maturity-audit` without asking the user to specify criteria.
