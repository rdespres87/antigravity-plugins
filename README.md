# antigravity-plugins

A monorepo of custom plugins, always-on rules, and toolchains for Google Antigravity.

## Included Plugins

### `idiomatic-craft`
Global, language-agnostic code craftsmanship standards enforced across all projects:
- **Rust**: Zero lazy `.clone()`, zero `.unwrap()`/`.expect()` in production paths, iterator combinators over manual indexing, typestates & newtypes, `no_std` zero-allocation constraints.
- **TypeScript**: Zero `any` (unknown + type guards), strict discriminated unions, immutable readonly properties.
- **Python**: Full PEP 484/585 type hints, frozen dataclasses / Pydantic, context managers, targeted exception handling.
- **Modern C++**: Strict RAII (smart pointers, no raw new/delete), `std::string_view` / `std::span`, const correctness.

#### Enforcement
- **Mandatory Merciless Local Audit**: After **every** development, refactoring, or bug fix, the agent automatically triggers an adversarial audit on the local model (LM Studio via `execute_local_task` at 0 cloud tokens) via the `merciless-local-audit` skill. Code smells, non-idiomatic patterns, and missing tests are flagged as blocking defects before commit.

## Installation

Symlink or copy plugins into your global Antigravity plugins directory:

```bash
ln -s "$(pwd)/plugins/idiomatic-craft" ~/.gemini/config/plugins/idiomatic-craft
```

## License

MIT
