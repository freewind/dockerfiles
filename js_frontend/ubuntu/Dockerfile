FROM codenvy/ubuntu_jre
ENV NODE_VERSION=0.12.9 \
    NODE_PATH=/usr/local/lib/node_modules
    
RUN sudo apt-get update && \
    sudo apt-get -y install \
      apache2 \
      build-essential \
      gcc \
      libssl-dev \
      libkrb5-dev \
      make \
      python \
      ruby-full \
      rubygems-integration && \
    sudo gem install sass compass && \
    sudo apt-get clean && \
    sudo apt-get -y autoremove && \
    sudo apt-get -y clean && \
    sudo rm -rf /var/lib/apt/lists/* && \
    set -ex \
    && for key in \
      9554F04D7259F04124DE6B476D5A82AC7E37093B \
      94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
      0034A06D9D9B0064CE8ADF6BF1747F4AD2306D93 \
      FD3A5288F042B6850C66B31F09FE44734EB7990E \
      71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
      DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    ; do \
      gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
    done && \
  	cd /home/user && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz" \
  	&& curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  	&& gpg --verify SHASUMS256.txt.asc \
  	&& grep "node-v$NODE_VERSION-linux-x64.tar.gz\$" SHASUMS256.txt.asc | sha256sum -c - \
  	&& sudo tar -xzf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 \
  	&& sudo rm "node-v$NODE_VERSION-linux-x64.tar.gz" SHASUMS256.txt.asc

# configure Apache to change document root and allow all for that path, allow mod rewrite and install composer
RUN sudo sed -i 's/\/var\/www\/html/\/projects/g'  /etc/apache2/sites-available/000-default.conf && \
    sudo sed -i 's/\/var\/www\//\/projects\//g' /etc/apache2/apache2.conf && \
    sudo sed -i 's/None/All/g' /etc/apache2/apache2.conf && \
    echo "ServerName localhost" | sudo tee -a /etc/apache2/apache2.conf && \
    sudo a2enmod rewrite

# label is used in Servers tab to display mapped port for Apache process on 80 port in the container
LABEL che:server:80:ref=apache2 che:server:80:protocol=http

EXPOSE 80 3000 5000 9000

RUN sudo npm install -g npm@latest
RUN sudo npm install --unsafe-perm -g gulp bower grunt grunt-cli yeoman-generator yo generator-angular generator-karma generator-webapp

CMD sudo chown -R www-data:www-data /projects && \
    sudo chmod -R 777 /projects && \
    sudo service apache2 restart && \
    tail -f /dev/null
