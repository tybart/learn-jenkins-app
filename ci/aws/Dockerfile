FROM ubuntu:resolute

# Install sudo and other dependencies
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    ca-certificates \
    docker.io \
    jq \
    && rm -rf /var/lib/apt/lists/*

# Install AWS CLI v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm -rf aws awscliv2.zip

RUN aws --version && docker --version