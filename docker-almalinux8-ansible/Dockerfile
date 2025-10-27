FROM almalinux:8
LABEL maintainer="Daniel Koop"
ENV container=docker

# Ansible version can be set at build time
# Examples: "ansible" for latest, "ansible-core~=2.16.0" for core 2.16
ARG ANSIBLE_VERSION="ansible"
ENV pip_packages="${ANSIBLE_VERSION}"

# Install systemd -- See https://hub.docker.com/_/centos/
RUN rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

# Install requirements.
RUN dnf -y install rpm dnf-plugins-core \
 && dnf -y update \
 && dnf -y config-manager --set-enabled powertools \
 && dnf -y install \
      epel-release \
      initscripts \
      sudo \
      which \
      hostname \
      libyaml-devel \
      python3 \
      python3-pip \
      python3-pyyaml \
      iproute \
 && dnf clean all

# Install Python 3.11 for newer Ansible versions (2.16+ requires Python 3.10+)
RUN dnf -y install python3.11 python3.11-pip \
 && dnf clean all

# Create symlinks to use Python 3.11 by default for newer Ansible versions
RUN alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 1 && \
    alternatives --set python3 /usr/bin/python3.11

# Upgrade pip to latest version.
RUN python3.11 -m pip install --upgrade pip

# Install Ansible via Pip using Python 3.11
RUN python3.11 -m pip install "$pip_packages"

# Disable requiretty.
RUN sed -i -e 's/^\(Defaults\s*requiretty\)/#--- \1/'  /etc/sudoers

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
RUN echo -e '[local]\nlocalhost ansible_connection=local' > /etc/ansible/hosts

VOLUME ["/sys/fs/cgroup"]
CMD ["/usr/lib/systemd/systemd"]
