# manifest-cli Command Arguments & Flags

This document provides a comprehensive reference of all command-line flags and arguments supported by the manifest-cli tool.

## Table of Contents

- [Global Flags](#global-flags)
- [Generate Command](#generate-command)
- [Publish Command](#publish-command)
- [Merge Command](#merge-command)
- [Convert Command](#convert-command)
- [Install Command](#install-command)
- [CRAT Command (Assess Reachability)](#crat-command-assess-reachability)

Notes for all commands:
- All boolean flags default to `false` unless otherwise specified
- String flags without defaults are optional unless marked as required in validation
- Flags marked as "Experimental" are available but not shown in `--help` output (typically for advanced use or internal testing), and may be subject to change or removal without deprecation
- Some flags may require other flags to be set (e.g., `--product-label` requires `--product-id`)

---

## Global Flags

These flags are available for all commands.

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--config` | `-c` | string | - | Path to config file |
| `--verbose` | `-v` | count | 0 | Log verbosity level (`-v`=info, `-vv`=debug, `-vvv`=trace) |

---

## Generate Command

Generate SBOMs from local filesystems or containers.

**Usage:** `manifest-cli generate [path] [flags]`

### Generation Flags

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--name` | `-n` | string | - | Name of generated SBOM document |
| `--version` | - | string | - | Version of generated SBOM document |
| `--file` | `-f` | string | - | Name of generated file (no extension) |
| `--generator` | `-g` | string | `syft` | Name of generator to use: `syft`, `trivy`, `cdxgen`, `docker-sbom`, `spdx-sbom-generator`, `sigstore-sbom` |
| `--output` | `-o` | string | `cyclonedx-json` | SBOM output format: `spdx-json`, `cyclonedx-json` |
| `--generator-preset` | - | string | `recommended` | Set generator config preset: `recommended`, `none` |
| `--generator-config` | - | string | - | Path to generator config file (if applicable) |
| `--hierarchical` | - | bool | `true` | Perform a hierarchical merge |
| `--use-tmp-sbom` | - | bool | `true` | Write temporary SBOMs to OS-specific temporary folder and delete after merge |
| `--keep-clone` | - | bool | `false` | Keep the local git clone once generation is completed |

### Publishing Integration

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--publish` | - | bool | `false` | Send the SBOM to your Manifest app tenant (requires API token) |

When `--publish` is enabled, all [Publish Command](#publish-command) flags become available. Generator passthrough arguments (using `--`) are also supported when publishing.

### Signing & Attestation

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--attest` | - | bool | `false` | Sign the SBOM and create an in-toto compliant attestation (requires private key) |
| `--key` | - | string | - | Path to the private key file |

### AI Detection (Experimental)

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--detect-ai` | - | bool | `false` | Scan for AI models in source code and include them in the SBOM (only supported with `cyclonedx-json` format) |
| `--detect-ai-rules` | - | string | - | Path to custom rules file for AI model scanning |

### BOM Doctor LLM Enrichment

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--openai-api-key` | - | string | `$OPENAI_API_KEY` | OpenAI API key for BOM Doctor LLM-based ecosystem detection (improves SBOM recovery rate) |

### Dependency Management

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--install-dependencies` | - | bool | `false` | Install dependencies required to run your command (generator, static analysis, etc.) |

### Generator Passthrough Arguments

You can pass additional arguments directly to the underlying SBOM generator by using the `--` separator at the end of your command. Any arguments after `--` will be passed through to the generator.

**Syntax:** `manifest-cli generate [paths] [flags] -- [generator-specific-args]`

**Example:**
```bash
# Pass --type java to cdxgen
manifest-cli generate ./my-java-project --generator=cdxgen --output=cyclonedx-json -- --type java
```

### Experimental/Internal Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--tmp-path` | string | - | Path to write temporary SBOMs to |
| `--fail-on-metadata` | bool | `false` | Fail if metadata cannot be extracted from the image (experimental) |

---

## Publish Command

Publish SBOM(s) to the Manifest platform.

**Usage:** `manifest-cli publish [sbom-files...] [flags]`

### Authentication & API

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--api-key` | `-k` | string | `$MANIFEST_API_KEY` | API key from Manifest App (overrides `MANIFEST_API_KEY` env variable) |
| `--api-uri` | - | string | `https://api.manifestcyber.com/v1` | URI for the Manifest API (for self-hosted environments) |

### SBOM Configuration

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--active` | - | string | - | Whether this SBOM should be marked as active (`true`/`false`) |
| `--relationship` | - | string | `first` | Set the relationship of the SBOM(s) |
| `--hidden` | - | bool | `false` | Hide assets and components |
| `--ignore-validation` | `-s` | bool | `false` | Ignore validation of the SBOM(s) |

### Product & Labeling

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--product-id` | `-P` | string | - | ID of the product to associate the SBOM(s) with |
| `--asset-label` | - | []string | - | Add labels to the associated asset of the SBOM(s) |
| `--product-label` | - | []string | - | Add labels to the associated product of the SBOM(s) (requires `--product-id`) |

### Enrichment

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--enrich` | `-e` | string | - | Enrichment to apply to the SBOM(s): `ECOSYSTEMS`, `PARLAY` (optional if enabled for your organization) |

### Asset Management

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--deactivate-older` | `-d` | bool | `false` | Mark previous versions of this asset as inactive when publishing this SBOM |

### VDR (Vulnerability Disclosure Report)

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--download-vdr` | - | bool | `false` | Automatically wait for vulnerability scanning to complete and download the VDR file |
| `--vdr-output` | - | string | `vdr.json` | Specify the output path for the VDR file |

> **Note:** VDR download requires `--api-key` and will wait up to 30 minutes for vulnerability scanning to complete.

### Hidden/Internal Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--source` | string | `manifest-cli:publish` | Passthrough source string to the Manifest API (advanced) |
| `--git-url` | string | - | Git URL for the repository the SBOM was generated from |
| `--sync` | bool | `false` | Force the use of the synchronous upload endpoint |

### Deprecated Flags

| Flag | Short | Replacement | Note |
|------|-------|-------------|------|
| `--label` | `-l` | `--asset-label` | Use `--asset-label` instead |
| `--paths` | `-p` | Positional arguments | Use positional arguments instead |

---

## Merge Command

Merge two or more SBOMs into a single SBOM.

**Usage:** `manifest-cli merge [sbom-files...] [flags]`

### Merge Configuration

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--file` | `-f` | string | - | Name of merged file (extension added based on output format) |
| `--output` | `-o` | format | First detected format | Merged output format: `spdx`, `spdx-json`, `cyclonedx`, `cyclonedx-json`, `cyclonedx-1.0` through `cyclonedx-1.6` |
| `--hierarchical` | - | bool | `true` | Perform a hierarchical merge |
| `--fallback-to-flat` | - | bool | `false` | If the hierarchical merge fails, fallback to a flat merge |
| `--experimental-format-conversion` | - | bool | `false` | Use conversion if the format of the SBOMs don't match each other or the output format |

### Merged SBOM Metadata

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--name` | - | string | - | Name of merged SBOM document |
| `--version` | - | string | - | Version of merged SBOM document |
| `--group` | - | string | - | Group of merged SBOM document |

### Signing & Publishing

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--attest` | - | bool | `false` | Sign the SBOM and create an in-toto compliant attestation (requires private key) |
| `--key` | - | string | - | Path to the private key file |
| `--publish` | - | bool | `false` | Send the SBOM to your Manifest app tenant (requires API token) |

When `--publish` is enabled, all [Publish Command](#publish-command) flags become available.

---

## Convert Command

Convert SBOMs between different formats and versions.

**Usage:** `manifest-cli convert [sbom-files...] [flags]`

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--format` | `-f` | string | - | Output format: `spdx`, `spdx-2.3`, `cyclonedx`, `cyclonedx-1.0` through `cyclonedx-1.6` |
| `--output` | `-o` | []string | - | Path to write the converted SBOM(s) (can be used multiple times or comma-separated) |

### Hidden Flags

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--encoding` | `-e` | string | `json` | Output encoding: `json`, `text` (SPDX only) |

---

## Install Command

Install SBOM generators and related tools.

**Usage:** `manifest-cli install [flags]`

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--generator` | `-g` | string | `syft` | Name of generator to install: `syft`, `trivy`, `cdxgen`, `docker-sbom`, `spdx-sbom-generator`, `sigstore-sbom`, `bomdoctor` |
| `--version` | - | string | Latest | Install specific version |
| `--destination` | `-d` | string | `/usr/local/bin` | Installation destination path |

### Hidden Flags

| Flag | Type | Default | Description |
|------|------|---------|-------------|
| `--shell` | string | `bash` | Shell to use for installation |

---

## CRAT Command (Assess Reachability)

Run CRAT reachability assessments or execute CRAT subcommands.

**Usage:** `manifest-cli crat [path] [flags]`  
**Aliases:** `assess-reachability`

> **Note:** This command is currently hidden and provides both a manifest-enhanced workflow and passthrough mode for native CRAT commands.

### CRAT Configuration

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--entrypoint` | `-e` | string | - | Entrypoint file for the analysis (e.g., `src/index.ts`) |

### LLM Options

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--llm` | - | string | - | LLM provider/model for CRAT assess (e.g., `openai/gpt-4`, `ollama/mistral`) |
| `--llm-api-key` | - | string | - | API key for the LLM provider |
| `--llm-base-url` | - | string | - | Base URL for the LLM provider API |

### Authentication & API

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--api-key` | `-k` | string | `$MANIFEST_API_KEY` | API key from Manifest App (overrides `MANIFEST_API_KEY` env variable) |
| `--api-uri` | - | string | `https://api.manifestcyber.com/v1` | URI for the Manifest API (self-hosted, etc environments will want to change this) |

### Generator Options

| Flag | Short | Type | Default | Description |
|------|-------|------|---------|-------------|
| `--generator` | `-g` | string | `syft` | Name of generator to use: `syft`, `trivy`, `cdxgen`, `docker-sbom`, `spdx-sbom-generator`, `sigstore-sbom` |
| `--enrich` | - | string | - | Enrichment to apply to the SBOM: `ECOSYSTEMS`, `PARLAY` |
| `--openai-api-key` | - | string | `$OPENAI_API_KEY` | OpenAI API key for BOM Doctor LLM-based ecosystem detection |
| `--install-dependencies` | - | bool | `false` | Install dependencies required to run the command |

---

## Environment Variables

The following environment variables are recognized by manifest-cli:

| Variable | Description | Used By |
|----------|-------------|---------|
| `MANIFEST_API_KEY` | Default API key for Manifest platform authentication | `publish`, `generate --publish`, `merge --publish`, `crat` |
| `OPENAI_API_KEY` | Default OpenAI API key for BOM Doctor enrichment | `generate`, `crat` |

---

## Examples

### Self-Hosted Environment

```bash
# Publish to self-hosted Manifest instance
manifest-cli publish sbom.json \
  --api-key YOUR_API_KEY \
  --api-uri https://manifest.yourcompany.com/v1
```

### Generate with AI Detection

```bash
# Generate SBOM with AI model detection
manifest-cli generate ./path/to/code \
  --detect-ai \
  --file my-sbom \
  --output cyclonedx-json
```

### Generate with Generator Passthrough

```bash
# Pass generator-specific arguments (e.g., --type java to cdxgen)
manifest-cli generate ./my-java-project \
  --generator=cdxgen \
  --output=cyclonedx-json \
  -- --type java

# Pass multiple arguments to the generator
manifest-cli generate alpine:latest \
  --generator=syft \
  -- --scope all-layers
```

### Publish with VDR Download

```bash
# Publish and automatically download vulnerability report
manifest-cli publish sbom.json \
  --api-key YOUR_API_KEY \
  --download-vdr \
  --vdr-output vulnerability-report.json
```

### CRAT Reachability Assessment

```bash
# Run reachability assessment with LLM
manifest-cli crat ./ \
  --llm openai/gpt-4 \
  --llm-api-key $OPENAI_API_KEY \
  --entrypoint src/index.ts
```
