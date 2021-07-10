FROM ubuntu:20.10 AS base

# adaption from https://github.com/newsboat/newsboat/blob/master/docker/ubuntu_20.04-build-tools.dockerfile

ARG NEWSBOAT_VERSION=r2.24

ARG cc=gcc-10
ARG cxx=g++-10
ARG cxx_package=g++-10
ARG rust_version=1.53.0

ENV CC=$cc
ENV CXX=$cxx
ENV PATH /root/.cargo/bin:$PATH
ENV DEBIAN_FRONTEND noninteractive

LABEL newsboat_version=$NEWSBOAT_VERSION

RUN apt-get update && apt-get upgrade -y \
    && apt-get install --assume-yes --no-install-recommends \
        build-essential $cxx_package libsqlite3-dev libcurl4-openssl-dev libssl-dev \
        libxml2-dev libstfl-dev libjson-c-dev libncursesw5-dev gettext git \
        pkg-config zlib1g-dev asciidoctor wget \
	&& wget -O ./rustup.sh --secure-protocol=TLSv1_2 https://sh.rustup.rs \
    && chmod +x ./rustup.sh \
    && ./rustup.sh -y \
        --default-host x86_64-unknown-linux-gnu \
        --default-toolchain $rust_version \
	&& git clone https://github.com/newsboat/newsboat.git \
	&& cd /newsboat && git checkout $NEWSBOAT_VERSION \
	&& make -j9

FROM ubuntu:20.10

COPY --from=base /newsboat/newsboat /usr/local/bin/

# dependencies to run newsboat and scripts
RUN apt-get update && apt-get install -y \
	curl jq python3 netbase libxml2 python3-pip \
	sqlite3 libstfl0 libjson-c-dev wget \
	&& pip install --no-cache-dir requests beautifulsoup4 \
	&& apt-get install locales \
    && echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen \
    && locale-gen

ENV HOME /home/user
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

RUN groupadd --gid 1000 user \
	&& useradd --uid 1000 --gid 1000 --home $HOME --create-home user \
	&& chown -R user:user $HOME

WORKDIR $HOME
USER user

ENTRYPOINT ["/usr/local/bin/newsboat"]
