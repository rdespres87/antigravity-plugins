# Global Idiomatic Code & Craftsmanship Standards (always-on)

All generated, refactored, or reviewed code MUST be strictly idiomatic to the target programming language.
Writing "transliterated" code (e.g. C-style code written in Rust, Java-style classes written in Python, non-typed JavaScript written in TypeScript) is strictly forbidden. Code must adhere to modern community idioms, standard library traits, and zero-warning compiler/linter standards.

---

## 1. Rust Standards (Core Focus)

1. **Ownership, Borrowing & Zero Lazy Clones**:
   - Never use `.clone()` to appease the borrow checker unless the clone is computationally trivial and explicitly semantically intended.
   - Prefer references and slices (`&str`, `&[T]`, `&mut [T]`) over owning collections in function parameters.
   - Use `Cow<'a, T>` when ownership is conditionally required.
   - Leverage RAII: resources (locks, files, sockets, memory) must clean themselves up on drop.

2. **Error Handling**:
   - **Zero unwrap/expect in production code**: Never call `.unwrap()` or `.expect()` in non-test paths unless accompanied by a comment proving an invariant that makes panic mathematically impossible.
   - Propagate errors idiomatically with the `?` operator.
   - Use strongly-typed, domain-specific error enums (with `thiserror`) for libraries/crates, or `anyhow` for top-level CLI/application runners.
   - Prefer functional combinators (`.map()`, `.and_then()`, `.unwrap_or_else()`, `.ok_or()`) over nested `match` or `if let` ladders.

3. **Iterators over Indexing**:
   - Never write C-style indexed loops (`for i in 0..vec.len() { vec[i] }`) unless required for simultaneous multi-index mutation.
   - Use iterator chains (`.iter()`, `.map()`, `.filter()`, `.fold()`, `.collect()`). They eliminate bounds checking overhead and express intent clearly.

4. **Type System & Standard Traits**:
   - Implement standard traits idiomatically: `Default`, `Display`, `Debug`, `From<T>`/`Into<T>`, `TryFrom<T>`, `AsRef<T>`. Never write ad-hoc `.to_xyz()` methods when a standard trait exists.
   - Avoid "Primitive Obsession": use the **Newtype pattern** (e.g., `struct SampleRate(f64);`) to prevent parameter swapping bugs at compile time.
   - Use the **Typestate pattern** for stateful workflows (e.g. `Builder<Unconfigured> -> Builder<Ready>`) to enforce state validity at compile time.

5. **Real-time, Audio DSP & `no_std` Constraints**:
   - In real-time audio threads or `no_std` crates: **zero dynamic memory allocations** (`Vec::push`, `Box::new`, `String`), zero blocking locks/mutexes, zero I/O, zero panicking paths. Pre-allocate buffers at initialization.
   - Adhere to lock-free or ring-buffer synchronization primitives (e.g., atomic operations, SPSC queues).

6. **Compiler & Linter Cleanliness**:
   - Code must pass `cargo clippy --all-targets -- -D warnings` with zero warnings.
   - Never silence linter warnings with `#[allow(...)]` without a clear, documented rationale.

7. **Strict Test-Driven Development (TDD) Mandate**:
   - Every feature, endpoint, algorithm, and bug fix MUST follow strict TDD: `Red -> Verify Failure -> Green -> Refactor`.
   - **Step 1 (Red)**: Write unit/integration tests with deterministic synthetic fixtures (boundary cases, edge values, known ground-truth inputs) BEFORE writing production code.
   - **Step 2 (Verify Failure)**: Run tests to confirm they fail for the intended reason.
   - **Step 3 (Green)**: Implement the minimal, clean, idiomatic code to make the tests pass.
   - **Step 4 (Refactor)**: Clean up duplication, sharpen types, ensure zero allocations/panics, and verify zero compiler/linter warnings.
   - In tests: return `Result<(), ...>` using the `?` operator or use `assert!`, `assert_eq!`, `matches!`. Avoid `.unwrap()` / `.expect()` in test bodies where possible.

---

## 2. TypeScript / JavaScript Standards

1. **Strict Typing & Zero Any**:
   - `any` is strictly prohibited. Use `unknown` combined with type guards or assertion functions.
   - Use **discriminated unions** (tagged unions) for state machines and action payloads rather than loose optional fields.
   - Prefer `readonly` arrays/properties and `as const` assertions for fixed literals.

2. **Modern Idiomatic Syntax**:
   - Use optional chaining (`?.`), nullish coalescing (`??`), and destructuring idiomatically.
   - Never use old `var` or unneeded `let` (default to `const`).
   - Use pure, immutable array methods (`.map()`, `.filter()`, `.reduce()`, `.find()`) over manual mutation loops.
   - Use `async`/`await` over raw promise `.then()` chaining.

---

## 3. Python Standards

1. **Pythonic & PEP 8**:
   - Comprehensive type hints (PEP 484/PEP 585) on all function arguments and return types.
   - Use list/dict/set comprehensions and generator expressions rather than procedural loops with `.append()`.
   - Use **Context Managers** (`with`) for any resource handling (files, sockets, database transactions, locks).
   - Use `@dataclass(frozen=True)` or Pydantic models for structured data instead of untyped dictionaries or tuples.
   - EAFP (*Easier to Ask for Forgiveness than Permission*) with specific exception catching; never use bare `except:`.

