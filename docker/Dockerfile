FROM heroku/heroku:24

# Ensure we're running as root
USER root

# Clear apt cache, create missing directories, and install necessary dependencies
RUN rm -rf /var/lib/apt/lists/* && \
    mkdir -p /var/lib/apt/lists/partial && \
    apt-get update && \
    apt-get install -y wget tar git

# Install OpenJDK 17 (needed if required by other tasks, not used for nativeImage)
RUN apt-get install -y gcc zlib1g-dev openjdk-8-jdk 

# Download and install GraalVM (JVM 21.0.4+8.1) for native-image
RUN wget https://download.oracle.com/graalvm/21/latest/graalvm-jdk-21_linux-x64_bin.tar.gz -O /tmp/graalvm-jdk-21_linux-x64_bin.tar.gz \
    && tar -xzf /tmp/graalvm-jdk-21_linux-x64_bin.tar.gz -C /opt \
    && rm /tmp/graalvm-jdk-21_linux-x64_bin.tar.gz

# Install Gradle 7.6
RUN wget https://services.gradle.org/distributions/gradle-7.6-bin.zip -P /tmp \
    && unzip /tmp/gradle-7.6-bin.zip -d /opt/gradle \
    && rm /tmp/gradle-7.6-bin.zip
ENV GRADLE_HOME=/opt/gradle/gradle-7.6
ENV PATH=$GRADLE_HOME/bin:$PATH

# Clone the pdftk-java repository
RUN git clone https://gitlab.com/pdftk-java/pdftk.git /opt/pdftk

# Set the working directory to pdftk
WORKDIR /opt/pdftk

# First part of the build: Use OpenJDK 8 for the Gradle build
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ENV PATH="$JAVA_HOME/bin:$PATH"

# Run Gradle tasks that require JDK 8
RUN gradle build

# Second part of the build: Switch to GraalVM for native-image generation
ENV JAVA_HOME=/opt/graalvm-jdk-21.0.4+8.1
ENV PATH="$JAVA_HOME/bin:$PATH"

# Build native image using GraalVM's native-image
RUN gradle nativeImage

# Copy the resulting binary to the mounted directory
CMD ["cp", "build/native-image/pdftk", "/app"]
