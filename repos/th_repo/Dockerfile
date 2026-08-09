FROM registry.access.redhat.com/ubi9/ubi

RUN dnf -y update && \
    dnf -y install \
        git \
        gcc \
        gcc-c++ \
        make \
        python3 \
        python3-pip \
        vim \
        tar \
        gzip \
        findutils \
        which \
        openssh-clients \
    && dnf clean all

# Install Ansible
RUN python3 -m pip install --no-cache-dir ansible

WORKDIR /workspace

CMD ["bash"]