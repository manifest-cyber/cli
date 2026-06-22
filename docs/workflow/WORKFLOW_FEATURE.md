# Workflow Feature

Execute a sequence of `manifest-cli` commands defined in a JSON file or via a built-in preset.
Each step maps directly to an existing command (`generate`, `merge`, `publish`, `install`) using
the same flags as the CLI. No new syntax to learn.

## Modes

### JSON workflow file

```bash
manifest-cli workflow --workflow-file workflow.json [input-path]
manifest-cli workflow -w workflow.json [input-path]
```

Reads a JSON file and executes its `action` steps in order.

The optional `[input-path]` positional argument sets the **default scan target** for every
`generate` step that has no `"input"` flag. If any `generate` step needs an input and none is
available, the command fails with a descriptive error **before any step runs**.

### Built-in preset

```bash
manifest-cli workflow --preset cpp -f <output-path> <input-path>
```

Runs a built-in workflow without a JSON file. The `cpp` preset runs `syft` + `csbom` + `merge`
in sequence, producing a single **CycloneDX 1.6** SBOM. Requires `--file` and `[input-path]`.

Available presets: `cpp`

---

## Quick Start

No JSON file needed — one command for C/C++ projects:

```bash
manifest-cli workflow --preset cpp -f merged.json ./my-project
```

---

## Scenarios

### 1. syft + csbom → merge (explicit inputs)

**File:** [`examples/workflow-syft-csbom-merge.json`](examples/workflow-syft-csbom-merge.json)

Each `generate` step has its own `"input"` path. Suitable when both generators scan the same
directory and you want the workflow file to be self-contained.

```json
{
  "action": [
    {
      "command": "generate",
      "generator": "syft",
      "stop_on_error": true,
      "flags": {
        "input": "./my-project",
        "output": "cyclonedx-json",
        "file": "/tmp/sbom-syft.json"
      }
    },
    {
      "command": "generate",
      "generator": "csbom",
      "stop_on_error": true,
      "flags": {
        "input": "./my-project",
        "output": "cyclonedx-json",
        "file": "/tmp/sbom-csbom.json"
      }
    },
    {
      "command": "merge",
      "stop_on_error": true,
      "flags": {
        "input_files": ["/tmp/sbom-syft.json", "/tmp/sbom-csbom.json"],
        "file": "merged.json",
        "output": "cyclonedx-1.6"
      }
    }
  ]
}
```

```bash
manifest-cli workflow --workflow-file workflow-syft-csbom-merge.json
```

---

### 2. syft + csbom → merge (default input from CLI)

**File:** [`examples/workflow-default-input.json`](examples/workflow-default-input.json)

No `"input"` in the workflow file. The scan target is passed once on the CLI and shared
automatically across all `generate` steps. Useful when the same project is scanned by multiple
generators and you want the workflow file to remain reusable across different paths.

```json
{
  "action": [
    {
      "command": "generate",
      "generator": "syft",
      "stop_on_error": true,
      "flags": { "output": "cyclonedx-json", "file": "/tmp/sbom-syft.json" }
    },
    {
      "command": "generate",
      "generator": "csbom",
      "stop_on_error": true,
      "flags": { "output": "cyclonedx-json", "file": "/tmp/sbom-csbom.json" }
    },
    {
      "command": "merge",
      "stop_on_error": true,
      "flags": {
        "input_files": ["/tmp/sbom-syft.json", "/tmp/sbom-csbom.json"],
        "file": "merged.json",
        "output": "cyclonedx-1.6"
      }
    }
  ]
}
```

```bash
manifest-cli workflow --workflow-file workflow-default-input.json ./my-project
```

---

### 3. Multiple scan targets → single merged SBOM

**File:** [`examples/workflow-multi-target.json`](examples/workflow-multi-target.json)

Covers projects that span multiple directories: main source code, external vendor SDKs installed
on the machine, or legacy libraries on a network share. Each location is a separate `generate`
step. All results are merged into one SBOM at the end.

```json
{
  "action": [
    {
      "command": "generate",
      "generator": "syft",
      "stop_on_error": true,
      "flags": { "input": "/opt/vendor-sdk-a", "output": "cyclonedx-json", "file": "/tmp/sbom-vendor-a.json" }
    },
    {
      "command": "generate",
      "generator": "syft",
      "stop_on_error": true,
      "flags": { "input": "/opt/vendor-sdk-b", "output": "cyclonedx-json", "file": "/tmp/sbom-vendor-b.json" }
    },
    {
      "command": "generate",
      "generator": "csbom",
      "stop_on_error": true,
      "flags": { "input": "./my-project", "output": "cyclonedx-json", "file": "/tmp/sbom-project.json" }
    },
    {
      "command": "merge",
      "stop_on_error": true,
      "flags": {
        "input_files": ["/tmp/sbom-vendor-a.json", "/tmp/sbom-vendor-b.json", "/tmp/sbom-project.json"],
        "file": "merged-full.json",
        "output": "cyclonedx-1.6"
      }
    }
  ]
}
```

