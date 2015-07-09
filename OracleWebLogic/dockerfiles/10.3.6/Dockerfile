# --------------------------
# This is the Dockerfile for WebLogic 10.3.6 ZIP Distro
# 
# IMPORTANT
# ---------
# The resulting image of this Dockerfile DOES NOT contain a WLS Domain.
# For that, look into the folder 'samples' for an example on how
# to create a domain on a new inherited image.
#
# You can go into 'samples/12.1.3-generic-domain' after building the generic raw image
# and build that image, for example:
# 
#   $ cd samples/12c-generic-domain
#   $ sudo docker build -t mywls .
#
# REQUIRED FILES TO BUILD THIS IMAGE
# ----------------------------------
# (1) wls1036_dev.zip 
#     Download the Developer ZIP Update1 installer from http://www.oracle.com/technetwork/middleware/weblogic/downloads/wls-main-097127.html
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run: 
#      $ sudo docker build -t lucksolutions/weblogic:10.3.6 . 
#
# To start the image and connect to a shell
# Run:
#	   $ docker run --rm=true -i -t lucksolutions/weblogic:10.3.6 bash
#
# Pull base image
# ---------------
FROM centos:latest

# Maintainer
# ----------
MAINTAINER Jason Luck <jason.luck@lucksolutions.com>

# Environment variables required for this build (do NOT change)
# -------------------------------------------------------------
ENV WLS_PKG wls1036_dev.zip
ENV JAVA_HOME /usr/lib/jvm/jre-1.6.0-openjdk.x86_64/
ENV MW_HOME /u01/oracle/wls1036
ENV CONFIG_JVM_ARGS -Djava.security.egd=file:/dev/./urandom

# SETUP FILESYSTEM AND ORACLE USER
# ------------------------------------------------------------
RUN mkdir /u01 && chmod a+xr /u01 && \
    useradd -b /u01 -m -s /bin/bash oracle

# Install and configure Oracle JDK 6
# -------------------------------------
RUN yum -y install java-1.6.0-openjdk-devel

# Adjust file permissions, go to /u01 as user 'oracle' to proceed with WLS installation
RUN chown oracle:oracle -R /u01

# Installation of Weblogic 
USER oracle

# Copy packages
COPY $WLS_PKG /u01/

RUN mkdir $MW_HOME
WORKDIR $MW_HOME
RUN jar xf /u01/$WLS_PKG && \
    ln -s $MW_HOME /u01/oracle/weblogic 

RUN chmod a+x configure.sh
RUN	sh configure.sh -silent && \
    find $MW_HOME -name "*.sh" -exec chmod a+x {} \; && \
    rm /u01/$WLS_PKG

WORKDIR /u01/oracle

ENV PATH $PATH:/u01/oracle/weblogic/oracle_common/common/bin

# Define default command to start bash. 
CMD ["bash"]
