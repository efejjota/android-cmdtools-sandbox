FROM alpine:3.12.3 as fetcheck
RUN apk add curl bash
COPY fetcheck /bin/fetcheck
RUN chmod +x /bin/fetcheck

FROM fetcheck as clitools
RUN fetcheck clitools.zip \
    https://dl.google.com/android/repository/commandlinetools-linux-6858069_latest.zip \
    87f6dcf41d4e642e37ba03cb2e387a542aa0bd73cb689a9e7152aad40a6e7a08
RUN unzip clitools.zip

FROM fetcheck as jdk11
RUN fetcheck jdk11.tar.gz \
    https://github.com/AdoptOpenJDK/openjdk11-binaries/releases/download/jdk-11.0.9.1%2B1/OpenJDK11U-jdk_x64_linux_hotspot_11.0.9.1_1.tar.gz \
    e388fd7f3f2503856d0b04fde6e151cbaa91a1df3bcebf1deddfc3729d677ca3
RUN tar -zxvf jdk11.tar.gz

# https://medium.com/@barcelos.ds/install-android-sdk-in-the-ubuntu-20-04-lts-without-android-studio-1b629924d6c5
FROM ubuntu:focal as sdkmanager
ENV JAVA_HOME="/root/DevTools/JDK/jdk-11.0.9.1+1"
ENV ANDROID_HOME="/root/DevTools/Android"
ENV PATH="$JAVA_HOME/bin:$ANDROID_HOME/cmdline-tools/tools/bin:$ANDROID_HOME/platform-tools:$PATH"

RUN mkdir -p ${ANDROID_HOME}/cmdline-tools/tools \
 && mkdir -p ${JAVA_HOME}

# https://stackoverflow.com/questions/65262340/cmdline-tools-could-not-determine-sdk-root
COPY --from=clitools cmdline-tools/ ${ANDROID_HOME}/cmdline-tools/tools
COPY --from=jdk11 jdk-11.0.9.1+1/ ${JAVA_HOME}

# https://stackoverflow.com/questions/38096225/automatically-accept-all-sdk-licences
RUN yes | sdkmanager --licenses


FROM sdkmanager as android-sdk-29
RUN sdkmanager "platform-tools" "platforms;android-29"
RUN sdkmanager "build-tools;29.0.3"

# https://stackoverflow.com/questions/36388465/how-to-set-bash-aliases-for-docker-containers-in-dockerfile
RUN mv ${ANDROID_HOME}/platform-tools/adb ${ANDROID_HOME}/platform-tools/adb.original \
 && echo '#!/bin/bash\nadb.original -H host.docker.internal "$@"' > /bin/adb \
 && chmod +x /bin/adb

CMD ["sleep", "infinity"]