```bash
manifest-cli workflow --workflow-file workflow-multi-target.json
```

---

### 4. Continue on generate failure, stop on merge failure

**File:** [`examples/workflow-stop-on-error.json`](examples/workflow-stop-on-error.json)

`stop_on_error: false` on the `generate` steps means a failing generator is logged and skipped
rather than aborting the whole workflow. The `merge` step uses `stop_on_error: true` so that a
broken merge is always fatal. At the end, if any steps failed, an aggregate error listing all
failures is returned.

```json
{
  "action": [
    {
      "command": "generate",
      "generator": "syft",
      "stop_on_error": false,
      "flags": { "input": "./my-project", "output": "cyclonedx-json", "file": "/tmp/sbom-syft.json" }
    },
    {
      "command": "generate",
      "generator": "csbom",
      "stop_on_error": false,
      "flags": { "input": "./my-project", "output": "cyclonedx-json", "file": "/tmp/sbom-csbom.json" }
    },
    {
      "command": "merge",
      "stop_on_error": true,
      "flags": {
        "input_files": ["/tmp/sbom-syft.json", "/tmp/sbom-csbom.json"],
        "file": "merged.json",
        "output": "cyclonedx-1.6"
      }
    }
  ]
}
```

```bash
manifest-cli workflow --workflow-file workflow-stop-on-error.json
```

| `stop_on_error` value | Behaviour on failure |
| --- | --- |
| `true` (default when absent) | Halt immediately, return step index and error |
| `false` | Log error, continue to next step; aggregate error returned at the end |

---

### 5. Full pipeline: generate → merge → publish

**File:** [`examples/workflow-full-pipeline.json`](examples/workflow-full-pipeline.json)

Extends the core use case with a `publish` step that uploads the merged SBOM to the Manifest API.
The API key is read from the `MANIFEST_API_KEY` environment variable when `"api-key"` is omitted
from the flags.

```json
{
  "action": [
    {
      "command": "generate",
      "generator": "syft",
      "stop_on_error": true,
      "flags": { "input": "./my-project", "output": "cyclonedx-json", "file": "/tmp/sbom-syft.json" }
    },
    {
      "command": "generate",
      "generator": "csbom",
      "stop_on_error": true,
      "flags": { "input": "./my-project", "output": "cyclonedx-json", "file": "/tmp/sbom-csbom.json" }
    },
    {
      "command": "merge",
      "stop_on_error": true,
      "flags": {
        "input_files": ["/tmp/sbom-syft.json", "/tmp/sbom-csbom.json"],
        "file": "/tmp/sbom-merged.json",
        "output": "cyclonedx-1.6"
      }
    },
    {
      "command": "publish",
      "stop_on_error": true,
      "flags": {
        "input_files": ["/tmp/sbom-merged.json"],
        "name": "my-project",
        "version": "v1.0.0",
        "product-id": "00000000-0000-0000-0000-000000000000",
        "active": "true",
        "deactivate-older": true,
        "asset-label": ["team:platform"],
        "label": ["release:stable"]
      }
    }
  ]
}
```

```bash
export MANIFEST_API_KEY=your-api-key
manifest-cli workflow --workflow-file workflow-full-pipeline.json
```

---

## Workflow JSON Structure

```json
{
  "action": [
    {
      "command": "generate | merge | publish | install",
      "generator": "syft | csbom",
      "stop_on_error": true,
      "flags": { }
    }
  ]
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `command` | string | yes | One of `generate`, `merge`, `publish`, `install` |
| `generator` | string | no | Generator for `generate` steps (`syft`, `csbom`). Takes precedence over `flags.generator` |
| `stop_on_error` | bool | no | Defaults to `true` when absent |
| `flags` | object | no | Command-specific flags (keys match CLI flag names, hyphens not underscores) |

---

## Environment Variables

| Variable | Description |
| --- | --- |
| `MANIFEST_API_KEY` | Manifest API key — used by `generate`, `merge`, and `publish` when `"api-key"` is not set in flags |
| `OPENAI_API_KEY` | OpenAI API key for AI detection features in `generate` |

---

## Reference

- Example workflow files for each scenario: [`examples/`](examples/)
