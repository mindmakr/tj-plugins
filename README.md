# tj-plugins

The official package, plugin, bridge and compliance-pack registry for [tinyjump](https://github.com/mindmakr/tinyjump).

**[Browse & search the registry](https://mindmakr.github.io/tj-plugins/)** · `tj search` · `tj info <name>` · `tj add <name>`

Every package in `index/` is pinned to an exact 40-character git commit SHA and a SHA-256 content hash, and declares the permissions it uses (`network`, `plugins`, `pii`, `device`). `tj add` verifies both before installing into `.tj-packages/` and recording the pin in `tj.lock`.

## Installing packages

```sh
# Install a library and pin it in tj.lock
tj add money
tj add slug
tj add csv
tj add validate

# Require Verified or Official packages only
tj add money --min-trust verified

# Install a compliance pack (stored at .tj-packages/<name>/pack.json)
tj add gdpr-eu
tj add pdpl-sa
tj check app.tj --compliance .tj-packages/pdpl-sa/pack.json
```

In your app:

```ts
import { toMinor, splitMinor, formatMinor } from "tj:money"
import { slugify } from "tj:slug"
import { parseCsv, toCsv } from "tj:csv"
import { isEmail, isIban } from "tj:validate"
```

Run `tj check app.tj --min-trust verified` in CI to verify every locked package's SHA-256, declared permissions, advisory status and trust mark.

## Available packages

| Package | Kind | Trust | Summary |
|---|---|---|---|
| `money` | `library` | Official | Integer minor-unit currency arithmetic (`toMinor`, `fromMinor`, `splitMinor`, `formatMinor`) |
| `slug` | `library` | Official | Clean URL-safe ASCII slug helpers (`slugify`, `slugifyMax`) |
| `csv` | `library` | Official | RFC 4180 CSV parser and serializer (`parseCsv`, `toCsv`) |
| `validate` | `library` | Official | Deterministic validators (`isEmail`, `isPhone`, `isUuid`, `isHttpsUrl`, `isIban`) |
| `gdpr-eu` | `compliance-pack` | Official | Sample EU GDPR compliance pack for `tj check --compliance` and `tj serve --compliance` |
| `pdpl-sa` | `compliance-pack` | Official | Sample Saudi Arabia PDPL compliance pack for `tj check --compliance` and `tj serve --compliance` |

Built-in modules (`tj:ai`, `tj:agent`, `tj:ui`, `tj:privacy`, `tj:consent`) ship directly inside `tj-core` and cannot be shadowed in this index. A machine-readable summary index lives at [`catalog.json`](catalog.json).

## Trust marks

- **Community** — passed automated manifest, license, typosquat, compilation and permission checks. Community PRs may only submit `"trust": "community"`; maintainers promote packages to Verified or Official.
- **Verified** — checked by a reviewer; publisher identity verified; test suite passes cleanly.
- **Official** — maintained by `mindmakr` (`tinyjump` core team).

## Submitting a package

1. Host your `.tj` module or compliance-pack `.json` in a public HTTPS git repository under an OSI-approved license (`MIT`, `Apache-2.0`, `BSD-2-Clause`, `BSD-3-Clause`, `ISC`, `MPL-2.0`, `Unlicense`, `0BSD`, `CC0-1.0`, `Zlib`, `BSL-1.0`).
   - If contributing directly into `packages/<name>/` in this repository, merge the package commit to `main` first (do not squash-merge `packages/*` together with `index/*`, which would rewrite the pinned commit SHA).
2. Add `index/<name>.json` pinned to a full 40-character commit SHA and exact SHA-256 hash, and add its summary entry to `catalog.json`:

```json
{
  "name": "example-pkg",
  "kind": "library",
  "version": "1.0.0",
  "trust": "community",
  "source": "https://github.com/you/tj-example-pkg",
  "entry": "plugin.tj",
  "commit": "<40-char lowercase git sha>",
  "sha256": "<64-char lowercase sha256 of entry file>",
  "license": "MIT",
  "publisher": "your-github-handle",
  "permissions": {
    "network": [],
    "plugins": [],
    "pii": [],
    "device": []
  }
}
```

Valid `kind` values: `library`, `plugin`, `port`, `bridge`, `device-plugin`, `compliance-pack`.
For `"kind": "port"`, include `"port": { "of": "<upstream repo>", "tests": "<passing test summary>" }`.

3. Validate locally before opening a PR:

```sh
tj registry check index/example-pkg.json --index index --source path/to/plugin.tj
```

4. Open a pull request to this repository. The review workflow validates every manifest against its pinned commit and SHA-256 (and re-scans nightly), compiles `.tj` modules, checks for typosquats, enforces community trust-mark rules, and verifies that every `fetch()`, `plugin()` and `@pii(...)` call matches `permissions`.

## Requesting a package

Need a library, an agent port of an existing package, or an API/MCP bridge? Open an issue using the [request forms](requests/README.md).

## License

Dual-licensed under [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE). Keep [NOTICE](NOTICE) in copies and derivative works.
