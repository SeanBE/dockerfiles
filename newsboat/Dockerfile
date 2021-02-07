FROM ubuntu:20.10

# adaption from https://github.com/newsboat/newsboat/blob/master/docker/ubuntu_20.04-build-tools.dockerfile

ARG NEWSBOAT_VERSION=r2.22

ARG cc=gcc-10
ARG cxx=g++-10
ARG cxx_package=g++-10
ARG rust_version=1.48.0

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
    && apt-get autoremove \
    && apt-get clean \
	&& apt-get install locales \
    && echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen \
    && locale-gen \
	&& wget -O ./rustup.sh --secure-protocol=TLSv1_2 https://sh.rustup.rs \
    && chmod +x ./rustup.sh \
    && ./rustup.sh -y \
        --default-host x86_64-unknown-linux-gnu \
        --default-toolchain $rust_version \
	&& git clone https://github.com/newsboat/newsboat.git && cd /newsboat && git checkout $NEWSBOAT_VERSION \
	&& make -j9 \
	&& mv /newsboat/newsboat /usr/local/bin \
	&& rm -rf /root/.rustup \
	&& rm -rf /root/.cargo \
	&& rm -rf /newsboat \
	&& apt-get remove -y $cxx_package build-essential asciidoctor pkg-config git \
	&& apt-get autoremove -y && apt-get clean

RUN apt-get install -y curl jq python3

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

ENV HOME /home/user
RUN groupadd --gid 1000 user \
	&& useradd --uid 1000 --gid 1000 --home $HOME --create-home user \
	&& chown -R user:user $HOME

WORKDIR $HOME
USER user
ENTRYPOINT ["/usr/local/bin/newsboat"]
