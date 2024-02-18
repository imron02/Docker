FROM imron02/base-hybrid:1.0.6

# Init git
RUN git config --global user.email "you@example.com" && \
    git config --global user.name "Your Name"

# Install ionic package
RUN npm install -g cordova ionic
RUN ionic start myApp tutorial --no-interactive

WORKDIR myApp
EXPOSE 8100

CMD ["ionic", "serve"]