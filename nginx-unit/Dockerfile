FROM unit:1.33.0-python3.12

# Add non-root user
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupadd -o -g ${PGID} developer && \
    useradd -m -o -u ${PUID} -g developer developer && \
    usermod -aG sudo,shadow developer

# Install Node version manager
RUN mkdir /usr/local/nvm
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 20.15.1
ENV NVM_INSTALL_PATH $NVM_DIR/versions/node/v$NODE_VERSION
RUN rm /bin/sh && ln -s /bin/bash /bin/sh
RUN curl --silent -o- https://raw.githubusercontent.com/creationix/nvm/v0.39.1/install.sh | bash
RUN . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default
ENV NODE_PATH $NVM_INSTALL_PATH/lib/node_modules
ENV PATH $NVM_INSTALL_PATH/bin:$PATH
RUN npm -v
RUN node -v
RUN source ~/.bashrc && nvm --version
RUN echo 'export NVM_DIR="/usr/local/nvm"' >> /etc/bash.bashrc
RUN echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm' >> /etc/bash.bashrc
RUN echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion' >> /etc/bash.bashrc

# Yarn install
RUN npm install -g yarn

# Change default work directory
WORKDIR /var/www
COPY config.json /var/run/control.unit.sock
RUN chmod 777 /var/run/control.unit.sock
CMD ["unitd", "--no-daemon", "--control", "unix:/var/run/control.unit.sock"]
