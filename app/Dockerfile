FROM jenkins/jenkins:lts

USER root

# Instalar Docker, OpenJDK 17 y Maven
RUN apt-get update && apt-get install -y \
    docker.io \
    openjdk-17-jdk \
    openjdk-17-jre \
    maven && \
    apt-get clean

# Establecer JAVA_HOME
ENV JAVA_HOME /usr/lib/jvm/java-17-openjdk-amd64

USER jenkins
