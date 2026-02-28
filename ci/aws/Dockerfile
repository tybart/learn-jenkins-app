FROM ubuntu:resolute

# Install sudo and other dependencies
RUN apt-get update && apt-get install -y \
    sudo \
    curl \
    unzip \
    ca-certificates \
    docker.io \
    && rm -rf /var/lib/apt/lists/*

# Install AWS CLI v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm -rf aws awscliv2.zip

# Create or modify user jenkins to use sudo without password
RUN useradd -ms /bin/bash jenkins \
    && echo "jenkins ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER jenkins
WORKDIR /home/jenkins

RUN aws --version && docker --version