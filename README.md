# Manifest manifest-cli 

## Overview

## Prerequisites

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

