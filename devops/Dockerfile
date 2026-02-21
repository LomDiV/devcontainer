ARG PYTHON_VERSION="3.12"
FROM python:${PYTHON_VERSION}-slim

# Default releases - can be overridden via --build-arg
# Example: docker build --build-arg PYTHON_VERSION=3.12 --build-arg ANSIBLE_RELEASE=9 --build-arg KUBECTL_RELEASE=1.29 --build-arg HELM_RELEASE=3 .
ARG ANSIBLE_RELEASE="13"
ARG KUBECTL_RELEASE="1.34"
ARG HELM_RELEASE="4"

ARG USERNAME=dev
ARG USER_UID=1001
ARG USER_GID=1001

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies
# curl: to download kubectl
# git: for ansible galaxy requirements
# openssh-client: for ansible ssh connections
# sshpass: for ansible password auth if needed
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    git \
    jq \
    nano \
    tar \
    openssh-client \
    sshpass \
    wget \
    procps \
    zip \
    unzip \
    apt-utils \
    ca-certificates && \
    echo 'Adding docker repository...' && \
    install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc && \
    chmod a+r /etc/apt/keyrings/docker.asc && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && apt-get install -y --no-install-recommends \
    docker-ce-cli \
    docker-buildx-plugin \
    docker-compose-plugin &&\
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    update-ca-certificates



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

# Create a non-root user 
RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m $USERNAME -s /bin/bash

# Switch to user
USER $USERNAME
WORKDIR /home/$USERNAME 

# Update PATH for user installed pip packages
ENV PATH="/home/$USERNAME/.local/bin:${PATH}"

# Install Ansible and typically used Python dependencies
# kubernetes: Python client for K8s (required for k8s_module)
# jmespath: Required for json_query filter
RUN pip install --no-cache-dir --user \
    "ansible==${ANSIBLE_RELEASE}.*" \
    kubernetes \
    jmespath

# Verify installations
RUN ansible --version && kubectl version --client && helm version

# Add version output to bashrc
RUN { \
    echo 'echo -e "\n\033[1;36m===================================================================\033[0m"'; \
    echo 'echo -e "\033[1;32m                         DevContainer Tools                        \033[0m"'; \
    echo 'echo -e "\033[1;36m===================================================================\033[0m"'; \
    echo 'echo -e "Ansible:"'; \
    echo 'ansible --version | head -n 1'; \
    echo 'echo -e "Kubectl:"'; \
    echo 'kubectl version --client'; \
    echo 'echo -e "Helm:"'; \
    echo 'helm version'; \
    echo 'echo -e "\033[1;36m===================================================================\033[0m\n"'; \
    echo "export A_VER=\$(ansible --version | head -n 1 | awk '{print \$3}' | tr -d ']')"; \
    echo "export K_VER=\$(kubectl version --client | grep 'Client Version:' | awk '{print \$3}')"; \
    echo "export H_VER=\$(helm version --short | awk -F+ '{print \$1}')"; \
    echo "export PS1=\"\\[\\033[01;32m\\]\\u@\\h\\[\\033[00m\\] \\[\\033[01;33m\\][a:\${A_VER} k:\${K_VER} h:\${H_VER}]\\[\\033[00m\\]:\\[\\033[01;34m\\]\\w\\[\\033[00m\\]\\$ \""; \
    } >> ~/.bashrc

CMD ["/bin/bash"]
