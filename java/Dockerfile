FROM centos
MAINTAINER Michael Huie, huie.michael@gmail.com

ENV JAVA_VERSION 8u111
ENV BUILD_VERSION b14

# Install wget
RUN yum -y install wget

# Downloading Java
RUN wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" -O /tmp/jdk-8-linux-x64.rpm

RUN yum -y install /tmp/jdk-8-linux-x64.rpm 

RUN alternatives --install /usr/bin/java jar /usr/java/latest/bin/java 200000
RUN alternatives --install /usr/bin/javaws javaws /usr/java/latest/bin/javaws 200000
RUN alternatives --install /usr/bin/javac javac /usr/java/latest/bin/javac 200000

RUN rm -f /tmp/jdk-8-linux-x64.rpm && yum clean all
ENV JAVA_HOME /usr/java/latest
ENV PATH $PATH:$JAVA_HOME/bin

ENTRYPOINT ["/bin/bash", "-c", "java -version"]