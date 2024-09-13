# Use the official Node.js LTS image with Debian Bookworm as a parent image
FROM node:20-bookworm

# Set the working directory
WORKDIR /workspaces/react_tut

# Install global packages
RUN npm install -g npm@latest vite@4.1.0 && \
    npm cache clean --force

# Install any OS updates and required packages
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y git sudo && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Ensure the 'node' user exists with the correct UID and GID
ARG USERNAME=node
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN if [ ! $(getent group $USER_GID) ]; then \
        groupmod -g $USER_GID $USERNAME || groupadd -g $USER_GID $USERNAME; \
    fi && \
    if [ ! $(getent passwd $USER_UID) ]; then \
        useradd -s /bin/bash -u $USER_UID -g $USER_GID -m $USERNAME; \
    else \
        usermod -u $USER_UID $USERNAME; \
    fi && \
    echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME

# Switch to non-root user
USER $USERNAME

# Set up npm to use a custom directory for global packages
RUN mkdir -p /home/$USERNAME/.npm-global && \
    npm config set prefix '/home/$USERNAME/.npm-global' && \
    echo "export PATH=/home/$USERNAME/.npm-global/bin:$PATH" >> /home/$USERNAME/.bashrc

# Set the PATH to include the custom npm global bin directory
ENV PATH=/home/$USERNAME/.npm-global/bin:$PATH

# Set the default shell to bash
SHELL ["/bin/bash", "-c"]