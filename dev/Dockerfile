FROM node:8.9.4

ENV SERVER_CONFIG_FILE /usr/src/app/config.json

# Add Tini
ENV TINI_VERSION v0.16.1
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini
ENTRYPOINT ["/usr/bin/tini", "--"]

RUN mkdir -p /usr/src/app

# Fetch application server release in /tmp
RUN cd /tmp \
 && git clone https://github.com/cabasvert/cabasvert-server.git \
 && cd cabasvert-server \
 && npm install \
 && npm run build \
 && npm prune --production \
 && cp -r ./* /usr/src/app/ \
 && rm -rf /tmp/cabasvert-server

# Fetch application browser client release in /tmp
RUN cd /tmp \
 && git clone https://github.com/cabasvert/cabasvert-client.git \
 && cd cabasvert-client \
 && npm install -g ionic cordova@7.1.0 \
 && npm install \
 && NODE_ENV=production ionic cordova build browser --prod --release \
 && cp -r platforms/browser/www/* /usr/src/app/public/ \
 && npm remove -g ionic cordova \
 && rm -rf /tmp/cabasvert-client

# Install application
WORKDIR /usr/src/app
RUN chown -R node:node /usr/src/app

# Run application
USER node
ENV NODE_ENV production
EXPOSE 8080
CMD node dist/cli --config=${SERVER_CONFIG_FILE} --generate-client-config=${NODE_ENV}
