# Use Ubuntu 24.04 LTS as base image (stable, long-term support)
FROM ubuntu:24.04

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install essential system dependencies:
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        sudo \
        openjdk-17-jdk \
        git \
        unzip \
        wget \
        curl \
        libglu1-mesa \
        lib32stdc++6 \
        lib32z1 \
        clang \
        cmake \
        ninja-build \
        pkg-config \
        libgtk-3-dev \
        ca-certificates && \
        echo "vscode ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    # Clean up the apt cache immediately in the same layer to reduce final image size
    rm -rf /var/lib/apt/lists/*

# Set Java environment variables
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV PATH="${JAVA_HOME}/bin:${PATH}"

# Create a non-root user to run Flutter and SDK commands (recommended)
RUN useradd -m -s /bin/bash vscode
USER vscode
WORKDIR /home/vscode

# === Install Android Command-Line Tools ===
# Android SDK Platform 34 corresponds to Android 14 (API level 34)
ENV ANDROID_SDK_ROOT=/home/vscode/android-sdk
ENV ANDROID_CMD_TOOLS_VERSION=11076708_latest
ENV PATH="${ANDROID_SDK_ROOT}/cmdline-tools/latest/bin:${ANDROID_SDK_ROOT}/platform-tools:${PATH}"

RUN mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools && \
    cd ${ANDROID_SDK_ROOT}/cmdline-tools && \
    # 1. Download and set up cmdline-tools
    wget -q https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_CMD_TOOLS_VERSION}.zip && \
    unzip commandlinetools-linux-${ANDROID_CMD_TOOLS_VERSION}.zip && \
    mv cmdline-tools latest && \
    rm commandlinetools-linux-${ANDROID_CMD_TOOLS_VERSION}.zip && \
    # 2. Accept licenses (essential) and install platforms/build tools
    yes | sdkmanager --sdk_root=${ANDROID_SDK_ROOT} --licenses > /dev/null && \
    sdkmanager --sdk_root=${ANDROID_SDK_ROOT} \
        "platform-tools" \
        "platforms;android-34" \
        "build-tools;34.0.0" \
        "extras;google;google_play_services"

# === Install Flutter ===
ENV FLUTTER_HOME=/home/vscode/flutter
ENV PATH="${FLUTTER_HOME}/bin:${PATH}"

RUN git clone https://github.com/flutter/flutter.git -b 3.22.3 ${FLUTTER_HOME} && \
    yes | flutter doctor --android-licenses && \
    flutter precache && \
    flutter config --no-analytics && \
    flutter config --no-enable-web

# Default command: open a shell
CMD ["/bin/bash"]