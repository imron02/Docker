FROM imron02/base-hybrid:1.0.4-alpine

# Init git, change user email with your email
RUN git config --global user.email "imron.r@meteor.id" && \
    git config --global user.name "Imron Rosdiana"

# Install watchman
RUN apk add --no-cache watchman

# Install react native package
RUN npm install -g react-native-cli npm@6.0.1 exp \
	&& yarn global add react-native-cli \
	&& react-native init AwesomeProject

# Clear all cache & Delete unused package
RUN rm -rf /tmp/* /var/cache/*

WORKDIR AwesomeProject
EXPOSE 8081

# Add tini
ENV TINI_VERSION v0.17.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]