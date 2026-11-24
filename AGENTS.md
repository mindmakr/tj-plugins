# Working in `tj-plugins`

Companion package, plugin, bridge, and compliance-pack registry for [`tinyjump`](https://github.com/mindmakr/tinyjump) (`d:\projects\mindmakr\tinyjump`).

## Rules

- **Two-commit pinning rule:** commit package sources under `packages/<name>/` to `main` **first** (`git commit`), capture the exact 40-character commit SHA (`git rev-parse HEAD`) and exact lowercase SHA-256 of the entry file, and then add/update `index/<name>.json` in a **second** commit. Never squash-merge `packages/*` together with `index/*`, because squashing rewrites the commit SHA and breaks `REG-1` / `tj add`.
- **Validate locally before committing or pushing:** run `tj registry check` against every changed manifest:
  ```sh
  tj registry check index/<name>.json --index index --source packages/<name>/plugin.tj
  ```
- **Trust-mark gating (`REG-2`):** community PRs may only set `"trust": "community"`; maintainers promote packages to `"verified"` or `"official"`.
- **Clean 2-space JSON:** keep `index/*.json` and `catalog.json` formatted cleanly (2-space indentation, sorted/consistent keys, no trailing spaces).
- **Parity across surfaces:** keep `index/*.json`, `catalog.json`, `README.md` (Available packages table), and `docs/index.html` in sync whenever packages are added or bumped.
- **No built-in shadowing:** built-in modules (`tj:ai`, `tj:agent`, `tj:ui`, `tj:privacy`, `tj:consent`) live in `tinyjump`'s `tj-core` (`crates/tj-core/src/modules.rs`) and must never be shadowed here.
- **Permission parity:** every `fetch()`, `plugin()`, `@pii(...)`, and device capability (`camera`, `microphone`, `location`, `files`, `notifications`) must be declared under `permissions` in `index/<name>.json` and `catalog.json`.
