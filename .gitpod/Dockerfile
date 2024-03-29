FROM gitpod/workspace-full-vnc
SHELL ["/bin/bash", "-c"]

############################################################
########################## LINUX ###########################
############################################################

USER root
RUN apt update \
	&& apt install -y cmake

############################################################
######################### ANDROID ##########################
############################################################

ENV ANDROID_HOME=$HOME/androidsdk
ENV PATH="$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH"

# Install Open JDK for android and other dependencies
USER root
RUN install-packages openjdk-8-jdk -y \
        libgtk-3-dev \
        libnss3-dev \
        fonts-noto \
        fonts-noto-cjk \
    && update-java-alternatives --set java-1.8.0-openjdk-amd64

# Install Android SDK Manager and Android 33
USER gitpod
RUN _file_name="commandlinetools-linux-9123335_latest.zip" && wget "https://dl.google.com/android/repository/$_file_name" \
    && unzip "$_file_name" -d $ANDROID_HOME \
    && rm -f "$_file_name" \
    && mkdir -p $ANDROID_HOME/cmdline-tools/latest \
    && mv $ANDROID_HOME/cmdline-tools/{bin,lib} $ANDROID_HOME/cmdline-tools/latest \
    && yes | sdkmanager "platform-tools" "build-tools;33.0.0" "platforms;android-33"

############################################################
############################ WEB ###########################
############################################################

# Install Google Chrome
USER root
RUN apt update \
  && apt install -y apt-transport-https \
  && curl -sSL https://dl.google.com/linux/linux_signing_key.pub | apt-key add - \
  && echo "deb [arch=amd64] https://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list \
  && apt update \
  && apt install -y google-chrome-stable

# Install Microsoft Edge
USER root
RUN curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg \
  && install -o root -g root -m 644 microsoft.gpg /usr/share/keyrings/ \
  && sh -c 'echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/edge stable main" > /etc/apt/sources.list.d/microsoft-edge-dev.list' \
  && rm microsoft.gpg \
  && apt update \
  && apt install -y microsoft-edge-dev

# Install Flatpak, FlatHub and Gnome Epiphany
USER root
RUN apt install -y flatpak \
  && flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo \
  && flatpak install -y flathub org.gnome.Epiphany

############################################################
######################### FLUTTER ##########################
############################################################

USER gitpod
WORKDIR /home/gitpod

ENV FLUTTER_VERSION=3.3.10
RUN git clone https://github.com/flutter/flutter -b ${FLUTTER_VERSION}

ENV PATH="$HOME/flutter/bin:$PATH"
ENV PUB_CACHE="/workspace/.pub_cache"

# Enable Flutter Web, Android and Linux Desktop
# Also doing precache, to increase workspace init
RUN flutter precache && for _plat in web linux-desktop; do flutter config --enable-${_plat}; done \
  && flutter config --android-sdk $ANDROID_HOME \
  && yes | flutter doctor --android-licenses \
  && flutter doctor

############################################################
########################### MISC ###########################
############################################################

# For Qt WebEngine on Docker
ENV QTWEBENGINE_DISABLE_SANDBOX 1
