# Stage 1: Base image with essential build tools
FROM ubuntu:latest AS base-builder
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    gnupg \
    software-properties-common \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# Stage 2: JDK installation
FROM base-builder AS jdk-builder
ENV JAVA_VERSION=17
RUN apt-get update && apt-get install -y --no-install-recommends openjdk-${JAVA_VERSION}-jdk-headless \
    && rm -rf /var/lib/apt/lists/*

# Stage 3: Maven installation
FROM base-builder AS maven-builder
ENV MAVEN_VERSION=3.9.9
ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH
RUN curl -fsSL https://downloads.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz -o /tmp/maven.tar.gz \
    && tar xzf /tmp/maven.tar.gz -C /opt \
    && mv /opt/apache-maven-${MAVEN_VERSION} $MAVEN_HOME \
    && rm /tmp/maven.tar.gz

# Stage 4: Docker installation
FROM ubuntu:latest AS docker-builder
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    gnupg \
    software-properties-common \
    apt-transport-https \
    lsb-release \
    iptables \
    procps \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
RUN apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io \
    && rm -rf /var/lib/apt/lists/*

# Stage 5: Final image - minimal with JRE, Maven, and Docker
FROM ubuntu:latest

ARG JAVA_VERSION=17

# Install a minimal JRE - this often handles security configuration better
RUN apt-get update && apt-get install -y --no-install-recommends openjdk-${JAVA_VERSION}-jre-headless \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-${JAVA_VERSION}-openjdk-amd64
ENV PATH=$PATH:$JAVA_HOME/bin

# Copy Maven from maven-builder
COPY --from=maven-builder /opt/maven /opt/maven
ENV MAVEN_HOME=/opt/maven
ENV PATH=$PATH:$MAVEN_HOME/bin

# Install necessary dependencies for Docker in the final stage
RUN apt-get update && apt-get install -y --no-install-recommends \
    iptables \
    procps \
    && rm -rf /var/lib/apt/lists/*

# Copy Docker binaries and libraries from docker-builder
COPY --from=docker-builder /usr/bin/docker /usr/bin/docker
COPY --from=docker-builder /usr/bin/dockerd /usr/bin/dockerd
COPY --from=docker-builder /usr/lib/systemd/system/docker.service /etc/systemd/system/docker.service

# Create the docker group if it doesn't exist
RUN groupadd docker || true
# Add a non-root user to the docker group (optional, for better security)
RUN useradd -ms /bin/bash builder && usermod -aG docker builder

# Switch to the builder user (optional)
USER builder

# Expose Maven default port (optional)
EXPOSE 8080

# Set the working directory
WORKDIR /app

# Default command
CMD ["bash"]