This Dockerfile defines a multi-stage build process for creating a Docker image with Ubuntu 22.04 as the base, installing Oracle JDK 17, Maven, and setting up a Java application. 

PART 01 - Base Image and Environment Setup:
            Uses the official Ubuntu 22.04 image as the base.
            Sets an environment variable to avoid interactive prompts during package installations.

PART 02 - Installing Required Packages:
            Updates the package list and installs essential utilities like wget, curl, ca-certificates, and gnupg.
            Cleans up temporary files to reduce image size.

PART 03 - Installing Oracle JDK 17
            Downloads and installs Oracle JDK 17.
            Sets environment variables for Java.
            Verifies the installation by checking the Java version.

PART 04 - Maven Build Stage:
            Defines a Maven build stage.
            Downloads and installs a specific version of Maven (3.9.6).
            Sets environment variables for Maven and Java.
            Verifies the Maven installation.
            Copies project files and builds the Maven project.

PART 05 - Build Package Stage:
            Sets up the final stage for running the application.
            Copies the built JAR file from the Maven build stage.
            Sets the necessary permissions for the JAR file.
            Exposes the application port (8081).
            Defines the default command to run the application using java -jar.

This multi-stage build process ensures that the final Docker image is as lightweight as possible by only including the necessary runtime dependencies and the built application.