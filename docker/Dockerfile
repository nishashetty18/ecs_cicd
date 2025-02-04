# FROM jenkins/jenkins:lts

# USER root

# RUN apt-get update && apt-get install sudo -y  
 
# # Install AWS CLI
# RUN apt-get update && apt-get install -y awscli

# # Install Docker CLI
# RUN apt-get install -y docker.io

# RUN systemctl Start docker

# RUN systemctl Start jenkins

# # Ensure Jenkins user can run Docker
# RUN usermod -aG docker jenkins




# USER jenkins
# Use the official Jenkins LTS image as a base
FROM jenkins/jenkins:lts

# Switch to root user to install dependencies
USER root

# Install AWS CLI and Docker CLI
RUN apt-get update && apt-get install -y \
    awscli \
    docker.io \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# Add Jenkins user to the Docker group so it can run Docker commands
RUN usermod -aG docker jenkins

# Switch back to the Jenkins user
USER jenkins

# Expose the default Jenkins port
EXPOSE 8080
