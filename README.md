# antigravity-plugins

A monorepo of custom plugins, always-on rules, and toolchains for Google Antigravity.

## Included Plugins

### `idiomatic-craft`
Global language-agnostic code craftsmanship standards:
- **Rust**: Zero lazy `.clone()`, zero `.unwrap()` in production paths, iterator combinators over indexing, typestates & newtypes, `no_std` zero-allocation constraints.
- **TypeScript**: Zero `any` (unknown + type guards), strict discriminated unions, immutable readonly properties.
- **Python**: Full PEP 484/585 type hints, frozen dataclasses / Pydantic, context managers, targeted exception handling.
- **Modern C++**: Strict RAII (smart pointers, no raw new/delete), `std::string_view` / `std::span`, const correctness.

## License

MIT
