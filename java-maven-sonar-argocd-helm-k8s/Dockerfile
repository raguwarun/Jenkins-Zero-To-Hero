##Docker Agent with maven, openjdk17 and docker installed##

FROM maven:3.8.5-openjdk-17

# Install necessary packages for Docker
RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up the stable Docker repository
RUN echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
RUN apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io

# Verify Docker installation (optional)
RUN docker --version

# Set the working directory for Maven
WORKDIR /app

# You can copy your project files here in subsequent steps
# For example:
# COPY pom.xml .
# COPY src ./src

# Define the default command (you'll likely override this in your CI/CD pipeline)
CMD ["echo", "Maven and Docker are installed in this image"]