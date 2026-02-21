ARG PYTHON_VERSION="3.12"
FROM python:${PYTHON_VERSION}-slim

# Default releases - can be overridden via --build-arg
# Example: docker build --build-arg PYTHON_VERSION=3.12 --build-arg ANSIBLE_RELEASE=9 --build-arg KUBECTL_RELEASE=1.29 --build-arg HELM_RELEASE=3 .
ARG ANSIBLE_RELEASE="13"
ARG KUBECTL_RELEASE="1.34"
ARG HELM_RELEASE="4"

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies
# curl: to download kubectl
# git: for ansible galaxy requirements
# openssh-client: for ansible ssh connections
# sshpass: for ansible password auth if needed
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    git \
    openssh-client \
    sshpass \
    wget \
    procps \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*



# Install kubectl (latest patch for given minor release)
RUN KUBECTL_VERSION=$(curl -sL "https://dl.k8s.io/release/stable-${KUBECTL_RELEASE}.txt") \
    && curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" \
    && chmod +x kubectl \
    && mv kubectl /usr/local/bin/kubectl

# Install Helm (latest release for given major/minor prefix)
RUN HELM_VERSION=$(git ls-remote --tags https://github.com/helm/helm.git | awk -F/ '{print $3}' | sed 's/\^{}//' | grep "^v${HELM_RELEASE}" | sort -V | tail -n1) \
    && curl -LO "https://get.helm.sh/helm-${HELM_VERSION}-linux-amd64.tar.gz" \
    && tar -zxvf helm-${HELM_VERSION}-linux-amd64.tar.gz \
    && mv linux-amd64/helm /usr/local/bin/helm \
    && rm -rf linux-amd64 helm-${HELM_VERSION}-linux-amd64.tar.gz

# Create a non-root user 'dev'
RUN useradd -m -s /bin/bash dev

# Switch to 'dev' user
USER dev
WORKDIR /home/dev

# Update PATH for user installed pip packages
ENV PATH="/home/dev/.local/bin:${PATH}"

# Install Ansible and typically used Python dependencies
# kubernetes: Python client for K8s (required for k8s_module)
# jmespath: Required for json_query filter
RUN pip install --no-cache-dir --user \
    "ansible==${ANSIBLE_RELEASE}.*" \
    kubernetes \
    jmespath

# Verify installations
RUN ansible --version && kubectl version --client && helm version

CMD ["/bin/bash"]
