FROM scratch
ADD bclinux-8.6-docker_x86_64.tar.xz /

LABEL org.label-schema.schema-version="1.0" \
    org.label-schema.name="BCLinux Base Image" \
    org.label-schema.vendor="BCLinux" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20230505"

ARG USER_UID=501
ARG USER_GID=20

RUN yum clean all \
    && yum makecache \
    && yum -y update \ 
    && yum -y groupinstall 'Development Tools' \
    && yum -y install openssh-server \ 
        clang \
        rsync \
        sudo \
        python2 \
        vim \
        cmake \
        passwd \
        openssl-devel \
        libcurl-devel \
        zlib-devel \ 
    && yum clean all

RUN ssh-keygen -A

RUN ( \
    echo 'LogLevel DEBUG2'; \
    echo 'PermitRootLogin yes'; \
    echo 'PasswordAuthentication yes'; \
    echo 'Subsystem sftp /usr/libexec/openssh/sftp-server'; \
  ) > /etc/ssh/sshd_config_local

RUN useradd -d /home/fduser -m  -g $USER_GID -u $USER_UID -s /bin/bash fduser \
    && yes password | passwd fduser \
    && echo "fduser ALL=(ALL)	NOPASSWD: ALL" > /etc/sudoers.d/fduser

COPY --chown=root:root --chmod=755 ./dumb-init_1.2.5_x86_64 /sbin/dumb-init

ENTRYPOINT ["/sbin/dumb-init", "--"]
CMD ["/usr/sbin/sshd", "-D", "-e", "-f", "/etc/ssh/sshd_config_local"]
# CMD ["/bin/bash"]
