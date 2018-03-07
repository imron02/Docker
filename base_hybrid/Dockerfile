FROM node:8.9.4-alpine

# Install basic
RUN apk add --no-cache openssl ca-certificates unzip bash wget

# Install Java
ENV JAVA_VERSION=8 \
    JAVA_UPDATE=162 \
    JAVA_BUILD=12 \
    JAVA_PATH=0da788060d494f5095bf8624735fa2f1 \
    JAVA_HOME="/usr/lib/java"

RUN wget --no-cookies --quiet --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
        "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/${JAVA_PATH}/jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" \
        && wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub \
        && wget -q https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.27-r0/glibc-2.27-r0.apk \
        && apk add glibc-2.27-r0.apk \
        && mkdir "${JAVA_HOME}" \
        && tar -xzvf jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz --strip-components=1 -C /usr/lib/java \
        && rm jdk-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz glibc-2.27-r0.apk

# Install Android SDK
ENV ANDROID_HOME=/usr/lib/android-sdk-linux

RUN mkdir "${ANDROID_HOME}" \
	&& echo ANDROID_HOME="${ANDROID_HOME}" >> /etc/environment \
	&& wget --quiet https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip \
	&& unzip sdk-tools-linux-3859397.zip -d "${ANDROID_HOME}" \
	&& rm sdk-tools-linux-3859397.zip

RUN yes | "${ANDROID_HOME}"/tools/bin/sdkmanager --licenses \
	&& "${ANDROID_HOME}"/tools/bin/sdkmanager "platform-tools" \
	&& "${ANDROID_HOME}"/tools/bin/sdkmanager "build-tools;27.0.3" \
	&& "${ANDROID_HOME}"/tools/bin/sdkmanager "extras;android;m2repository" \
	&& "${ANDROID_HOME}"/tools/bin/sdkmanager "extras;google;m2repository" \
	&& "${ANDROID_HOME}"/tools/bin/sdkmanager "platforms;android-27"

# Install Gradle
ENV GRADLE_VERSION=4.6

RUN wget --quiet https://services.gradle.org/distributions/gradle-"${GRADLE_VERSION}"-bin.zip \
    && mkdir /opt/gradle \
    && unzip -d /opt/gradle gradle-"${GRADLE_VERSION}"-bin.zip \
    && rm gradle-"${GRADLE_VERSION}"-bin.zip

# Setup environment
ENV PATH ${PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tool/tools/bin:${ANDROID_HOME}/platform-tools:/opt/gradle/gradle-"$GRADLE_VERSION"/bin

# Clear all cache & Delete unused package
RUN rm -rf /tmp/* /var/cache/apk/*