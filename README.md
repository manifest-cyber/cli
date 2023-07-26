# Manifest CLI

## Overview

The Manifest CLI is a cross-platform application and supports both amd and arm architectures. Using various methods, you can install it on Linux, Windows, or Mac (OSX).

You can use the CLI to

- generate SBOMs (via the `sbom` command) from specific manifest files, local filesystems (e.g. a python project), and containers (e.g. alpine:latest).
- merge two or more SBOMs (of the same format) together into one SBOM, with the `merge` command

## Installation

<details>
<summary>Aptitude (apt)</summary>

```bash
echo "deb [trusted=yes] https://manifest.fury.io/apt/ /" > /etc/apt/sources.list.d/fury.list
sudo apt update
sudo apt install manifest
```

</details>

<details>
<summary>Homebrew (tap)</summary>
	
```bash
brew install manifest-cyber/tap/manifest
```
</details>

<details>
<summary>Docker</summary>
	
```bash
docker run --rm --privileged \
  -v $PWD:/go/src/github.com/user/repo \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -w /go/src/github.com/user/repo \
  -e GITHUB_TOKEN \
  -e DOCKER_USERNAME \
  -e DOCKER_PASSWORD \
  -e DOCKER_REGISTRY \
  -e MANIFEST_API_KEY \
manifest-cyber/cli merge
```
</details>

<details>
<summary>Go</summary>

```go
go get github.com/manifest-cyber/cli
```

</details>

<details>
<summary>Snapcraft</summary>

```bash
sudo snap install --classic manifest-cli
```

</details>

<details>
<summary>Scoop</summary>

```bash
scoop bucket add manifest https://github.com/manifest-cyber/scoop-bucket.git
scoop install manifest
```

</details>

<details>
<summary>Yum</summary>

```bash
echo '[fury] name=Gemfury Private Repo baseurl=https://manifest.fury.io/yum/ enabled=1 gpgcheck=0' | sudo tee /etc/yum.repos.d/fury.repo
sudo yum install manifest
```

</details>

<details>
<summary>Manual Installation</summary>

Download the pre-compiled binaries, `.deb`, `.rpm`, or `.apk`, from the [releases](https://github.com/manifest-cyber/cli/releases) page.
Copy them to the desired location or install them with the appropriate tools.

For Mac users, please note that the current release is not yet signed by Apple Developer.
Therefore, you must enable it under Privacy & Security > Security > Open Anyway > Open.

</details>

## Generating an SBOM (`sbom`)

The `sbom` command generates an SBOM on any number of targets (paths to source code, containers, etc.), using a specified open-source SBOM generator (our default is Syft). Make sure to install the relevant generators first before using them in the cli (see the Generators section below)

### Arguments

`-g`, `--generators`: the generator to use (syft, trivy, cdxgen, sigstore-bom).

`-p`, `--paths`: the paths to local repositories, or name:version of a container, to scan [DEPRECATED: use positional arguments instead]

`-f`, `--file`: filename for the output file (default is bom.json)

`-h`, `help`: get helpon how to use the cli.

`-k`, `--api-key`: Manifest API key, if publish is set to true.

`-o`, `--output`: SBOM format to use. Either cyclonedx-json or spdx-json.

`-n`, `--name`: name of the generated SBOM. Overrides any existing version info.

`--label`: one or more labels attached to the SBOM and the asset in Manifest app.

`--publish`: true/false, whether to send the SBOM to your Manifest app tenant. This requires an API token (see more below). Default: false.

`v`, `--version`: version of the generated SBOM. Overrides any existing version info.

`--`: to pass through additional arguments to specific generators, use the `--` separator at the end of the command, followed by any additional arguments.

### SBOM Generation Best Practices

To enable full visibility into software dependencies and vulnerabilities, we recommend integrators implement SBOM generation following the build stage of a CI/CD pipeline. However, if there are dependencies that are pulled in during runtime, then SBOM generation should follow after the testing stage. Doing so ensures that all dependent software and artifacts are properly captured in the resulting SBOM.

An example of a dependency that is pulled in during runtime would be Docker containers. These containers are referenced with static blueprints (Dockerfile and docker-compose) in the source code. However, to get visibility into those containers, they would first need to be built or already exist within a container registry. For working with containers, store the image name and tag of the containers used within a variable so they can be referenced in the `sbom` command. See the examples below for more details.

A general rule of thumb is that properly implemented SBOM generation within a CI/CD pipeline will almost always be more complete and accurate compared to an SBOM generated statically. In the cases where that is not true, the SBOMs are equivalent.

Regardless of whether or not the SBOM generation is implemented within a CI/CD pipeline, it is **strongly** recommended to use paths instead of specific files.

### Examples

#### Quickstart

```bash
manifest sbom ./
```

#### SBOM Generation with specific container, generator, output format, and passthrough flags

```bash
manifest sbom --label=production --label=java --generator=cdxgen --name=java-sbom --output=cyclonedx-json ./path/to/repo alpine:latest -- --type java
```

#### Generation with specific file and container

**Be aware**: Generating an SBOM by pointing to specific files is not recommended. Doing so may result in an incomplete SBOM.

```bash
manifest sbom --generator=trivy ./route-to-file ./go.mod ./go.sum alpine:latest
```

## Merge

Use the `merge` command to merge two or more SBOMs of the same format.

### Arguments

The same arguments available for the `sbom` command are available for `merge`.

`-i`, `--input-format`: SBOM format of the inputs, either cyclonedx or spdx.

### Examples

```bash
manifest merge --input-format=cyclonedx --name=my-app scm-sbom.json image-scm.json
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
manifest sbom --attest --key my-secret-key.key ./
```

This also supported for merging SBOMs.

```bash
manifest merge --attest --key my-secret-key.key sbom1.json sbom2.json
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
manifest merge sbom1.json sbom2.json
```

## Contact

Have any questions or need help? Don't hesitate to reach out: info@manifestcyber.com!
