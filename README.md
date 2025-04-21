# Manifest CLI

> [!NOTE] 
> Starting _January 31st, 2024_ all Manifest CLI releases will be named `manifest-cli` and changed away from the current name: `manifest`.
> Users will need to change the name anywhere that `manifest` is being called (e.g. pipelines or scripts) to get the latest Manifest CLI functionality.

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

</details>

<details>
<summary>Aptitude (apt)</summary>

```bash
echo "deb [trusted=yes] https://manifest.fury.io/apt/ /" > /etc/apt/sources.list.d/fury.list
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
<summary>Scoop</summary>

```bash
scoop bucket add manifest-cli https://github.com/manifest-cyber/scoop-bucket.git
scoop install manifest-cli
```

</details>

<details>
<summary>Yum</summary>

```bash
echo '[fury]
name=Manifest Cyber
baseurl=https://manifest.fury.io/yum/
enabled=1
gpgcheck=0' | sudo tee /etc/yum.repos.d/manifest-cyber.repo
sudo yum install manifest-cli
```

- if running as admin, you can omit `sudo`.
</details>

<details>
<summary>Manual Installation</summary>

Download the pre-compiled binaries, `.deb`, `.rpm`, or `.apk`, from the [releases](https://github.com/manifest-cyber/cli/releases) page.
Copy them to the desired location or install them with the appropriate tools.

For Mac users, please note that the current release is not yet signed by Apple Developer.
Therefore, you must enable it under Privacy & Security > Security > Open Anyway > Open.

</details>

## Installing generators

The `install` command can help you install supported generators that are required for generating SBOM with this tool

**NOTE**: On Windows, you must have WSL enabled or `bash` available at the path. Otherwise, this command will not work.

### Arguments

` -d`, `--destination`: Installation destination string (default "/usr/local/bin")
`-g`, `--generator`: Name of generator to install. Supported options: [syft|trivy|cdxgen|docker-sbom|spdx-sbom-generator|sigstore-sbom] (default "syft")
`--version`: Installs specific version of the generator

### Generator Installation Example
This command installs the generator globally.
```bash
manifest-cli install -g cdxgen
```

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

`-g`, `--generator`: the generator to use (syft, trivy, cdxgen, sigstore-bom).

`-p`, `--paths`: **[DEPRECATED: use positional arguments instead]** the paths to local repositories, or name:version of a container, to scan.

`-f`, `--file`: filename for the output file.

`-h`, `help`: Get help on how to use the cli.

`-k`, `--api-key`: Manifest API key, if publish is set to true.

`-o`, `--output`: SBOM format to use. Either cyclonedx-json or spdx-json.

Note: CycloneDX released v1.5 on June 25, 2023. Currently, Manifest only provides partial support for v1.5 and full support for prior versions.

`-n`, `--name`: Name of the generated SBOM. Overrides any existing version info.

`--label`: **[DEPRECATED] use --asset-label instead.** One or more labels to add to the SBOM. If the label does not exist, it will be created then applied to the SBOM. Use a single --label flag with comma delimited values, or multiple --label flag instances.

`--product-id`: Assign an SBOM to a product by providing a product ID. You may create products through the Manifest UI.

`--active={true|false}`: Whether this SBOM should be marked as Active when uploading, if not present the default of your organization's setting will be used (which is typically `true`).

`--asset-label`: One or more labels to add to the SBOM's asset. If the label does not exist, it will be created. Use a single `--asset-label` flag with comma delimited values, or multiple `--asset-label` flag instances to add multiple labels to an asset.

`--product-label`: One or more labels to add to the product. If the label does not exist, it will be created. Use a single `--product-label` flag with comma delimited values, or multiple `--product-label` flag instances to add multiple labels to a product. You must send a valid `--product-id` in order for the `--product-label` values to be assigned properly.

`--publish`: true/false, whether to send the SBOM to your Manifest app tenant. This requires an API token (see more below). Default: false.

`--generator-preset`: set generator config preset. (recommended, none)

`--generator-config`: set path to generator config file (if applicable)

`v`, `--version`: Version of the generated SBOM. Overrides any existing version info.

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
manifest-cli sbom --product-id=MY_PRODUCT_ID --product-label=production --product-label=golang --name=my-sbom --version=v1.0.0 --output=spdx-json ./path/to/repo
```

#### Generation with specific file and container

**Be aware**: Generating an SBOM by pointing to specific files is not recommended. Doing so may result in an incomplete SBOM.

```bash
manifest-cli sbom --generator=trivy ./route-to-file ./go.mod ./go.sum alpine:latest
```

## Merge

Use the `merge` command to merge two or more SBOMs of the same format.

### Arguments

The same arguments available for the `sbom` command are available for `merge`.

`-i`, `--input-format`: SBOM format of the inputs, either cyclonedx or spdx.

### Examples

```bash
manifest-cli merge --input-format=cyclonedx --name=my-app scm-sbom.json image-scm.json
```

## (Beta) Generating & Publishing SBOM Attestation

## Keyless Signing

Coming Soon!

## Local Private Key Generation

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

This also supported for merging SBOMs.

```bash
manifest-cli merge --attest --key my-secret-key.key sbom1.json sbom2.json
```

## Generators

Generators must be installed in order for the cli to use them. Syft is the default generator. Keep in mind that not all generators work the same or create the same outputs, we will add more information here later on that.

Supported generators:

- [syft](https://github.com/anchore/syft)
- [trivy](https://github.com/aquasecurity/trivy)
- [cdxgen](https://github.com/CycloneDX/cdxgen)
- [docker-sbom](https://docs.docker.com/engine/sbom/)
- [spdx-sbom-generator](https://github.com/opensbom-generator/spdx-sbom-generator)
- [kubernetes sigstore-bom](https://github.com/kubernetes-sigs/bom) (`sigstore-bom`)

## API Tokens for Publishing SBOMs

To create a new token, go to your profile in the Manifest App and click on the Create New Token button. Then, fill out the form and click "confirm".

![Create a new token in the Manifest app](/img1.png)

Once you have successfully created your key, copy it and save it in a secure location.

<img width="417" alt="image" src="https://user-images.githubusercontent.com/4471948/224496595-4770bac2-2b2c-4d39-9663-cecdb5e366f1.png">

Remember to protect your API key! Avoid committing it to your source code or printing it as plain text. Instead, use secrets management tools to keep it secure 🧙.

### Usage

The recommended way to use the API Key is via the `MANIFEST_API_KEY` environment variable.

```bash
export MANIFEST_API_KEY=your-api-token
manifest-cli sbom ./ --publish
```

## Contact

Have any questions or need help? Don't hesitate to reach out: info@manifestcyber.com!
