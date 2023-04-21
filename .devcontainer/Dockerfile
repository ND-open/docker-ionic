FROM ubuntu:20.04

RUN apt-get update && apt-get install tzdata -y
ENV TZ="Europe/Paris"

# ENV LANGUAGE = "fr_FR.UTF-8",
# ENV LC_ALL = "fr_FR.UTF-8",
# ENV LANG = "fr_FR.UTF-8"

# This Dockerfile adds a non-root user with access.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

## Acces to USB Device
# RUN sudo -t -i --privileged -v /dev/bus/usb:/dev/bus/usb ubuntu bash

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Set the default user
USER $USERNAME

RUN sudo apt-get clean
RUN sudo apt-get update --fix-missing
RUN sudo apt-get install gnupg -y \
    && sudo apt-get install git -y \
    && sudo apt-get install python3-pip -y \
    && sudo apt-get install apt-transport-https -y \
    && sudo apt-get install vim -y \
    && sudo apt-get install curl -y \
    && sudo apt-get install unzip -y \
    && sudo apt-get install nano -y \
    && sudo apt-get install psmisc -y \
    && sudo apt-get install debconf-utils -y \
    && sudo apt-get install wget -y \
    && sudo apt-get install gnupg2 -y \
    && sudo apt-get install usbutils -y
RUN sudo apt-get update --fix-missing
RUN export EDITOR="/usr/bin/nano"

# install nodejs 18.x and stuff
RUN sudo curl -sL https://deb.nodesource.com/setup_18.x | sudo bash -
RUN sudo apt-get install -y nodejs
RUN sudo npm i -g serve@14.0.1
RUN sudo npm i -g gitmoji-cli@7.0.1
RUN sudo npm i -g axios

# install nest 😺
RUN sudo npm i -g @nestjs/cli@9.1.4

# install Ionic CLI ⚛️
RUN sudo npm i -g @ionic/cli

RUN sudo curl --create-dirs -L -o /etc/udev/rules.d/51-android.rules https://raw.githubusercontent.com/snowdream/51-android/master/51-android.rules
RUN sudo chmod a+r /etc/udev/rules.d/51-android.rules

ENV ANDROID_HOME="/android-sdk" \
    ANDROID_SDK_HOME="/android-sdk/sdk" \
    ANDROID_SDK_ROOT="/android-sdk" \
    ANDROID_NDK="/android-sdk/ndk/latest" \
    ANDROID_NDK_ROOT="/android-sdk/ndk/latest" \
    FLUTTER_HOME="/flutter"
ENV ANDROID_SDK_MANAGER=${ANDROID_HOME}/cmdline-tools/latest/bin/sdkmanager

# Installing packages
RUN sudo apt-get update
RUN sudo apt-get install -y openjdk-8-jdk openjdk-11-jdk gradle

RUN export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64/"

# Get the latest version from https://developer.android.com/studio/index.html
# "9123335" as of 2023/01/11
ENV ANDROID_SDK_TOOLS_VERSION="9123335"

#bundletool version
ENV BUNDLETOOL_VERSION="1.14.0"

#Flutter Version
# ENV FLUTTER_VERSION="3.7.7"

RUN sudo apt-get clean && \
    sudo apt-get update -qq && \
    sudo apt-get install -qq -y apt-utils locales && \
    sudo locale-gen $LANG

ENV DEBIAN_FRONTEND="noninteractive" \
    TERM=dumb \
    DEBIAN_FRONTEND=noninteractive

# Variables must be references after they are created
ENV ANDROID_SDK_HOME="$ANDROID_HOME"
ENV ANDROID_NDK_HOME="$ANDROID_NDK"

ENV PATH="$JAVA_HOME/bin:$PATH:$ANDROID_SDK_HOME/emulator:$ANDROID_SDK_HOME/cmdline-tools/latest/bin:$ANDROID_SDK_HOME/tools:$ANDROID_SDK_HOME/platform-tools:$ANDROID_NDK:$FLUTTER_HOME/bin:$FLUTTER_HOME/bin/cache/dart-sdk/bin"

WORKDIR /tmp

# Install Android SDK
RUN echo "sdk tools ${ANDROID_SDK_TOOLS_VERSION}" && \
    sudo wget --quiet --output-document=sdk-tools.zip \
    "https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_TOOLS_VERSION}_latest.zip" && \
    sudo mkdir --parents "$ANDROID_HOME" && \
    sudo unzip -q sdk-tools.zip -d "$ANDROID_HOME" && \
    cd "$ANDROID_HOME" && \
    sudo mv cmdline-tools latest && \
    sudo mkdir cmdline-tools && \
    sudo mv latest cmdline-tools && \
    sudo rm --force sdk-tools.zip

USER root

RUN sudo mkdir --parents "$ANDROID_HOME/.android/" && \
    echo '### User Sources for Android SDK Manager' > \
    "$ANDROID_HOME/.android/repositories.cfg" && \
    yes | sudo $ANDROID_SDK_MANAGER --licenses

RUN yes | sudo $ANDROID_SDK_MANAGER "platforms;android-32" 

RUN sudo echo "platform tools" && \
    yes | sudo $ANDROID_SDK_MANAGER \
    "platform-tools"

RUN sudo echo "build tools 27-33" && \
    yes | sudo $ANDROID_SDK_MANAGER "build-tools;33.0.0"

EXPOSE 5000

USER $USERNAME

RUN sudo chmod -R a+rwx /android-sdk

WORKDIR /docker-ionic