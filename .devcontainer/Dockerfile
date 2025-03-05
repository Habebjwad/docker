# Use an official Ubuntu base image
FROM ubuntu:22.04

# Prevent interactive prompts during package install
ENV DEBIAN_FRONTEND=noninteractive

# Update and install required packages
RUN apt-get update && apt-get install -y \
    xfce4 \
    xfce4-goodies \
    xorg \
    xrdp \
    wget \
    curl \
    gnupg2 \
    software-properties-common \
    sudo \
    locales \
    && rm -rf /var/lib/apt/lists/*

# Configure locale (if needed)
RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# Create a new user (if not already present) and add to sudoers
RUN useradd -m -s /bin/bash vscode && echo "vscode ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Set up XFCE session for xrdp
RUN echo "startxfce4" > /home/vscode/.xsession && chown vscode:vscode /home/vscode/.xsession

# Configure xrdp to use the Xfce session by default
RUN sed -i.bak 's/^port=3389$/port=3389/' /etc/xrdp/xrdp.ini && \
    sed -i 's/^#X11DisplayOffset/X11DisplayOffset/' /etc/xrdp/xrdp.ini

# Install Tailscale
RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.noarmor.gpg | tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null && \
    curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.tailscale-key.list | tee /etc/apt/sources.list.d/tailscale.list && \
    apt-get update && apt-get install -y tailscale && \
    rm -rf /var/lib/apt/lists/*

# Expose the RDP port
EXPOSE 3389

# Set the default user (this should match the remoteUser in devcontainer.json)
USER vscode

# Optionally, set the working directory
WORKDIR /home/vscode

# The container will start with an interactive shell. xrdp and tailscale are started via the postCreateCommand.
CMD [ "bash" ]
