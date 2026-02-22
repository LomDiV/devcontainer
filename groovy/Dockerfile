ARG JAVA_VERSION="21"
FROM eclipse-temurin:${JAVA_VERSION}-jdk

# Default releases - can be overridden via --build-arg
# Example: docker build --build-arg JAVA_VERSION=21 --build-arg GROOVY_VERSION=4.0.25 --build-arg GRADLE_VERSION=8.12.1 .
ARG GROOVY_VERSION="4.0.25"
ARG GRADLE_VERSION="8.12.1"

ARG USERNAME=dev
ARG USER_UID=1001
ARG USER_GID=1001

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    git \
    jq \
    nano \
    tar \
    openssh-client \
    wget \
    procps \
    zip \
    unzip \
    apt-utils \
    ca-certificates \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Groovy
RUN curl -s -L "https://archive.apache.org/dist/groovy/${GROOVY_VERSION}/distribution/apache-groovy-binary-${GROOVY_VERSION}.zip" -o /tmp/groovy.zip \
    && unzip /tmp/groovy.zip -d /opt/ \
    && rm /tmp/groovy.zip \
    && ln -s /opt/groovy-${GROOVY_VERSION} /opt/groovy

ENV GROOVY_HOME=/opt/groovy
ENV PATH="${GROOVY_HOME}/bin:${PATH}"

# Install Gradle (Essential for Groovy projects)
RUN curl -s -L "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" -o /tmp/gradle.zip \
    && unzip /tmp/gradle.zip -d /opt/ \
    && rm /tmp/gradle.zip \
    && ln -s /opt/gradle-${GRADLE_VERSION} /opt/gradle

ENV GRADLE_HOME=/opt/gradle
ENV PATH="${GRADLE_HOME}/bin:${PATH}"

# Create a non-root user 
RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m $USERNAME -s /bin/bash

# Switch to user
USER $USERNAME
WORKDIR /home/$USERNAME 

# Verify installations
RUN java --version && groovy --version && gradle --version

# Add version output to bashrc
RUN { \
    echo 'echo -e "\n\033[1;36m===================================================================\033[0m"'; \
    echo 'echo -e "\033[1;32m                         Groovy DevContainer                       \033[0m"'; \
    echo 'echo -e "\033[1;36m===================================================================\033[0m"'; \
    echo 'echo "Java:"'; \
    echo 'java --version | head -n 1'; \
    echo 'echo "Groovy:"'; \
    echo 'groovy --version | head -n 1'; \
    echo 'echo "Gradle:"'; \
    echo 'gradle --version | head -n 1'; \
    echo 'echo -e "\033[1;36m===================================================================\033[0m\n"'; \
    echo "export J_VER=\$(java --version | head -n 1 | awk '{print \$2}')"; \
    echo "export GR_VER=\$(groovy --version | head -n 1 | awk '{print \$3}')"; \
    echo "export GD_VER=\$(gradle --version | head -n 1 | awk '{print \$2}')"; \
    echo "export PS1=\"\\[\\033[01;32m\\]\\u@\\h\\[\\033[00m\\] \\[\\033[01;33m\\][j:\${J_VER} go:\${GR_VER} gd:\${GD_VER}]\\[\\033[00m\\]:\\[\\033[01;34m\\]\\w\\[\\033[00m\\]\\$ \""; \
    } >> ~/.bashrc

CMD ["/bin/bash"]
