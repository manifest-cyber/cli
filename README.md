# Manifest cli

## Overview
The Manifest cli is a cross-platform application and supports both amd and arm architectures. Using various methods, you can install it on Linux, Windows, or Mac (OSX).

## Installation
<details>
<summary>apt</summary>

### apt
```bash
echo "deb [trusted=yes] https://manifest.fury.io/apt/ /" > /etc/apt/sources.list.d/fury.list
sudo apt update
sudo apt install manifest
```
</details>

<details>
<summary>homebrew</summary>

### Homebrew (tap)
```bash
brew install manifest-cyber/tap/manifest
```

</details>

<details>
<summary>Docker</summary>
### Docker
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
### go install
```go
go get github.com/manifest-cyber/cli
```

</details>

<details>
<summary>Snapcraft</summary>
### go install
```bash
sudo snap install --classic manifest-cli
```

</details>

<details>
<summary>Scoop</summary>
### go install
```bash
scoop bucket add manifest https://github.com/manifest-cyber/scoop-bucket.git 
scoop install manifest
```

</details>

<details>
<summary>Yum</summary>
### yum
```bash
echo '[fury] name=Gemfury Private Repo baseurl=https://manifest.fury.io/yum/ enabled=1 gpgcheck=0' | sudo tee /etc/yum.repos.d/fury.repo
sudo yum install manifest
```

</details>


<details>
<summary>Manually</summary>

### Manually

Download the pre-compiled binaries, .deb, .rpm, or .apk, from the [releases](https://github.com/manifest-cyber/cli/releases) page. Copy them to the desired location or install them with the appropriate tools.

For Mac users, please note that the current release is not yet signed by Apple Developer. Therefore, you must enable it under Privacy & Security > Security > Open Anyway > Open. 

</details>

## Generating an SBOM (`sbom`)
The `sbom` command generates an SBOM on any number of targets (paths to source code, containers, etc.), using a specified open-source SBOM generator (our default is Syft).

### Arguments
`-g`, `--generators`: the generator to use (syft, trivy, cdxgen, sigstore-bom). 

`-p`,  `--paths`: the paths to local repositories, or name:version of a container, to scan

`-f`, `--file`: filename for the output file (default is bom.json)

`-h`, `help`: get helpon how to use the cli. 

`-k`, `--api-key`: Manifest API key, if publish is set to true. 

`-o`, `--output`: SBOM format to use. Either cyclonedx-json or spdx-json. 

`-n`, `--name`: name of the generated SBOM. Overrides any existing version info.

`--publish`: true/false, whether to send the SBOM to your Manifest app tenant. This requires an API token (see more below). Default: false. 

`v`, `--version`: version of the generated SBOM. Overrides any existing version info.

`--`: to pass through additional arguments to specific generators, use the `--` separator at the end of the command, followed by any additional arguments. 


### Examples
```bash
manifest sbom --paths=./route-to-file,alpine:latest --generator=trivy
```

```bash
manifest sbom --paths=./example-sbom-generation-workflow-java-gradle --generator=cdxgen --name=java-sbom --output=cyclonedx-json -- --type java
```

## Merge
Use the `merge` command to merge two or more SBOMs of the same format. 

### Arguments
The same arguments available for the `sbom` command are available for `merge`.

`-i`, `--input-format`: SBOM format of the inputs, either cyclonedx or spdx. 

### Examples
```bash
manifest merge --paths=scm-sbom.json,image-scm.json --input-format=cyclonedx --name=my-app
```

## API Tokens for Publishing SBOMs
To create a new token, go to your profile in the Manifest App and click on the Create New Token button. Then, fill out the form and click "confirm".

Once you have successfully created your key, copy it and save it in a secure location.

Remember to protect your API key! Avoid committing it to your source code or printing it as plain text. Instead, use secrets management tools to keep it secure 🧙.

### Usage

The recommended way to use the API Key is via the `MANIFEST_API_KEY` environment variable.
```bash
export MANIFEST_API_KEY=rrcoRKdmTwMFhE40k0t2s7JgJui07x0wX2kByXkJ0MM=
manifest merge --paths=sbom1.json,sbom2.json
```

## Contact
Have any questions or need help? Don't hesitate to reach out: info@manifestcyber.com!








============================
### Devcontainer

1. Docker
2. VSCode
3. Remote-Containers [extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

### Native

1. Go 1.19+
2. Make

## Getting Started

First, clone this repository and make sure you have your enviornment all setup. </br>
For VSCode users, you can simply run this project in a container: </br>
![Remote Containers Popup](remote-containers.png?raw=true "RemoteContainers")

The option would be available once you install the recommended extension.

### Run

Then, simple run:

```bash
make run
```

### Test

Again, as easy as running:

```bash
make test
```


Docker SBOM:
mkdir ~/.docker && wget -O - https://raw.githubusercontent.com/docker/sbom-cli-plugin/main/install.sh | sh

SPDX Generator:
wget -qO- https://github.com/opensbom-generator/spdx-sbom-generator/releases/download/v0.0.15/spdx-sbom-generator-v0.0.15-linux-arm64.tar.gz | sudo tar -xz -C /usr/local/bin

Trivy:
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy

