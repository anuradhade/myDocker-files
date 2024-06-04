# Use the official Ubuntu 22.04 base image
FROM ubuntu:22.04 as base

# Set environment variables to prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Update the package list and install required packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    curl \
    ca-certificates \
    gnupg && \
    # Clean up
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Oracle JDK 17
RUN mkdir -p /usr/java && \
    wget -O /tmp/jdk-17_linux-x64_bin.deb https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.deb && \
    apt-get update && \
    apt-get install -y /tmp/jdk-17_linux-x64_bin.deb && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set environment variables for Java
ENV JAVA_HOME=/usr/lib/jvm/jdk-17-oracle-x64
ENV PATH="$JAVA_HOME/bin:$PATH"

RUN export JAVA_HOME=/usr/lib/jvm/jdk-17-oracle-x64

# Verify the installation
RUN java -version

# Default command to run
CMD ["bash"]

#
#----------------------------------- MAVEN BUILD STAGE------------------------------------------------------------------------
#
FROM base as maven3-build

# Define the version of Maven to install
ENV MAVEN_VERSION=3.9.6

# Set working directory
WORKDIR /opt

# Download and install Maven
RUN wget https://dlcdn.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz  && \
    tar -xzf apache-maven-${MAVEN_VERSION}-bin.tar.gz

RUN chmod -R 777 apache-maven-${MAVEN_VERSION}

# Set environment variables for Maven and Java
ENV MAVEN_HOME=/opt/apache-maven-${MAVEN_VERSION}
ENV PATH="$MAVEN_HOME/bin:$JAVA_HOME/bin:$PATH"

RUN export M2_HOME=/opt/apache-maven-${MAVEN_VERSION}
RUN export MAVEN_HOME=/opt/apache-maven-${MAVEN_VERSION} 

# Source the profile script and verify the Maven installation
RUN mvn -version

# Verify the Maven installation
RUN echo $JAVA_HOME && echo $MAVEN_HOME && mvn -version

# Set working directory
WORKDIR /app/account-package

# Copy the specific project files and directories to the working directory
COPY account-package-common /app/account-package/account-package-common
COPY account-package-core /app/account-package/account-package-core
COPY account-package-web /app/account-package/account-package-web
COPY pom.xml /app/account-package/pom.xml
 

# Build the Maven project
RUN mvn clean install

# Default command
CMD ["/bin/bash"]
#
#----------------------------------- BUILD PACKAGE STAGE -----------------------------------
FROM base as dev

# Set working directory
WORKDIR /app/acctpkg-backend

# Copy the built jar file from the maven build stage
COPY --from=maven3-build /app/account/account-web/target/account-web-1.0.0.jar /app/acctpkg-backend/account-web-1.0.0.jar

# Set permissions
RUN chmod -R 777 /app/acctpkg-backend/account-web-1.0.0.jar

# Expose the application port
EXPOSE 8081  

# Command to run the application
CMD ["java", "-jar", "/app/acctpkg-backend/account-web-1.0.0.jar"]