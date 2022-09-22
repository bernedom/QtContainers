FROM ubuntu:bionic

ARG QT_VERSION=5.15.2
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ARG NDK_TOOLCHAIN_PREFIX=arm-linux-androideabi
ARG NDK_VERSION=r20b
ARG NDK_URL="https://dl.google.com/android/repository/android-ndk-${NDK_VERSION}-linux-x86_64.zip"
ARG NDK_SHA256="8381c440fe61fcbb01e209211ac01b519cd6adf51ab1c2281d5daad6ca4c8c8c"

ARG SDK_URL="http://dl.google.com/android/repository/sdk-tools-linux-4333796.zip"
ARG SDK_SHA256="92ffee5a1d98d856634e8b71132e8a95d96c83a63fde1099be3d86df3106def9"
ARG SDK_PLATFORM=android-28
ARG SDK_INSTALL_DIR=/opt/android-sdk
ARG SDK_BUILD_TOOLS=28.0.3
ARG SDK_PACKAGES="tools platform-tools"
ENV ANDROID_HOME=${SDK_INSTALL_DIR} \
    ANDROID_SDK_ROOT=${SDK_INSTALL_DIR} \
    ANDROID_SDK_BUILD_TOOLS=${SDK_BUILD_TOOLS} \
    ANDROID_NDK_ROOT=/opt/android-ndk \
    ANDROID_NDK_TOOLS_PREFIX=${NDK_TOOLCHAIN_PREFIX} \
    ANDROID_NDK_TOOLCHAIN_PREFIX=${NDK_TOOLCHAIN_PREFIX} \
    ANDROID_NDK_TOOLCHAIN_VERSION=4.9 \
    ANDROID_NDK_TOOLCHAIN_ABI=armeabi-v7a \
    ANDROID_NDK_HOST=linux-x86_64 \
    ANDROID_NDK_PLATFORM=${SDK_PLATFORM}  

ENV PATH=${PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools

# Build and install Qt from source
RUN apt-get update && apt-get -qq install -y build-essential python python3 git perl \
    mesa-common-dev libglu1-mesa-dev libfontconfig1-dev libfreetype6-dev libx11-dev \
    libxext-dev libxfixes-dev libxi-dev libxrender-dev libxcb1-dev libx11-xcb-dev \
    libxcb-glx0-dev libxkbcommon-x11-dev libssl-dev libxkbcommon-dev libxkbcommon-x11-dev \
    libxcb-xinerama0-dev libxcb-render-util0-dev  libxcb-icccm4-dev libxcb-util-dev libxcb-shm0-dev \
    libxcb-image0-dev libxcb-keysyms1-dev \
    bluez libbluetooth-dev clang openjdk-8-jdk-headless openjdk-8-jre-headless curl unzip


# Download & unpack android SDK
RUN echo "${SDK_SHA256} -" > sum.txt && curl -fLs "${SDK_URL}" | tee /tmp/sdk-tools.zip | sha256sum -c sum.txt \
    && mkdir -p /opt/android-sdk && unzip -q /tmp/sdk-tools.zip -d /opt/android-sdk && rm -f /tmp/sdk-tools.zip \
    && yes | sdkmanager --licenses && sdkmanager --update && sdkmanager --verbose "platforms;${SDK_PLATFORM}" "build-tools;${SDK_BUILD_TOOLS}" ${SDK_PACKAGES}

# Download & unpack android NDK
RUN mkdir /tmp/android && cd /tmp/android && echo "${NDK_SHA256} -" > sum.txt && curl -fLs "${NDK_URL}" | tee ndk.zip | sha256sum -c sum.txt \
    && unzip -q ndk.zip && mv android-ndk-* $ANDROID_NDK_ROOT && chmod -R +rX $ANDROID_NDK_ROOT \
    && rm -rf /tmp/android

RUN git clone https://code.qt.io/qt/qt5.git
WORKDIR qt5
RUN git checkout v${QT_VERSION} && perl init-repository


WORKDIR ./qt-build
RUN ../configure -xplatform android-clang -disable-rpath -opensource -nomake examples -nomake tests -confirm-license -android-ndk ${ANDROID_NDK_ROOT} -android-sdk ${ANDROID_SDK_ROOT} -no-warnings-are-errors -prefix "/usr/local/Qt/"
RUN make -s -j$(nproc) && make install
WORKDIR /
RUN rm -rf qt && rm -rf ./qt-build
