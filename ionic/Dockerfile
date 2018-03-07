FROM ubuntu:16.04

# replace shell with bash so we can source files
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Install basic
RUN apt-get update \
        && apt-get install -y unzip \
        && apt-get install -y --no-install-recommends apt-utils

# Install nodejs
RUN wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

# nvm environment variables
ENV NVM_DIR /root/.nvm
ENV NODE_VERSION 8.9.4

RUN source $NVM_DIR/nvm.sh \
    && nvm install --lts \
    && nvm alias default lts/* \
    && nvm use default

# Install Java
RUN apt-get install -y software-properties-common \
	&& add-apt-repository -y ppa:webupd8team/java \
	&& apt-get update \
	&& echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections \
	&& apt-get install -y oracle-java8-installer

# Install Android SDK
ENV ANDROID_HOME=/opt/android-sdk-linux

RUN mkdir /opt/ndroid-sdk-linux \
	&& echo ANDROID_HOME="${ANDROID_HOME}" >> /etc/environment \
	&& wget --quiet https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip \
	&& unzip sdk-tools-linux-3859397.zip -d /opt/android-sdk-linux \
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
    && rm -rf gradle-"${GRADLE_VERSION}"-bin.zip

# Setup environment
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV JAVA_HOME=/usr/lib/jvm/java-8-oracle/

ENV PATH ${PATH}:$NVM_DIR/versions/node/v$NODE_VERSION/bin:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tool/tools/bin:${ANDROID_HOME}/platform-tools:/opt/gradle/gradle-"$GRADLE_VERSION"/bin

# Install ionic package
RUN npm install -g cordova ionic

# Clear all cache
RUN npm cache clear --force \
	&& apt-get clean \
    && apt-get -y autoclean \
    && rm -rf /tmp/* /var/tmp/*

CMD ["node"]