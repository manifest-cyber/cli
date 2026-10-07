# Manifest CLI

## Overview

The Manifest CLI is a cross-platform application and supports both amd and arm architectures. Using various methods, you can install it on Linux, Windows, or Mac (OSX).

You can use the CLI to

- generate SBOMs (via the `sbom` command) from specific manifest files, local filesystems (e.g. a python project), and containers (e.g. alpine:latest).
- merge two or more SBOMs (of the same format) together into one SBOM, with the `merge` command

## Installation

<details>
<summary>Install script (Recommended)</summary>

```bash
curl -sSfL https://raw.githubusercontent.com/manifest-cyber/cli/main/install.sh | sh -s
```

`-b`: sets bindir or installation directory, Defaults to `./bin`.

`-d`: turns on debug logging.

Use a positional argument to pass a specific release.

```bash
curl -sSfL https://raw.githubusercontent.com/manifest-cyber/cli/main/install.sh | sh -s -- -b /usr/local/bin v0.14.8
```

**Note**: This command pipes to `sh`, so it requires a POSIX shell -- Linux, macOS, or Windows under WSL or Git Bash. It will not run in a native PowerShell or Command Prompt session. On native Windows, see [Windows](#windows-install) below instead.

</details>

<details>
<summary>Aptitude (apt)</summary>

```bash
echo "deb [trusted=yes] https://repo.fury.io/manifest/ /" > /etc/apt/sources.list.d/fury.list
sudo apt update
sudo apt install manifest-cli
```

</details>

<details>
<summary>Homebrew (tap)</summary>

```bash
brew install manifest-cyber/tap/manifest-cli
```
</details>

<details>
<summary>Yum</summary>

```bash
echo '[fury]
name=Manifest Cyber
baseurl=https://repo.fury.io/manifest/
enabled=1
gpgcheck=0' | sudo tee /etc/yum.repos.d/manifest-cyber.repo
sudo yum install manifest-cli
```

- if running as admin, you can omit `sudo`.
</details>

<a name="windows-install"></a>
<details>
<summary>Windows</summary>

No WSL, Git Bash, or other POSIX shell required -- pick one of the methods below.

<details>
<summary>Scoop</summary>

```bash
scoop bucket add manifest-cli https://github.com/manifest-cyber/scoop-bucket.git
scoop install manifest-cli
```

Requires [Scoop](https://scoop.sh) itself to be installed first.

</details>

<details>
<summary>PowerShell or Command Prompt</summary>

`curl.exe` and `tar` ship with Windows 10/11, so you can download and extract the binary directly from Command Prompt or PowerShell:

```bat
curl.exe -sSfLo manifest-cli.zip https://github.com/manifest-cyber/cli/releases/latest/download/manifest-cli_windows_x86_64.zip
tar -xf manifest-cli.zip
```

This extracts `manifest-cli.exe` into the current folder. Move it to a folder on your `PATH`, or add the folder to `PATH` (see [Adding a Folder to the Path (Windows)](#adding-a-folder-to-the-path-windows) below).

To install a specific version instead of the latest, replace `latest/download` with `download/vX.Y.Z` (see [releases](https://github.com/manifest-cyber/cli/releases) for available tags).

</details>

<details>
<summary>Direct Download</summary>

If you prefer, pick a version from the [releases](https://github.com/manifest-cyber/cli/releases) page, download `manifest-cli_windows_x86_64.zip` from its assets, then extract it and follow the same PATH steps above.

</details>

Installing generators (syft, trivy, cdxgen, etc.) on Windows is a separate step -- see [Windows Installation (--native, No WSL Required)](#windows-installation---native-no-wsl-required) below.

</details>

<details>
<summary>Manual Installation</summary>

Download the pre-compiled binaries, `.deb`, `.rpm`, `.apk`, or `.zip`, from the [releases](https://github.com/manifest-cyber/cli/releases) page.
Copy them to the desired location or install them with the appropriate tools.

For Mac users, please note that the current release is not yet signed by Apple Developer.
Therefore, you must enable it under Privacy & Security > Security > Open Anyway > Open.

For Windows users, follow the instructions in the [Windows](#windows-install) section instead.

</details>

## Updating to the Latest Version

<details>
<summary>Install script (Recommended)</summary>

To update to the latest version, simply run the install script again:

```bash
curl -sSfL https://raw.githubusercontent.com/manifest-cyber/cli/main/install.sh | sh -s
```

The install script will automatically update your existing installation to the latest version.

To update to a specific version:

```bash
curl -sSfL https://raw.githubusercontent.com/manifest-cyber/cli/main/install.sh | sh -s -- -b /usr/local/bin v0.14.8
```

**Note**: This command pipes to `sh`, so it requires a POSIX shell (Linux, macOS, or Windows under WSL/Git Bash) -- it will not run in native PowerShell or Command Prompt. On native Windows, see [Windows](#windows-update) below instead.

</details>

<details>
<summary>Aptitude (apt)</summary>

```bash
sudo apt update
sudo apt upgrade manifest-cli
```

</details>

<details>
<summary>Homebrew (tap)</summary>

```bash
brew upgrade manifest-cyber/tap/manifest-cli
```
</details>

<details>
<summary>Yum</summary>

```bash
sudo yum update manifest-cli
```

- if running as admin, you can omit `sudo`.
</details>

<a name="windows-update"></a>
<details>
<summary>Windows</summary>

No WSL, Git Bash, or other POSIX shell required -- pick one of the methods below.

<details>
<summary>Scoop</summary>

```bash
scoop update manifest-cli
```

</details>

<details>
<summary>PowerShell or Command Prompt</summary>

```bat
curl.exe -sSfLo manifest-cli.zip https://github.com/manifest-cyber/cli/releases/latest/download/manifest-cli_windows_x86_64.zip
tar -xf manifest-cli.zip
```

Replace the existing `manifest-cli.exe` on your `PATH` with the extracted one.

To update to a specific version instead of the latest, replace `latest/download` with `download/vX.Y.Z` (see [releases](https://github.com/manifest-cyber/cli/releases) for available tags).

</details>

<details>
<summary>Direct Download</summary>

If you prefer, pick a version from the [releases](https://github.com/manifest-cyber/cli/releases) page, download `manifest-cli_windows_x86_64.zip` from its assets, then extract it and replace your existing `manifest-cli.exe`.

</details>

</details>

<details>
<summary>Manual Installation</summary>

Download the latest pre-compiled binaries, `.deb`, `.rpm`, `.apk`, or `.zip`, from the [releases](https://github.com/manifest-cyber/cli/releases) page.
Replace your existing installation with the new binaries or install them with the appropriate tools.

For Windows users, follow the instructions in the [Windows](#windows-update) section instead.

</details>

## Installing generators

The `install` command can help you install supported generators that are required for generating SBOM with this tool

**NOTE**: On Windows, `install` uses a shell script by default and requires WSL or `bash` on the path. To install generators without WSL or a Unix shell, use `--native` -- see [Windows Installation (--native, No WSL Required)](#windows-installation---native-no-wsl-required) below.

### Arguments

For an exhaustive list of arguments, see [ARGUMENTS.md](ARGUMENTS.md).

`-d`, `--destination`: Installation destination string (default "/usr/local/bin"; on Windows with `--native`, defaults to the directory containing `manifest-cli.exe` instead)
`-g`, `--generator`: Name of generator to install. Supported options: [syft|csbom|trivy|cdxgen|docker-sbom|spdx-sbom-generator|sigstore-bom] (default "syft")
`--version`: Installs specific version of the generator
`--native`: Install using a built-in Go downloader instead of a shell script. Available on all platforms; removes the WSL/Bash requirement on Windows.

### Generator Installation Example

This command installs the generator globally.

```bash
manifest-cli install -g cdxgen
```

### Windows Installation (--native, No WSL Required)

<details>
<summary>Installing generators on Windows without WSL (--native)</summary>

This section covers installing SBOM *generators* (syft, trivy, cdxgen, etc.) on Windows. To install `manifest-cli` itself on native Windows, see Manual Installation under [Installation](#installation) above.

By default, `install` downloads generators with a shell script, which requires WSL or `bash`. Pass `--native` to install using a built-in Go downloader instead -- no WSL, Bash, or other Unix shell required.

```bash
# Installs syft.exe next to manifest-cli.exe -- already on PATH, no extra setup
manifest-cli install -g syft --native
```

If you omit `-d`/`--destination`, the generator installs into the same directory as `manifest-cli.exe`, which is already on your `PATH` since that's where you're running `manifest-cli` from. If you pass a custom `-d`, add that directory to your Windows `PATH` manually before running `generate`.

By default, `--native` installs a supported version of each generator: `syft`, `trivy`, and `cdxgen` install a version manifest-cli has validated; `spdx-sbom-generator`, `docker-sbom`, and `sigstore-bom` fetch the latest release. To pin an exact version, add `--version`:

```bash
manifest-cli install -g syft -d .\bin --native --version v1.44.0
```

Pass `--native` to `generate` as well, so it can find natively-installed generators on `PATH` automatically:

```bash
manifest-cli generate --generator syft --native ./my-project
```

**Generator support on Windows:**

| Generator | `--native` | Prerequisites | Notes |
| --- | --- | --- | --- |
| syft | Yes | None | |
| trivy | Yes | None | |
| spdx-sbom-generator | Yes | None | |
| docker-sbom | Yes | Docker Desktop | Always installs to `%USERPROFILE%\.docker\cli-plugins\`, regardless of `-d` |
| csbom | Always native | None | No `--native` flag needed |
| cdxgen | Yes (via npm) | Node.js and npm on `PATH` | Runs `npm install -g @cyclonedx/cdxgen` |
| sigstore-bom | Install only | None | `generate` does not yet work on Windows -- see below |

**sigstore-bom limitation**: `install --native` works for `sigstore-bom`, but running `generate --generator sigstore-bom` currently fails on Windows due to a known upstream issue. Use WSL for `sigstore-bom` generation only, until it's fixed upstream.

</details>

## Generating an SBOM (`sbom`)

The `sbom` command generates an SBOM on any number of targets (paths to source code, containers, etc.), using a specified open-source SBOM generator (our default is Syft). Make sure to install the relevant generators first before using them in the CLI (see the Generators section below).

It is essential that the generator installation location is part of the OS execution path.

### Adding a Folder to the Path (Mac/Linux)

To add a folder to the path in OSX or Linux, you can follow these steps:

1. Open a terminal.
2. Locate the folder you want to add to the path.
3. Copy the path of the folder.
4. Open the `.bashrc` or `.bash_profile` file in a text editor. This file is usually located in your home directory.
5. Add the following line at the end of the file, replacing `/path/to/folder` with the actual path of the folder you want to add:

```bash
export PATH="/path/to/folder:$PATH"
```

6. Save the file and exit the text editor.
7. Restart your terminal or run the following command to apply the changes:

```bash
source ~/.bashrc
```

### Adding a Folder to the Path (Windows)

To add a folder to the path in Windows, you can follow these steps:

1. Open the Start menu and search for "Environment Variables".
2. Click on "Edit the system environment variables".
3. In the System Properties window, click on the "Environment Variables" button.
4. In the "System Variables" section, scroll down and select the "Path" variable.
5. Click on the "Edit" button.
6. Click on the "New" button and enter the path of the folder you want to add.
7. Click "OK" to save the changes.
8. Restart your terminal or any open command prompt windows for the changes to take effect.

Remember to replace `/path/to/folder` with the actual path of the folder you want to add.

### Arguments

For an exhaustive list of arguments, see [ARGUMENTS.md](ARGUMENTS.md).

`-g`, `--generator`: the generator to use (syft, csbom, trivy, cdxgen, docker-sbom, spdx-sbom-generator, sigstore-bom).

`-p`, `--paths`: **[DEPRECATED: use positional arguments instead]** the paths to local repositories, or name:version of a container, to scan.

`-f`, `--file`: filename for the output file.

`-h`, `--help`: Get help on how to use the cli.

`-k`, `--api-key`: Manifest API key, if publish is set to true.

`-o`, `--output`: SBOM format to use. Either cyclonedx-json or spdx-json.

`-n`, `--name`: Name of the generated SBOM. Overrides any existing version info.

`--label`: **[DEPRECATED] use --asset-label instead.** One or more labels to add to the SBOM. If the label does not exist, it will be created then applied to the SBOM. Use a single --label flag with comma delimited values, or multiple --label flag instances.

`--product-id`: Assign an SBOM to a product by providing a product ID. You may create products through the Manifest UI. To find a product's ID, open the product in the Manifest app and copy the identifier from the page URL: it is the segment between `/product/` and `/overview` (for example, in `https://app.manifestcyber.com/product/64b8f0a2e1d3c5a7b9f2e4d6/overview` the product ID is `64b8f0a2e1d3c5a7b9f2e4d6`). Only takes effect together with `--publish`, and requires a token carrying the product permissions listed under [API Tokens for Publishing SBOMs](#api-tokens-for-publishing-sboms).

`--active={true|false}`: Whether this SBOM should be marked as Active when uploading, if not present the default of your organization's setting will be used (which is typically `true`).

`--asset-label`: One or more labels to add to the SBOM's asset. If the label does not exist, it will be created. Use a single `--asset-label` flag with comma delimited values, or multiple `--asset-label` flag instances to add multiple labels to an asset.

`--product-label`: One or more labels to add to the product. If the label does not exist, it will be created. Use a single `--product-label` flag with comma delimited values, or multiple `--product-label` flag instances to add multiple labels to a product. You must send a valid `--product-id` in order for the `--product-label` values to be assigned properly.

`--publish`: true/false, whether to send the SBOM to your Manifest app tenant. This requires an API token (see more below). Default: false.

`--enrich`: `none` / `ecosystems` (case insensitive) Overrides your organization settings to force the SBOM to be enriched (`ecosystems`) or to skip enrichment (`none`)

`--generator-preset`: set generator config preset. (recommended, none)

`--generator-config`: set path to generator config file (if applicable)

`--supplier`: Supplier (organization) name to set on the root SBOM component (`metadata.component.supplier`) and the BOM metadata (`metadata.supplier`). For SPDX output this sets the package supplier, falling back to `--group` when unset.

`--version`: Version of the generated SBOM. Overrides any existing version info.

`--`: to pass through additional arguments to specific generators, use the `--` separator at the end of the command, followed by any additional arguments.

### SBOM Generation Best Practices

To enable full visibility into software dependencies and vulnerabilities, we recommend integrators implement SBOM generation following the build stage of a CI/CD pipeline. However, if there are dependencies that are pulled in during runtime, then SBOM generation should follow after the testing stage. Doing so ensures that all dependent software and artifacts are properly captured in the resulting SBOM.

An example of a dependency that is pulled in during runtime would be Docker containers. These containers are referenced with static blueprints (Dockerfile and docker-compose) in the source code. However, to get visibility into those containers, they would first need to be built or already exist within a container registry. For working with containers, store the image name and tag of the containers used within a variable so they can be referenced in the `sbom` command. See the examples below for more details.

A general rule of thumb is that properly implemented SBOM generation within a CI/CD pipeline will almost always be more complete and accurate compared to an SBOM generated statically. In the cases where that is not true, the SBOMs are equivalent.

Regardless of whether or not the SBOM generation is implemented within a CI/CD pipeline, it is **strongly** recommended to use paths instead of specific files.

### Examples

#### Quickstart

```bash
manifest-cli sbom ./
```

#### SBOM Generation with specific container, generator, output format, and passthrough flags

```bash
manifest-cli sbom --asset-label=production --asset-label=java --generator=cdxgen --name=java-sbom --output=cyclonedx-json ./path/to/repo alpine:latest -- --type java
```

#### SBOM Generation with product assignment and labels

```bash
manifest-cli sbom --product-id=MY_PRODUCT_ID --product-label=production --product-label=golang --name=my-sbom --version=v1.0.0 --output=spdx-json --publish ./path/to/repo
```

#### Generation with specific file and container

**Be aware**: Generating an SBOM by pointing to specific files is not recommended. Doing so may result in an incomplete SBOM.

```bash
manifest-cli sbom --generator=trivy ./route-to-file ./go.mod ./go.sum alpine:latest
```

## Generating a C/C++ SBOM with csbom

`csbom` is a generator purpose-built for C and C++ projects. It works at the build-system and
source level to surface components that were compiled into a binary — including vendored
libraries, statically linked dependencies, and third-party SDKs that leave no trace in a
package manager.

Install `csbom` and run it against your project:

```bash
manifest-cli install -g csbom
manifest-cli generate --generator csbom -f sbom.json ./my-cpp-project
```

For full details, flag reference, and workflow integration, see [docs/csbom.md](docs/csbom.md).

## Merge

Use the `merge` command to merge two or more SBOMs of the same format.

### Arguments

For an exhaustive list of arguments, see [ARGUMENTS.md](ARGUMENTS.md).

The same arguments available for the `sbom` command are available for `merge`.

`-i`, `--input-format`: SBOM format of the inputs, either cyclonedx or spdx.

### Examples

```bash
manifest-cli merge --input-format=cyclonedx --name=my-app scm-sbom.json image-scm.json
```

## Workflow

The `workflow` command executes a sequence of `manifest-cli` commands defined in a JSON file or
via a built-in preset. Each step maps to an existing command (`generate`, `merge`, `publish`,
`install`) using the same flags as the CLI — no new syntax to learn.

Use the built-in `cpp` preset for C/C++ projects with no configuration needed. It runs `syft`
and `csbom` against the same input path, then merges both results into a single CycloneDX 1.6
SBOM — giving you broad package detection and deep build-system analysis in one command:

```bash
manifest-cli workflow --preset cpp -f merged.json ./my-project
```

Or bring your own workflow file for full control over every step:

```bash
manifest-cli workflow --workflow-file workflow.json ./my-project
```

For full details, all flags, and ready-to-use example files, see
[docs/workflow/WORKFLOW_FEATURE.md](docs/workflow/WORKFLOW_FEATURE.md).

## Deactivating Older Versions

When you publish a new version of an asset, the previous versions stay active by default. Add `--deactivate-older` (`-d`) to mark prior versions of the same asset inactive as part of the same publish, so your inventory reflects only the version you just shipped:

```bash
export MANIFEST_API_KEY=your-api-token
manifest-cli publish sbom.json --deactivate-older
```

By default this deactivates *every* older version of the asset. To narrow it to older versions carrying specific labels, add `--deactivate-label` (repeatable). Each value is matched against the asset's labels, and only matching older versions are deactivated. `--deactivate-label` requires `--deactivate-older`:

```bash
# Only deactivate older versions labeled "production" or "java"
manifest-cli publish sbom.json \
  --deactivate-older \
  --deactivate-label production \
  --deactivate-label java
```

These flags are available on the `publish`, `sbom --publish`, and `merge --publish` commands.

## Replacing an Asset in a Product

When an asset belongs to a product's inventory, `--replace-in-product` swaps the asset's prior version out of that product and puts the version you are publishing in its place, after the upload and vulnerability scan complete. This is useful when a product should track exactly one version of an asset. It requires `--product-id` to identify which product's inventory to update:

```bash
export MANIFEST_API_KEY=your-api-token
manifest-cli publish sbom.json \
  --product-id YOUR_PRODUCT_ID \
  --replace-in-product
```

## Publishing Snapshots

A snapshot tells the Manifest platform that a group of SBOMs represents the complete state of an environment or product at a single point in time. When you publish in snapshot mode, the platform runs a deactivation sweep on each upload: every asset in your organization that carries the snapshot label and was *created* before the snapshot timestamp is marked inactive. Assets left out of the snapshot, such as a retired service, are deactivated along with older versions. This keeps your inventory in sync with what is actually deployed, without you having to deactivate stale assets by hand.

> **Warning:** The sweep matches assets by label name across your **whole organization**, not just one product, and it decides what is stale by when each asset record was first created. A pipeline that breaks the [snapshot pipeline rules](#snapshot-pipeline-rules) can silently deactivate current assets (including assets in other products) or leave old assets active indefinitely. Read those rules before wiring snapshots into CI.

Common uses are reconciling the live contents of a product or environment (e.g. `payments-platform-prod`) on each deploy or on a schedule, and retiring assets for services that have been removed from that product or environment.

Snapshot mode is enabled by passing **both** of these flags together:

- `--snapshot-label <label>`: identifies the pipeline the SBOM belongs to. Use a name that is unique to one product's pipeline and will never change (e.g. `payments-platform-prod`). The label is also attached to each published asset.
- `--snapshot-timestamp <timestamp>`: an RFC3339 timestamp with an explicit UTC offset (e.g. `2024-01-15T10:00:00Z`). This is the start of the snapshot pass and the boundary the deactivation sweep uses. Compute it once per pass (see [snapshot pipeline rules](#snapshot-pipeline-rules)).

The CLI validates the timestamp before making any API call. It must be RFC3339 with an explicit UTC offset (a trailing `Z` for UTC, or an offset like `-05:00`), must not be in the future, and must not be more than 7 days in the past. Because of the 7-day limit, a pass can only be retried with its original timestamp within 7 days of that timestamp; after that, run a fresh pass. Passing one flag without the other fails validation; passing neither publishes the SBOM normally with no sweep.

These flags are available on the `publish`, `sbom --publish`, and `merge --publish` commands.

```bash
# Publish an existing SBOM as part of the payments-platform-prod snapshot
manifest-cli publish sbom.json \
  --snapshot-label payments-platform-prod \
  --snapshot-timestamp 2024-01-15T10:00:00Z

# Generate and publish in one step
manifest-cli sbom ./ -f sbom.json --publish \
  --snapshot-label payments-platform-prod \
  --snapshot-timestamp 2024-01-15T10:00:00Z
```

In CI, compute the timestamp once at the start of the pass and reuse it for every publish in that pass:

```bash
export MANIFEST_API_KEY=your-api-token

# Once, at the start of the pass. Persist this value (for example, as a
# pipeline output) so that a retry of this pass reuses it instead of
# generating a new one.
SNAPSHOT_TS=$(date -u +%Y-%m-%dT%H:%M:%SZ)

for sbom in sboms/*.json; do
  manifest-cli publish "$sbom" \
    --snapshot-label payments-platform-prod \
    --snapshot-timestamp "$SNAPSHOT_TS" \
    --version "$SNAPSHOT_TS"
done
```

Drop `--version` only if each SBOM's own version already changes on every build.

> **Note:** The deactivation sweep only runs when snapshot mode is enabled. Snapshot mode and `--deactivate-older` serve different purposes: `--deactivate-older` deactivates prior versions of the same asset, while snapshots reconcile an entire environment or product against a point in time.

### Snapshot Pipeline Rules

Follow all of these. Breaking any of them fails silently: the CLI reports success, and the damage shows up later as missing or duplicate active assets.

1. **Use one snapshot label per product pipeline, and never share it.** The sweep deactivates matching assets across your whole organization; `--product-id` does not narrow it. If two products publish with the same snapshot label, each product's run deactivates the other product's assets. Snapshot labels and asset labels share one set of label names, so don't use a snapshot label's name with `--asset-label` or on assets in any other product.
2. **Never rename the snapshot label.** Label matching is exact and case-sensitive: `UAT`, `uat`, and `Uat` are three different labels (only leading and trailing spaces are ignored). After a rename, the first run finds nothing to deactivate under the new label, and assets from runs under the old label stay active permanently because no future run will carry that label again. If you must rename, contact Manifest support to clean up the previous generation.
3. **Compute `--snapshot-timestamp` once per pass, before the first upload, and pass the same value to every publish in the pass.** Reuse that value on any retry of the pass. Generating a new timestamp for each publish (for example, `$(date ...)` inline in each command) makes each publish deactivate the assets that the earlier publishes in the same pass just created.
4. **Give every asset a new version on every pass.** The platform identifies an asset by its name and version. Publishing the same name and version again updates the existing asset instead of creating a new one, and that asset keeps its original creation time, which is before the new pass's timestamp. Every later publish in the pass then deactivates it (and, with `--update-product`, removes it from the product) even though it is part of the current snapshot, so of the assets affected this way only the one published last stays active. If the version inside each SBOM already changes on every build (for example, a commit SHA or build number), this is handled. Otherwise, pass `--version` with a value unique to the pass, such as the snapshot timestamp itself. Don't rely on stable version strings (such as `1.4.0` for a service that hasn't changed) in a snapshot pipeline. If an SBOM has no version, Manifest uses a checksum of the file, so republishing an unchanged file counts as the same version.
5. **Include every SBOM in every pass.** Any asset under the label that isn't republished in a pass is treated as stale and deactivated. This is how retired services drop out, but it also means a pass that skips an SBOM deactivates that asset.
6. **Expect the previous generation to go inactive as soon as the pass starts.** The sweep runs on each upload, before the platform has processed it, so the first upload of a pass deactivates the entire previous generation. Until the pass finishes, only the assets published so far are active. If a pass stops partway, it stays that way until you retry with the same timestamp.
7. **Don't run two passes with the same snapshot label at the same time.** Overlapping passes race each other's sweeps, and which assets end up active depends on timing.

`--asset-label` has no effect on the sweep. It is a tag for filtering in the Manifest app, and only the snapshot label decides what gets deactivated. An asset published in snapshot mode carries both its asset labels and the snapshot label.

### Reconciling a Product's Inventory to a Snapshot

While snapshot mode reconciles your organization's asset inventory, `--update-product` reconciles a specific **product's inventory** to the same snapshot. After the upload completes, it removes from the product every asset that carries the snapshot label and was created before the snapshot timestamp, then adds the asset you are publishing. This keeps a product's inventory in sync with exactly what a given environment or product contains.

`--update-product` requires `--product-id`, `--snapshot-label`, and `--snapshot-timestamp`, and is mutually exclusive with `--replace-in-product` (use `--replace-in-product` for a single per-asset version swap, and `--update-product` for a full snapshot reconciliation).

When every asset in the pass carries a new version (see rule 4 in [snapshot pipeline rules](#snapshot-pipeline-rules)), only the first call removes anything, and later calls just add their asset. Without a new version, each call can remove assets that earlier calls in the same pass just added. To loop over every service in a deploy, compute the timestamp once and pass a per-pass `--version`:

```bash
export MANIFEST_API_KEY=your-api-token
# Compute the timestamp once for the whole pass, never inside the loop.
SNAPSHOT_TS=$(date -u +%Y-%m-%dT%H:%M:%SZ)
for sbom in service-a.json service-b.json service-c.json; do
  manifest-cli publish "$sbom" \
    --product-id YOUR_PRODUCT_ID \
    --update-product \
    --snapshot-label payments-platform-prod \
    --snapshot-timestamp "$SNAPSHOT_TS" \
    --version "$SNAPSHOT_TS"
done
```

The removal **deletes** the product's inventory rows for the stale assets, so the previous generation disappears from the product view rather than appearing as inactive. The assets themselves stay in your organization's asset list, marked inactive by the snapshot's deactivation sweep. If you want previous versions to remain listed in the product as inactive, publish with `--product-id` and the snapshot flags but without `--update-product`.

Only assets whose record was *created* at or after the snapshot timestamp are safe from the removal. Publishing an SBOM with the same name and version as an existing asset updates that asset instead of creating a new one, and the asset keeps its original creation time, so later publishes in the same snapshot remove it. This is why the example passes a per-pass `--version`; see [snapshot pipeline rules](#snapshot-pipeline-rules). Assets that don't carry the snapshot label are left untouched.

## (Beta) Generating & Publishing SBOM Attestation

### Local Private Key Generation

You will need `cosign` installed to proceed. [Click here to get started](https://github.com/sigstore/cosign/tree/main#installation).

Run the following command to generate a public-private key pair. Users will be prompted for a password which will be required for all attestations with this key.

This will result in two files being created: `cosign.key` and `cosign.pub` generated in the user's home directory.

```bash
cosign generate-key-pair --output-key-prefix ~/cosign
```

To generate a public-private key pair with a specific filename prefix:

```bash
cosign generate-key-pair --output-key-prefix=~/my-secret-key
```

In this example, two files will be created: `my-secret-key.key` and `my-secret-key.pub` in the user's home directory.

To generate an SBOM with attestation using this key pair, two flags will need to be added: `--attest` and `--key`.

Simply include these two flags in any of the examples found in [Quickstart](#quickstart) to generate attestations.

```bash
manifest-cli sbom --attest --key my-secret-key.key ./
```

This is also supported for merging SBOMs.

```bash
manifest-cli merge --attest --key my-secret-key.key sbom1.json sbom2.json
```

## Generators

Generators must be installed in order for the cli to use them. Syft is the default generator. Keep in mind that not all generators work the same or create the same outputs, we will add more information here later on that.

Supported generators:

- [syft](https://github.com/anchore/syft)
- [csbom](https://github.com/manifest-cyber/csbom-cli) — C/C++ SBOM generator with static analysis across 19+ build system file types (CMake, Conan v1+v2, vcpkg, Meson, pkg-config, BitBake/Yocto, Makefile, VS linker maps, and more); enriches components with CPEs, PURLs, and licenses; outputs CycloneDX 1.6 or native csbom JSON
- [trivy](https://github.com/aquasecurity/trivy)
- [cdxgen](https://github.com/CycloneDX/cdxgen)
- [docker-sbom](https://docs.docker.com/engine/sbom/)
- [spdx-sbom-generator](https://github.com/opensbom-generator/spdx-sbom-generator)
- [kubernetes sigstore-bom](https://github.com/kubernetes-sigs/bom) (`sigstore-bom`)

## API Tokens for Publishing SBOMs

To create a new token:
1. In the Manifest App, go to the Settings page and click on the "API Tokens" section under "Account":
    ![API Tokens](/img0.png)

2. Fill out the form, add the required scope permissions, and then click "Create":
    ![Create a new token in the Manifest app](/img1.png)
    ![Add required scope permissions](/img2.png)

3. Once you have successfully created your key, copy it and save it in a secure location:
    ![Save your token in a secure location](/img3.png)

Remember to protect your API key! Avoid committing it to your source code or printing it as plain text. Instead, use secrets management tools to keep it secure 🧙.

### Scope permissions by flag

The scopes you enable in step 2 must cover the flags you intend to use. Enable the scope(s) below for each operation (names match the checkboxes on the token creation screen):

| Flag / operation | Scope(s) to enable |
| --- | --- |
| `--publish` (upload an SBOM) | Manage SBOMs and VEX |
| `--product-id` (assign an SBOM to a product) | Manage products, Manage assets, and View all pages and data |
| `--replace-in-product` | Manage products, Manage assets, and View all pages and data |
| `--update-product` (reconcile a product to a snapshot) | Manage products, Manage assets, and View all pages and data |
| `--download-vdr` | View all pages and data |

> **Note:** Use a user API token created from the API Tokens page on your profile (the flow shown above). The product operations (`--product-id`, `--replace-in-product`, `--update-product`) wait for the upload's vulnerability scan to finish before attaching the asset, so they need **View all pages and data** (the read scope) in addition to **Manage products** and **Manage assets**. **View all pages and data** is also what covers `--download-vdr`.

### Usage

The recommended way to use the API Key is via the `MANIFEST_API_KEY` environment variable. The `-f` flag is required in this case to ensure the file being uploaded has the correct `.json` file extension that is supported by the api.

```bash
export MANIFEST_API_KEY=your-api-token
manifest-cli sbom ./ -f sbom.json --publish
```

## Contact

Have any questions or need help? Don't hesitate to reach out: info@manifestcyber.com!
