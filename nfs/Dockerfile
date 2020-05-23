# Run:
#      $ docker build -f Dockerfile_java -t oracle/serverjre:8_nfs .
#
FROM oracle/serverjre:8
USER root
RUN yum install -y \
    nfs-utils \
    rpcbind && \
    rm -rf /var/cache/yum