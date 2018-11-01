
# set Ubuntu
FROM ubuntu:16.04
MAINTAINER LSF Docker Maintainers "opensource.lk"

# set user configurations
ARG USER=wso2carbon
ARG USER_ID=802
ARG USER_GROUP=wso2
ARG USER_GROUP_ID=802
ARG USER_HOME=/home/${USER}
# set dependant files directory
ARG FILES=./product-is/files
# set jdk configurations
#ARG JDK=jdk1.8.0*
ARG JAVA_HOME=${USER_HOME}/java
# set wso2 product configurations
ARG WSO2_SERVER=wso2is
ARG WSO2_SERVER_VERSION=5.7.0
ARG WSO2_SERVER_PACK=${WSO2_SERVER}-${WSO2_SERVER_VERSION}
ARG WSO2_SERVER_HOME=${USER_HOME}/${WSO2_SERVER}-${WSO2_SERVER_VERSION}

# install required packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    curl \
    netcat && \
    rm -rf /var/lib/apt/lists/* && \
    echo '[ ! -z "$TERM" -a -r /etc/motd ] && cat /etc/motd' \
    >> /etc/bash.bashrc \
    ; echo "\
    Welcome to WSO2 Docker resources.\n\
    The Docker container contains the WSO2 product with its latest updates, which are under the End User License Agreement (EULA) 2.0.\n\
    \n\
    Read more about EULA 2.0 (https://wso2.com/licenses/wso2-update/2.0).\n"\
    > /etc/motd

# create a user group and a user
RUN groupadd --system -g ${USER_GROUP_ID} ${USER_GROUP} && \
    useradd --system --create-home --home-dir ${USER_HOME} --no-log-init -g ${USER_GROUP_ID} -u ${USER_ID} ${USER}


# Pull base image.
#FROM oracle-java8

# Install Java.
RUN apt-get update && \

    apt-get upgrade -y && \

    apt-get install -y  software-properties-common && \

    add-apt-repository ppa:webupd8team/java -y && \

    apt-get update && \

    echo oracle-java7-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections && \

    apt-get install -y oracle-java8-installer && \

    apt-get clean


RUN apt-get -y install ldap-utils

# Define working directory.
WORKDIR /data


# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

# Define default command.
CMD ["bash"]    



# copy the wso2 product distributions to user's home directory and copy the mysql connector jar to server distribution
#COPY --chown=wso2carbon:wso2 ${FILES}/${JDK} ${USER_HOME}/java
COPY --chown=wso2carbon:wso2 ${FILES}/${WSO2_SERVER_PACK}/ ${USER_HOME}/${WSO2_SERVER_PACK}/
COPY --chown=wso2carbon:wso2 init.sh ${USER_HOME}/
#COPY --chown=wso2carbon:wso2 ${FILES}/mysql-connector-java-*-bin.jar ${USER_HOME}/${WSO2_SERVER_PACK}/repository/components/lib/
ADD --chown=wso2carbon:wso2 https://repo1.maven.org/maven2/dnsjava/dnsjava/2.1.8/dnsjava-2.1.8.jar ${USER_HOME}/${WSO2_SERVER_PACK}/repository/components/lib/
#ADD --chown=wso2carbon:wso2 https://repo1.maven.org/maven2/org/wso2/carbon/kubernetes/artifacts/kubernetes-membership-scheme/1.0.5/kubernetes-membership-scheme-1.0.5.jar ${USER_HOME}/${WSO2_SERVER_PACK}/repository/components/dropins/
# set temporary location for shared artifacts
#COPY --chown=wso2carbon:wso2 ${FILES}/${WSO2_SERVER_PACK}/repository/deployment/ ${USER_HOME}/wso2-tmp/deployment

# set the user and work directory
USER ${USER_ID}
WORKDIR ${USER_HOME}

# set environment variables
ENV JAVA_HOME=${JAVA_HOME} \
    PATH=$JAVA_HOME/bin:$PATH \
    WSO2_SERVER_HOME=${WSO2_SERVER_HOME} \
    WORKING_DIRECTORY=${USER_HOME}

# Set openLDAP credentials in side wso2 is
COPY ./product-is/ldap_dep/* /${USER_HOME}/${WSO2_SERVER_PACK}/repository/conf/


# Set openLDAP dependancies
COPY ./product-is/ldif/* /

COPY ./product-is/ldap_dep/ldapdep.sh /
#RUN ./ldapdep.sh  // Error : /bin/sh: 1: ./rspamd.sh: Permission denied , bellow line introduced
# RUN chmod -R 755 ./ldapdep.sh

COPY ./wso2carbon.jks /${USER_HOME}/${WSO2_SERVER_PACK}/repository/resources/security/wso2carbon.jks
COPY ./product-is/files/${WSO2_SERVER_PACK}/repository/conf/tomcat/catalina-server.xml /${USER_HOME}/${WSO2_SERVER_PACK}/repository/conf/tomcat/catalina-server.xml
WORKDIR ${USER_HOME}/${WSO2_SERVER_PACK}/repository/resources/security/

RUN echo $(ls ${USER_HOME}) --no-cache

RUN keytool -export -alias wso2carbon -keystore wso2carbon.jks -file publickey.pem

RUN echo $(ls ${USER_HOME}/${WSO2_SERVER_PACK}/repository/resources/) --no-cache


RUN yes | keytool -import -alias wso2 -file publickey.pem -keystore client-truststore.jks -storepass wso2carbon

# WORKDIR ${USER_HOME}/${WSO2_SERVER_PACK}/repository/conf/
# RUN grep -ir wso2carbon.jks

#RUN chmod -R 755 /
#RUN chmod +x /ldapdep.sh
#RUN ./ldapdep.sh    

# expose ports
#EXPOSE 4000 9763 9443

ENTRYPOINT ${WORKING_DIRECTORY}/init.sh

