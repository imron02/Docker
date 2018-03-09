FROM imron02/base-hybrid:1.0.4

# Install ionic package
RUN npm install -g cordova ionic
RUN ionic start myApp tutorial --no-interactive

WORKDIR myApp
EXPOSE 8100

CMD ["ionic", "serve"]