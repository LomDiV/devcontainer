# DevOps DevContainer

A fully featured, Docker-based Development environment specifically crafted for interacting with Kubernetes Clusters, automating deployments with Ansible, and packaging Helm charts seamlessly. It includes a completely automated pipeline utilizing GitHub Actions to push updated container variants directly to the GitHub Container Registry (ghcr.io).

## 🚀 Features

This container is based on **Debian** (via the `python-slim` base image) and comes pre-configured with the following essential DevOps tools:

- **Debian Linux**
- **Python**
- **Ansible** (Core) + `jmespath` filter support
- **Kubernetes Client (kubectl)**
- **Helm** 

When logging into the container via an interactive bash session, your terminal prompt (`PS1`) dynamically displays the loaded, exact versions of Ansible, Kubectl, and Helm to assist debugging. 

## ⚙️ Configuration & Tool Versions

To keep track of and rapidly upgrade tools, base versions for all key utilities are extracted dynamically from the `versions.env` file located in the root directory. 

Currently tracking:
- `PYTHON_VERSION`
- `ANSIBLE_RELEASE`
- `KUBECTL_RELEASE`
- `HELM_RELEASE`

Docker caches will be re-evaluated against the latest compatible minor versions specified automatically upon a rebuild.

## 🛠️ DevContainer Usage

You can use this DevContainer directly inside **VS Code** (or any editor supporting the DevContainers standard). 

Below is an `example/devcontainer.json` to get started quickly:

```json
{
    "name": "DevOps DevContainer",
    "image": "ghcr.io/lomdiv/devcontainer-devops:latest",
    "customizations": {
        "vscode": {
            "extensions": [
                "ms-python.python",
                "redhat.ansible",
                "ms-kubernetes-tools.vscode-kubernetes-tools"
            ]
        }
    },
    "remoteUser": "dev"
}
```
