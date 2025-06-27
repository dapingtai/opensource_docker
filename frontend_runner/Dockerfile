FROM ubuntu:latest
LABEL maintainer="Rb"

RUN rm /bin/sh && ln -s /bin/bash /bin/sh
RUN apt update && apt install curl -y

# nvm environment variables
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION="12.13.0 18.16.0 22.14.0"
ENV DEFAULT_NODE_VERSION="18.16.0"
RUN mkdir $NVM_DIR

# install nvm
#RUN apt install curl -y
RUN curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash 

# install node and npm
RUN bash -c "source $NVM_DIR/nvm.sh && \
  for ver in $NODE_VERSION; do \
    nvm install \$ver; \
  done && \
  nvm alias default $DEFAULT_NODE_VERSION"

# add node and npm to path so the commands are available
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

RUN for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do apt-get remove $pkg; done

RUN apt-get update && apt-get install ca-certificates curl
RUN install -m 0755 -d /etc/apt/keyrings
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
RUN chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
RUN echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

# install docker && ssh
RUN apt update && apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin openssh-client -y

