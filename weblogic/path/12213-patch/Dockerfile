# LICENSE UPL 1.0
#
# Copyright (c) 2018 Oracle and/or its affiliates. All rights reserved.
#
# ORACLE DOCKERFILES PROJECT
# --------------------------
# This Dockerfile extends the Oracle WebLogic install image and applies a patch.

# REQUIRED FILES TO BUILD THIS IMAGE
# ----------------------------------
# (1) p27117282_122130_Generic.zip
#     Download the patch from http://support.oracle.com
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run:
#      $ sudo docker build -t oracle/weblogic:12.2.1.3-p .
#

# Pull base image
# ---------------
FROM oracle/weblogic:12.2.1.3-crbc

# Maintainer
# ----------
MAINTAINER Monica Riccelli <monica.riccelli@oracle.com>

# Environment variables required for this build (do NOT change)
# -------------------------------------------------------------
ENV PATCH_PKG="p27117282_122130_Generic.zip"

# Copy supplemental package and scripts
# --------------------------------
COPY --chown=weblogic:weblogic $PATCH_PKG /wls11/bea12c/

# Installation of Supplemental Quick Installer
# --------------------------------------------
USER root
ADD nfs.tar /wls11/bea12c/
RUN     cd /wls11/bea12c && \
	yum install -y nfs-rpm/nfs-utils-1.3.0-0.66.0.1.el7.x86_64.rpm && \
	rm -rf /var/cache/yum && \
        rm -rf nfs.rpm
USER weblogic
RUN cd /wls11/bea12c && $JAVA_HOME/bin/jar xf /wls11/bea12c/$PATCH_PKG && cd - && \
    cd /wls11/bea12c/27117282 && $ORACLE_HOME/OPatch/opatch apply -silent && \
    rm /wls11/bea12c/$PATCH_PKG

WORKDIR ${ORACLE_HOME}


CMD ["/wls11/bea12c/oracle/createAndStartEmptyDomain.sh"]
