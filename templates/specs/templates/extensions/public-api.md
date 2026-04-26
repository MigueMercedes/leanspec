## API contract

> Extension: public-api. Enabled for libraries, SDKs, or services with a public API contract under semver.

- **Surface affected**: which exported functions / classes / types / endpoints / CLI commands change
- **Semver classification**: major (breaking) / minor (additive) / patch (internal). Justify the choice.
- **Breaking changes** (if any): exhaustive list with before → after, plus migration path users follow
- **Deprecation policy**: if removing/renaming, what's the deprecation timeline? Warning emitted? Documented in CHANGELOG?
- **Compatibility matrix**: which versions of your package are compatible with which versions of consumers/peers (if applicable, e.g. peer dependencies)
- **TypeScript / type definitions**: exported types updated. Type tests added (e.g. `tsd`, `expect-type`) if breaking.
- **CHANGELOG entry**: what to write under [Unreleased]
