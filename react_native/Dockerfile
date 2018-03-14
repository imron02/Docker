FROM imron02/base-hybrid:1.0.6

# Init git
RUN git config --global user.email "you@example.com" && \
    git config --global user.name "Your Name"

# Install watchman
RUN apt-get install -y python-dev lib32stdc++6 lib32z1 \
	&& git clone https://github.com/facebook/watchman.git \
	&& cd watchman \
	&& git checkout v4.9.0 \
	&& ./autogen.sh \
	&& ./configure \
	&& make \
	&& make install \
	&& cd .. \
	&& rm -rf watchman

# Install react native package
RUN npm install -g react-native-cli \
	&& yarn global add react-native-cli \
	&& react-native init AwesomeProject

WORKDIR AwesomeProject
EXPOSE 8081

# Add tini
ENV TINI_VERSION v0.17.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]