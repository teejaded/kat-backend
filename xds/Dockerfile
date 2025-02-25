FROM golang:1.11

ENV PROTOC_URL=https://github.com/protocolbuffers/protobuf/releases/download
ENV PROTOC_VERSION=3.5.1

ENV ENVOY_REPO=https://github.com/datawire/envoy.git
ENV ENVOY_REMOTE=origin/buffered-auth

WORKDIR $GOPATH/src/github.com/datawire/kat-backend/xds

COPY xds/glide.yaml glide.yaml
COPY xds/entrypoint.sh entrypoint.sh

RUN apt-get update && apt-get install make git openssl curl unzip -y
RUN curl https://glide.sh/get | sh
RUN mkdir -p bin tmp
RUN cd tmp && curl -OL $PROTOC_URL/v$PROTOC_VERSION/protoc-$PROTOC_VERSION-linux-x86_64.zip && \
    unzip protoc-$PROTOC_VERSION-linux-x86_64.zip
RUN mv tmp/bin/protoc bin/protoc && chmod +x bin/protoc && rm -rf tmp
RUN glide install
RUN go build -o bin/validate vendor/github.com/lyft/protoc-gen-validate/main.go
RUN go build -o bin/gogofast vendor/github.com/gogo/protobuf/protoc-gen-gogofast/main.go
RUN git clone ${ENVOY_REPO} && cd envoy/ && \
    git reset --hard ${ENVOY_REMOTE}
RUN rm -rf proto/ && mkdir proto && \
    mv envoy/api/envoy proto/ && \
    rm -rf envoy && rm -rf proto/envoy/service/trace/v2

ENTRYPOINT ["bash", "-c", "./entrypoint.sh"]
