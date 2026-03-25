FROM mcr.microsoft.com/devcontainers/base:ubuntu

ENV DEBIAN_FRONTEND=noninteractive

# Install base dependencies
RUN apt-get update && apt-get install -y \
    curl git unzip xz-utils zip libglu1-mesa openjdk-17-jdk wget \
    && rm -rf /var/lib/apt/lists/*

# Set Java (required for Android builds)
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

# Install Android SDK
ENV ANDROID_HOME=/opt/android-sdk
RUN mkdir -p $ANDROID_HOME/cmdline-tools

WORKDIR $ANDROID_HOME/cmdline-tools

RUN wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O tools.zip \
    && unzip tools.zip \
    && rm tools.zip \
    && mv cmdline-tools latest

ENV PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools

# Accept licenses + install SDK packages
RUN yes | sdkmanager --licenses

RUN sdkmanager \
    "platform-tools" \
    "platforms;android-33" \
    "build-tools;33.0.2"

# Install Flutter
WORKDIR /opt
RUN git clone https://github.com/flutter/flutter.git -b stable

ENV PATH="/opt/flutter/bin:/opt/flutter/bin/cache/dart-sdk/bin:${PATH}"

# Pre-cache Flutter artifacts
RUN flutter doctor && flutter precache

# Set working directory
WORKDIR /workspaces

# Install SSH server
RUN apt-get update && apt-get install -y openssh-server \
    && mkdir /var/run/sshd

# Allow passwordless login for vscode user
RUN echo "vscode ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN service ssh start
