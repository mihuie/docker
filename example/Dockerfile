FROM demo/centos-nodejs

# Install Bower & Grunt
RUN npm install -g bower grunt-cli

ENV PATH $PATH:/usr/bin/bower
ENV PATH $PATH:/usr/bin/grunt

# Define working directory.
WORKDIR /app

# Define volume mount point.
VOLUME ["/app"]

# Define default command.
ENTRYPOINT ["/bin/bash"]