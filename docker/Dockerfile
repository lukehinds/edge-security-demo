##############################################################################
# Dockerfile for Edge / AI Security Demo
##############################################################################

FROM fedora:latest
MAINTAINER Luke Hinds <lhinds@redhat.com>
LABEL version="0.1" description="Dockerfile for Edge / AI Security Demo"

# environment variables
ARG BRANCH=master
ENV container docker
ENV HOME /root
ENV KEYLIME_HOME ${HOME}/keylime
ENV TPM_HOME ${HOME}/swtpm2

# Packaged dependencies
ENV PKGS_DEPS="automake \
dbus-devel \
dnf-plugins-core \
gcc \
git \
glib2-devel \
glib2-static \
gnulib \
libselinux-python3 \
libtool \
make \
openssl-devel \
procps \
python3-cryptography \
python3-dbus \
python3-devel \
python3-m2crypto \
python3-pip \
python3-setuptools \
python3-sqlalchemy \
python3-simplejson \
python3-tornado \
python3-virtualenv \
python3-yaml \
python3-zmq \
redhat-rpm-config \
tpm2-abrmd \
tpm2-tools \
tpm2-tss \
uthash-devel \
wget \
keylime \
which"

RUN dnf -y update -v 

RUN dnf makecache && \
  dnf -y install $PKGS_DEPS && \
  dnf clean all && \
  rm -rf /var/cache/dnf/*

RUN cd "/lib/systemd/system/sysinit.target.wants/"; \
    for i in *; do [ $i = systemd-tmpfiles-setup.service ] || rm -f "$i"; done

RUN rm -f /lib/systemd/system/multi-user.target.wants/* \
    /etc/systemd/system/*.wants/* \
    /lib/systemd/system/local-fs.target.wants/* \
    /lib/systemd/system/sockets.target.wants/*udev* \
    /lib/systemd/system/sockets.target.wants/*initctl* \
    /lib/systemd/system/basic.target.wants/* \
    /lib/systemd/system/anaconda.target.wants/*

RUN systemctl set-default multi-user.target
ENV init /lib/systemd/systemd

# Build and install TPM 2.0 simulator
WORKDIR ${TPM_HOME}
RUN wget --content-disposition http://sourceforge.net/projects/ibmswtpm2/files/ibmtpm1119.tar.gz/download && \
  tar -zxvf ibmtpm1119.tar.gz
WORKDIR ${TPM_HOME}/src
RUN make && \
  install -c tpm_server /usr/local/bin/tpm_server

VOLUME [ "/sys/fs/cgroup" ]

RUN cd && git clone https://github.com/keylime/keylime

ENTRYPOINT ["/lib/systemd/systemd"]

