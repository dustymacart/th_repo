FROM registry.access.redhat.com/ubi9/ubi

ENV ANSIBLE_VAULT_PASSWORD_FILE=/root/.ansible/vault_pass

RUN mkdir -p /root/.ansible && \
    echo 'export ANSIBLE_VAULT_PASSWORD_FILE=/root/.ansible/vault_pass' >> /root/.bashrc

RUN dnf -y update && \
    dnf -y install \
        git \
        gcc \
        gcc-c++ \
        make \
        python3 \
        python3-devel \
        python3-pip \
        vim \
        tar \
        gzip \
        findutils \
        which \
        openssh-clients \
        sshpass \
        krb5-workstation \
        krb5-libs \
        krb5-devel \
    && dnf clean all

RUN python3 -m pip install --upgrade pip setuptools wheel

RUN python3 -m pip install --no-cache-dir \
        ansible \
        pywinrm \
        "pywinrm[kerberos]"

WORKDIR /workspace

CMD ["bash"]