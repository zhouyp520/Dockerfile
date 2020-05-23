#Copyright (c) 2014-2019 Oracle and/or its affiliates. All rights reserved.
#
#Licensed under the Universal Permissive License v 1.0 as shown at https://oss.oracle.com/licenses/upl.
#
# ORACLE DOCKERFILES PROJECT
# --------------------------
# This is the Dockerfile for WebLogic 12.2.1.3 Quick Install Distro
#
# REQUIRED FILES TO BUILD THIS IMAGE
# ----------------------------------
# (1) fmw_12.2.1.3.0_wls_quick_Disk1_1of1.zip
#     Download the Developer Quick installer from http://www.oracle.com/technetwork/middleware/weblogic/downloads/wls-for-dev-1703574.html
#
# (2) server-jre-8u251-linux-x64.tar.gz
#     Download from http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run:
#      $ docker build -t oracle/weblogic:12.2.1.3-crbc .
#
# IMPORTANT
# ---------
# The resulting image of this Dockerfile contains a WLS Empty Domain.
#
# Extend base JRE image
# You must build the image by using the Dockerfile in GitHub project `../../../OracleJava/java8`                                                                          
# ----------------------------------------------------------------------------------------------
FROM oracle/serverjre:8 as builder

# Maintainer
# ----------
MAINTAINER Monica Riccelli <monica.riccelli@oracle.com>

# Common environment variables required for this build (do NOT change)
# --------------------------------------------------------------------
ENV ORACLE_HOME=/wls11/bea12c/oracle \
    USER_MEM_ARGS="-Djava.security.egd=file:/dev/./urandom" \
    PATH=$PATH:${JAVA_HOME}/bin:/wls11/bea12c/oracle/oracle_common/common/bin:/wls11/bea12c/oracle/wlserver/common/bin

# Setup filesystem and oracle user
# Adjust file permissions, go to /wls11/bea12c as user 'oracle' to proceed with WLS installation
# ------------------------------------------------------------
RUN mkdir -p /wls11/bea12c && \
    chmod a+xr /wls11/bea12c && \
    groupadd -g 2001 bea && \
    useradd -b /wls11/bea12c -d /wls11/bea12c/oracle -m -s /bin/bash -u 2001 -g bea weblogic

# Environment variables required for this build (do NOT change)
# -------------------------------------------------------------
ENV FMW_PKG=fmw_12.2.1.3.0_wls_quick_Disk1_1of1.zip \
    FMW_JAR=fmw_12.2.1.3.0_wls_quick.jar

# Copy packages
# -------------
COPY $FMW_PKG install.file oraInst.loc /wls11/bea12c/
RUN chown 2001:2001 -R /wls11/bea12c

# Install
# ------------------------------------------------------------
USER weblogic
RUN cd /wls11/bea12c && ${JAVA_HOME}/bin/jar xf /wls11/bea12c/$FMW_PKG && cd - && \
    ${JAVA_HOME}/bin/java -jar /wls11/bea12c/$FMW_JAR -silent -responseFile /wls11/bea12c/install.file -invPtrLoc /wls11/bea12c/oraInst.loc -jreLoc $JAVA_HOME -ignoreSysPrereqs -force -novalidation ORACLE_HOME=$ORACLE_HOME && \
    rm /wls11/bea12c/$FMW_JAR /wls11/bea12c/$FMW_PKG /wls11/bea12c/install.file && \
    rm -rf /wls11/bea12c/oracle/cfgtoollogs

# Final image stage
FROM oracle/serverjre:8

ENV ORACLE_HOME=/wls11/bea12c/oracle \
    USER_MEM_ARGS="-Djava.security.egd=file:/dev/./urandom" \
    SCRIPT_FILE=/wls11/bea12c/oracle/createAndStartEmptyDomain.sh \
    PATH=$PATH:${JAVA_HOME}/bin:/wls11/bea12c/oracle/oracle_common/common/bin:/wls11/bea12c/oracle/wlserver/common/bin

# Domain and Server environment variables
# ------------------------------------------------------------
ENV DOMAIN_NAME="${DOMAIN_NAME:-base_domain}" \
    ADMIN_LISTEN_PORT="${ADMIN_LISTEN_PORT:-7001}"  \
    ADMIN_NAME="${ADMIN_NAME:-AdminServer}" \
    DEBUG_FLAG=true \
    PRODUCTION_MODE=dev \
    ADMINISTRATION_PORT_ENABLED="${ADMINISTRATION_PORT_ENABLED:-true}" \
    ADMINISTRATION_PORT="${ADMINISTRATION_PORT:-9002}"

# Setup filesystem and oracle user
# Adjust file permissions, go to /wls11/bea12c as user 'oracle' to proceed with WLS installation
# ------------------------------------------------------------
RUN mkdir -p /wls11/bea12c && \
    chmod a+xr /wls11/bea12c && \
    groupadd -g 2001 bea && \
    useradd -b /wls11/bea12c -d /wls11/bea12c/oracle -m -s /bin/bash -u 2001 weblogic && \
    chown 2001:2001 /wls11/bea12c

COPY --from=builder --chown=2001:2001 /wls11/bea12c /wls11/bea12c

# Copy scripts
#-------------
COPY container-scripts/createAndStartEmptyDomain.sh container-scripts/create-wls-domain.py /wls11/bea12c/oracle/

RUN chmod +xr $SCRIPT_FILE && \
    chown 2001:2001 $SCRIPT_FILE /wls11/bea12c/oracle/create-wls-domain.py

USER weblogic

WORKDIR ${ORACLE_HOME}

# Define default command to start script.
CMD ["/wls11/bea12c/oracle/createAndStartEmptyDomain.sh"]
