#----------------------------------------------------------------------------
# Base image derived from Ubuntu 18.04 used as the starting point for all stages
# Install all needed tools and setup the user account
# The final release image could be optimized to be smaller if needed
#----------------------------------------------------------------------------
FROM ubuntu:18.04 as base

RUN echo "root:Arm2019" | chpasswd

RUN apt-get update -y && \
      apt-get -y install sudo vim iputils-ping net-tools curl wget dialog software-properties-common apt-utils chrpath git make cmake gcc g++ autoconf autogen libtool scons unzip bzip2 

RUN useradd --create-home -s /bin/bash -m ubuntu && echo "ubuntu:Arm2019" | chpasswd && adduser ubuntu sudo

RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

WORKDIR /home/ubuntu
USER ubuntu


#----------------------------------------------------------------------------
# Build the entire Arm NN SDK, a large one time activity
#----------------------------------------------------------------------------
FROM base 

WORKDIR /home/ubuntu
USER ubuntu

# Build machine and target machine linux/arm/v7  linux/arm64 or linux/amd64
ARG TARGETPLATFORM
ARG BUILDPLATFORM

COPY --chown=ubuntu:ubuntu build-armnn.sh /home/ubuntu

RUN if [ "$TARGETPLATFORM" = "linux/arm/v7" ] ; then /home/ubuntu/build-armnn.sh -a armv7a ; else /home/ubuntu/build-armnn.sh ; fi