# This Dockerfile extends the Oracle WebLogic Server 10.3.6 Image
#
# Util scripts are copied into the image enabling users to plug NodeManager 
# magically into the AdminServer running on another container as a Machine.
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run: 
#      $ docker build -t lucksolutions/weblogic-admin:10.3.6 .
#

# Pull base image
# ---------------
FROM lucksolutions/weblogic:10.3.6	

# Maintainer
# ----------
MAINTAINER Jason Luck <jason.luck@lucksolutons.com>

# WLS Configuration
# -------------------------------
ENV ADMIN_PASSWORD welcome1
ENV ADMIN_PORT 7001
ENV NM_PORT 5556
ENV USER_MEM_ARGS -Xms256m -Xmx512m -XX:MaxPermSize=2048m
ENV WL_HOME /u01/oracle/weblogic
ENV DOMAIN_NAME base_domain

# Add files required to build this image
COPY domain-scripts/* /u01/oracle/

# Root commands
USER root
RUN echo ". $WL_HOME/user_projects/domains/$DOMAIN_NAME/bin/setDomainEnv.sh" >> /root/.bashrc && \
    echo "export PATH=$PATH:$WL_HOME/wlserver/common/bin:$WL_HOME/user_projects/domains/$DOMAIN_NAME/bin" >> /root/.bashrc

# Configuration of WLS Domain
USER oracle
WORKDIR $WL_HOME
RUN $WL_HOME/wlserver/common/bin/wlst.sh -skipWLSModuleScanning /u01/oracle/create-wls-domain.py
RUN mv /u01/oracle/commEnv.sh $WL_HOME/wlserver/common/bin/commEnv.sh

#Setup boot properties so the server can start without providing password at startup
RUN mkdir -p $WL_HOME/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security && \
    echo "username=weblogic" > $WL_HOME/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security/boot.properties && \ 
    echo "password=$ADMIN_PASSWORD" >> $WL_HOME/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security/boot.properties
 
#Setup WLS environment variable and path modifications to be set when the shell is started 
RUN echo ". $WL_HOME/user_projects/domains/$DOMAIN_NAME/bin/setDomainEnv.sh" >> /u01/oracle/.bashrc && \ 
    echo "export PATH=$PATH:$WL_HOME/wlserver/common/bin:$WL_HOME/user_projects/domains/$DOMAIN_NAME/bin" >> /u01/oracle/.bashrc && \
    rm /u01/oracle/create-wls-domain.py

# Expose Node Manager default port, and also default http/https ports for admin console
EXPOSE $NM_PORT $ADMIN_PORT

# Final setup
WORKDIR /u01/oracle

ENV PATH $PATH:$WL_HOME/wlserver/common/bin:$WL_HOME/user_projects/domains/$DOMAIN_NAME/bin:/u01/oracle

# Define default command to start bash. 
CMD ["startWebLogic.sh"]