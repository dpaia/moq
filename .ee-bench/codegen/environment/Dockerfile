FROM mcr.microsoft.com/dotnet/sdk:8.0-jammy
# Ubuntu 22.04

ENV DEBIAN_FRONTEND=noninteractive

# Ubuntu server blocks a lot of requests, so we use a mirror (version to Ubuntu 22 and older)
RUN sed -i 's|http://.*.ubuntu.com|http://eu-west-1.ec2.archive.ubuntu.com|g' /etc/apt/sources.list && apt-get update

# Install common packages
RUN apt-get update && \
    apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:git-core/ppa && \
    apt-get update && \
    apt-get install -y \
    build-essential \
    git \
    curl \
    wget \
    sudo \
    tar \
    zip \
    unzip \
    autoconf \
    # Required for env
    libcurl4-gnutls-dev \
    libexpat1-dev \
    libicu-dev \
    libicu70 \
    # Required for installing Git
    zlib1g-dev \
    gettext \
    # Required for Junie
    libsecret-1-0 \
    libsecret-1-dev \
    rsync \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y wget && \
    wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.0g-2ubuntu4_amd64.deb && \
    dpkg -i libssl1.1_1.1.0g-2ubuntu4_amd64.deb && \
    rm -rf /var/lib/apt/lists/*

RUN wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh && \
    chmod +x ./dotnet-install.sh && \
    ./dotnet-install.sh --version 3.1.426 --install-dir /usr/share/dotnet && \
    ./dotnet-install.sh --version 6.0.428 --install-dir /usr/share/dotnet

ENV PATH="/usr/share/dotnet:${PATH}"

RUN apt-get update && \
    apt-get install -y python3 python3-pip && \
    rm -rf /var/lib/apt/lists/*

RUN git clone https://github.com/{{ instance.owner }}/{{ instance.repo_name }}.git /app
WORKDIR /app
RUN git checkout {{ instance.base_commit }}

RUN rm -rf "./tests/Moq.Tests/MockRepositoryFixture.cs" && \
    dotnet add "./tests/Moq.Tests/Moq.Tests.csproj" package JUnitTestLogger && \
    dotnet build

LABEL ee-bench.type="codegen"
LABEL ee-bench.version="1.0"
RUN rm -rf /app/.ee-bench/ 2>/dev/null || true
