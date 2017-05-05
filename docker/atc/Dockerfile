FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    git \
    xz-utils

# Install Go
RUN curl -o /tmp/go.tar.gz https://storage.googleapis.com/golang/go1.8.1.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf /tmp/go.tar.gz \
    && rm /tmp/go.tar.gz \
    && echo "export GOPATH=${HOME}/go" >> /etc/profile \
    && echo "export PATH=${PATH}:/usr/local/go/bin:${HOME}/go/bin" >> /etc/profile
ENV GOPATH ${HOME}/go
ENV PATH ${PATH}:/usr/local/go/bin:${GOPATH}/bin

# Install Node.js
RUN curl -o /tmp/node.tar.xz https://nodejs.org/dist/v6.10.3/node-v6.10.3-linux-x64.tar.xz \
    && tar -C /usr/local -xf /tmp/node.tar.xz \
    && rm /tmp/node.tar.xz \
    && mv /usr/local/node-v6.10.3-linux-x64 /usr/local/node/ \
    && echo "export PATH=${PATH}:/usr/local/node/bin" >> /etc/profile
ENV PATH="${PATH}:/usr/local/node/bin"

# Install npm dependencies.
RUN npm install -g \
    elm \
    less \
    less-plugin-clean-css \
    uglify-js

RUN git clone --recursive https://github.com/concourse/concourse ${GOPATH}/src/github.com/concourse/concourse
RUN cp -R $GOPATH/src/github.com/concourse/concourse/src/* $GOPATH/src

# Install go-bindata
RUN go get github.com/jteeuwen/go-bindata/...
WORKDIR ${GOPATH}/src/github.com/jteeuwen/go-bindata/
RUN go install

WORKDIR ${GOPATH}/src/github.com/concourse/atc/web
RUN make -B
WORKDIR ${GOPATH}/src/github.com/concourse/atc
CMD ["go", "run", "cmd/atc/main.go", "--postgres-data-source=postgres://atc:atc@db/atc?sslmode=disable", "--no-really-i-dont-want-any-auth"]
