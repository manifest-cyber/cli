# Generating a C/C++ SBOM with csbom

`csbom` is a generator purpose-built for C and C++ projects. It works at the build-system and
source level to surface what was actually compiled into a binary — components that standard
package-scanning tools cannot see.

## What csbom analyses

- **Build systems** — Makefile, CMake, Conan, vcpkg
- **Source files** — vendored libraries and in-tree dependencies
- **Header files** — third-party headers that indicate compiled-in components
- **Pre-built binaries** — `.dll` / `.lib` (Windows), `.so` (Linux), `.dylib` (macOS) shipped
  without source or a package manager, correlated against build metadata

This makes `csbom` especially effective for:

- Legacy C/C++ repositories that vendor their dependencies
- Embedded and Windows projects that depend on external SDKs installed on the build machine
- Projects that statically link third-party libraries (invisible to runtime scanners)

## Installing csbom

```bash
manifest-cli install -g csbom
```

## Generating a C/C++ SBOM

```bash
manifest-cli generate --generator csbom -f sbom.json ./my-cpp-project
```

This scans `./my-cpp-project`, analyses the build system and source tree, and writes a
CycloneDX JSON SBOM to `sbom.json`.

### Common flags

| Flag | Description |
| --- | --- |
| `-f`, `--file` | Output SBOM file path |
| `-o`, `--output` | Output format (`cyclonedx-json`, `spdx-json`). Default: `cyclonedx-json` |
| `--name` | Asset name to embed in the SBOM |
| `--version` | Asset version to embed in the SBOM |
| `--publish` | Publish the SBOM to the Manifest API after generation |
| `--api-key` | Manifest API key (or set `MANIFEST_API_KEY` env var) |

## Using csbom with the Workflow command

For C/C++ projects, running `csbom` as part of a multi-step workflow gives you the most complete
SBOM coverage. See the [Workflow documentation](workflow/WORKFLOW_FEATURE.md) for full details.

Quick start with the built-in `cpp` preset:

```bash
manifest-cli workflow --preset cpp -f merged.json ./my-cpp-project
```

This runs `csbom` and an additional generator in sequence, then merges the results into a single
CycloneDX 1.6 SBOM — with no workflow file needed.
