# Get version of Rocky Linux to build
ARG VERSION
# Get build platform
ARG BUILDPLATFORM=linux/amd64

FROM --platform=$BUILDPLATFORM rockylinux/rockylinux:${VERSION}
LABEL maintainer="Kirill Ziuzin"
ENV container=docker

ENV pip_packages "ansible"

RUN dnf -y update && dnf clean all

# Enable systemd.
RUN dnf -y install systemd && dnf clean all && \
  (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
  rm -f /lib/systemd/system/multi-user.target.wants/*;\
  rm -f /etc/systemd/system/*.wants/*;\
  rm -f /lib/systemd/system/local-fs.target.wants/*; \
  rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
  rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
  rm -f /lib/systemd/system/basic.target.wants/*;\
  rm -f /lib/systemd/system/anaconda.target.wants/*;

# Install pip and other requirements.
RUN dnf makecache \
  && dnf -y install \
    dnf-plugins-core \
    python3-pip \
    sudo \
    which \
    python3-dnf \
  && dnf clean all

# Install Ansible via Pip.
RUN python3 -m pip install --upgrade pip && pip3 install $pip_packages

# Disable requiretty.
RUN sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/'  /etc/sudoers

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
RUN echo -e '[local]\nlocalhost ansible_connection=local' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/usr/lib/systemd/systemd"]