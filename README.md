# Case Brief

> Pedagogical framing + expected document pack manifest

**LegalBlocks module package** `bluelab.case-brief` v0.1.0

- **Execution**: frontend (provides screen `bluelab.case`)
- **Entrypoint**: `CaseBrief.vue`
- **Inputs**: none
- **Outputs**: `bluelab.case_brief`
- **Artifact I/O**: via [`@legalblocks/module-runtime`](../module-runtime) (configurable adapter: localStorage by default, HTTP endpoint when configured)

## Configuration

| name | type | label | default |
|---|---|---|---|
| `scenario_id` | enum | Scenario | `"maritime-msp-deme"` |

## Registering with LegalBlocks

This module is a standalone, publishable package. To register it:

1. `npm publish` (or push to its own git repo).
2. Add the package to a LegalBlocks builder project; the builder reads `package.manifest.json` and exposes the inputs, outputs, and configuration above.

## Local development

This package depends on unpublished workspace packages (`@legalblocks/*`, `@lawnotation/*`). Until they are published, link them locally:

```bash
npm link @legalblocks/module-runtime   # if this module does artifact I/O
npm install
# no tests in this module
```