---

## 4. Modern C / C++ Standards

1. **Modern C++ (C++17/20/23)**:
   - Strict RAII: use `std::unique_ptr` and `std::shared_ptr`. Naked `new` and `delete` are strictly forbidden.
   - Enforce const-correctness everywhere.
   - Prefer `std::string_view` and `std::span` over raw pointers and length parameters.
   - Use move semantics (`std::move`) to avoid expensive copying.

---

## 5. Enforcement Across Tiers & Adversarial Review

- **Tier 1 (Local Model)**: When generating code via `execute_local_task`, the prompt must include language idiomaticity constraints. Any clunky construct must be caught in the auto-healing loop.
- **Mandatory Merciless Local Audit**: After EVERY code development, refactoring, or bug fix across any project, the agent MUST invoke the local model (LM Studio via `execute_local_task` at 0 cloud tokens) for an adversarial, merciless audit (`merciless-local-audit` skill). The local auditor aggressively flags non-idiomatic patterns (lazy `.clone()`, `unwrap`/`expect` in production paths, C-style manual indexing, non-strict types, hidden allocations) as **blocking defects** that must be resolved before proceeding.
- **Grill Inquisitor**: The Inquisitor must flag non-idiomatic code, archaic syntax, code smells, or unnecessary copies/allocations as **blocking defects**, forcing the Maker to rewrite.

---

## 6. Git Commit Discipline & Atomic Quality Gates (Mandatory across all projects)

1. **Atomic & Single-Responsibility Commits**:
   - Every commit must represent a single, focused, logical change.
   - **Strict separation of concerns**: NEVER mix a bug fix with a new feature, and never bundle multiple unrelated fixes or features together.
   - Refactorings, documentation updates, and functional changes must be isolated in distinct, self-contained commits.

2. **Bisectability & Compile Guarantee**:
   - Every single commit MUST compile cleanly and pass the full test suite independently (`git bisect`-ready).
   - Broken intermediate commits are strictly forbidden, even on experimental or feature branches.

3. **Pre-Commit CI Validation**:
   - Before crafting any commit, run the full native verification toolchain (`cargo test && cargo clippy`, `npm test && tsc`, `pytest`, etc.).
   - Zero compiler errors, zero linter warnings.

4. **Pre-Commit README & Documentation Sync**:
   - Before committing any new capability, configuration option, hook, or API change, evaluate and update the relevant `README.md` and docs within the same logical scope. Documentation is never an afterthought; it lands alongside the code it describes.

5. **Explicit Human-in-the-Loop Consent (Zero Rogue Commits / Pushes)**:
   - You are strictly FORBIDDEN from running `git commit` or `git push` autonomously.
   - Before every commit: display the unified diff, propose a clear atomic commit message, and wait for explicit user approval.
   - Before every `git push`: request explicit remote push authorization.

---

## 7. Mandatory Phase Tracking & Detailed Test Protocol Reporting

Across EVERY task (feature development, refactoring, or bug fix), the agent MUST explicitly trace progress using clear phase banners and provide a comprehensive test protocol:

1. **Explicit Phase Banners (MANDATORY FIRST-TOKEN OUTPUT BEFORE ANY TOOL CALL)**:
   - **Zero Silent Tool Calls**: You are strictly forbidden from calling tools (`read_file`, `edit_file`, `run_command`, etc.) silently with empty prose.
   - **First Visible Markdown Token**: At the very start of every turn and **BEFORE invoking any tool**, you MUST output visible text in the chat declaring the active phase banner in Markdown:
     `### [PHASE 1/5 : CONCEPTION & DÉVELOPPEMENT]` (or active phase `[PHASE 2/5]`, `[PHASE 3/5]`, etc.)
     followed by a one-sentence summary of the action being taken.
   - The 5 phases:
     - `### [PHASE 1/5 : CONCEPTION & DÉVELOPPEMENT]` : Architecture, coding, strict idiomatic craft.
     - `### [PHASE 2/5 : VALIDATION NATIVE & TESTS CI]` : Native toolchain verification (`cargo test && cargo clippy`, `tsc`, `pytest`).
     - `### [PHASE 3/5 : AUDIT LOCAL IMPITOYABLE (0 TOKENS)]` : Adversarial audit of `git diff` via LM Studio (`execute_local_task`).
     - `### [PHASE 4/5 : AUTO-HEALING & CORRECTIFS]` : *(If rejected)* Resolving `[BLOCKING]` defects and re-auditing.
     - `### [PHASE 5/5 : RAPPORT DÉTAILLÉ & PROTOCOLE DE TEST]` : Final restitution before commit gate.

2. **Standardized Implementation Report**:
   Every completion turn MUST provide:
   - **Modified Files**: Clickable links (`file://...`) and exact responsibilities changed.
   - **Local Audit Verdict**: Name of the active local model, token cost (0 tokens), and `APPROVED` status.
   - **CI Status**: Exact number of unit/integration tests passing and confirmation of zero linter warnings.

3. **Reproducible Test Protocol for the User**:
   The agent MUST provide actionable instructions explaining how the user can independently verify the changes:
   - **Automated CLI Test Commands**: Exact commands to run targeted tests and the full suite.
   - **Manual / Visual Step-by-Step Scenario**: Specific UI steps, edge-case values to input, and expected visual/functional outcomes vs failure modes prevented.
