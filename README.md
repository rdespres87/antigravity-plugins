# antigravity-plugins

A monorepo of custom plugins, always-on rules, and toolchains for Google Antigravity.

## Included Plugins

### `idiomatic-craft`
Global, language-agnostic code craftsmanship standards enforced across all projects:
- **Rust**: Zero lazy `.clone()`, zero `.unwrap()`/`.expect()` in production paths, iterator combinators over manual indexing, typestates & newtypes, `no_std` zero-allocation constraints.
- **TypeScript**: Zero `any` (unknown + type guards), strict discriminated unions, immutable readonly properties.
- **Python**: Full PEP 484/585 type hints, frozen dataclasses / Pydantic, context managers, targeted exception handling.
- **Modern C++**: Strict RAII (smart pointers, no raw new/delete), `std::string_view` / `std::span`, const correctness.

#### Governance & Lifecycle Protocol
- **Continuous 5-Phase Lifecycle**: Every task follows an explicit state machine with banners emitted once at each phase transition:
  1. `[PHASE 1/5 : CONCEPTION & DÉVELOPPEMENT]` : Architecture, strict TDD, and idiomatic craft.
  2. `[PHASE 2/5 : VALIDATION NATIVE & TESTS CI]` : Zero compiler warnings, 100% green native tests.
  3. `[PHASE 3/5 : AUDIT LOCAL IMPITOYABLE (0 TOKENS)]` : Adversarial audit by local offline model (LM Studio Qwen 35B) via `merciless-local-audit`.
  4. `[PHASE 4/5 : AUTO-HEALING & CORRECTIFS]` : Non-optional state confirming 0 defects if approved, or tracing resolution of blocking findings if rejected.
  5. `[PHASE 5/5 : RAPPORT DÉTAILLÉ & PROTOCOLE DE TEST]` : Final test commands and manual verification scenario before commit gates.
- **Zero Ephemeral Tracking References**: Code comments, docstrings, and Git commit messages must never contain ticket/issue numbers, phase/sprint/step numbers, or audit version labels.
- **Two-Stage Consent Gate (`ask_question`)**:
  - *Gate A (Local Commit)*: Interactive choice box to authorize local commit. Strict separation: `git push` is never bundled into this gate.
  - *Gate B (Remote Push)*: Separate subsequent turn requesting push permission, defaulting to keeping commits local to preserve CI runner quotas.
- **Mandatory New Project Scaffolding Protocol**: When bootstrapping or entering any workspace lacking agent state, the agent is strictly forbidden from writing domain code before establishing root `AGENTS.md` and `docs/agent/{SESSION_STATE,TASKS,HANDOFF}.md` (via skill `project-scaffold`).
- **Standardized Maturity Audit Protocol**: Automatically triggers the `maturity-audit` skill on any maturity audit request, enforcing 12 universal axes (including performance & bottlenecks) plus conditional domain packs (Real-Time Audio/DSP, Image/GPU/EDR, Desktop GUI/UX) with zero extra prompting.

## Installation

Symlink or copy plugins into your global Antigravity plugins directory:

```bash
ln -s "$(pwd)/plugins/idiomatic-craft" ~/.gemini/config/plugins/idiomatic-craft
```

## License

MIT
