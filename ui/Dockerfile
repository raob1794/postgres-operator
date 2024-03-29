ARG BASE_IMAGE=registry.opensource.zalan.do/library/alpine-3.15:latest
ARG NODE_IMAGE=node:14.21.2-alpine

FROM $NODE_IMAGE AS build

COPY . /workdir
WORKDIR /workdir/app

RUN npm install &&\
    npm run build

FROM $BASE_IMAGE

LABEL maintainer="Team ACID @ Zalando <team-acid@zalando.de>"

EXPOSE 8081

RUN \
  apk add --no-cache \
    alpine-sdk \
    autoconf \
    automake \
    ca-certificates \
    libffi-dev \
    libtool \
    python3 \
    python3-dev \
    zlib-dev \
    && \
  python3 -m ensurepip && \
  rm -r /usr/lib/python*/ensurepip && \
  pip3 install --upgrade \
    gevent \
    jq \
    pip \
    setuptools \
    && \
  rm -rf \
    /root/.cache \
    /tmp/* \
    /var/cache/apk/*

COPY requirements.txt /
COPY start_server.sh /
RUN pip3 install -r /requirements.txt

COPY operator_ui /operator_ui
COPY --from=build /workdir/operator_ui/static/build /operator_ui/static/build
ARG VERSION=dev
RUN sed -i "s/__version__ = .*/__version__ = '${VERSION}'/" /operator_ui/__init__.py

WORKDIR /
CMD ["/usr/bin/python3", "-m", "operator_ui"]
