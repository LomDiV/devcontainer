# DevContainers Collection

A collection of fully featured, Docker-based Development environments mainly intended for my personal home lab environment. Includes a container specifically crafted for interacting with Kubernetes Clusters (DevOps), and another for Java/Groovy application development. Both include completely automated pipelines utilizing GitHub Actions to push updated container variants directly to the GitHub Container Registry (ghcr.io).

## 🚀 Images Available

### 1. DevOps DevContainer (`devops`)

## 🚀 Features

This container is based on **Debian** (via the `python-slim` base image) and comes pre-configured with the following essential DevOps tools:

- **Debian Linux**
- **Python**
- **Ansible** (Core) + `jmespath` filter support
- **Kubernetes Client (kubectl)**
- **Helm** 
- **Docker CLI** (`docker-ce-cli`, `docker-buildx-plugin`, `docker-compose-plugin`)
- **Common Utilities** (`jq`, `nano`, `tar`, `zip`, `unzip`, `wget`, `curl`, `apt-utils`)
- **Configurable OS User** (matches container user UID/GID to host permissions)

### 2. Groovy DevContainer (`groovy`)

This container is based on **Eclipse Temurin** (JDK) and is pre-configured for Java and Groovy development:

- **Java (JDK)**
- **Groovy**
- **Gradle**
- **Common Utilities** (`jq`, `nano`, `tar`, `zip`, `unzip`, `wget`, `curl`, `apt-utils`)
- **Configurable OS User** (matches container user UID/GID to host permissions)

When logging into the containers via an interactive bash session, your terminal prompt (`PS1`) dynamically displays the loaded, exact versions of the installed primary tools to assist debugging. 

## ⚙️ Configuration & Tool Versions

To keep track of and rapidly upgrade tools, base versions for all key utilities are extracted dynamically from the `versions.env` file located in the root directory. 

Currently tracking:
- `PYTHON_VERSION`
- `ANSIBLE_RELEASE`
- `KUBECTL_RELEASE`
- `HELM_RELEASE`
- `JAVA_VERSION`
- `GROOVY_VERSION`
- `GRADLE_VERSION`

User Configuration (to ensure proper file permissions with the host):
- `USERNAME` (Default: `dev`)
- `USER_UID` (Default: `1001`)
- `USER_GID` (Default: `1001`)

Docker caches will be re-evaluated against the latest compatible minor versions specified automatically upon a rebuild.

## 🛠️ DevContainer Usage

You can use these DevContainers directly inside **VS Code** (or any editor supporting the DevContainers standard). 

Below are examples of `.devcontainer.json` files to get started quickly. You can find full examples in the `devops/example/.devcontainer` and `groovy/example/.devcontainer` directories.

### DevOps Example

```json
{
    "name": "DevOps DevContainer",
    "image": "ghcr.io/lomdiv/devcontainer-devops:latest",
    "customizations": {
        "vscode": {
            "extensions": [
                "redhat.ansible",
                "redhat.vscode-yaml",
                "ms-kubernetes-tools.vscode-kubernetes-tools",
                "ms-azuretools.vscode-docker"
            ]
        }
    },
    "remoteUser": "dev",
    "mounts": [
        "source=${localEnv:HOME}/.kube,target=/home/dev/.kube,type=bind"
    ]
}
```

### Groovy Example

```json
{
    "name": "Groovy DevContainer",
    "image": "ghcr.io/lomdiv/devcontainer-groovy:latest",
    "customizations": {
        "vscode": {
            "extensions": [
                "vscjava.vscode-java-pack",
                "vscjava.vscode-gradle",
                "marlon407.code-groovy"
            ]
        }
    },
    "remoteUser": "dev",
    "mounts": [
        "source=${localEnv:HOME}/.gradle,target=/home/dev/.gradle,type=bind"
    ]
}
```
