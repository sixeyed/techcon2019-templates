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
# Build the developer image to compile C++ applications with Arm NN
# Only libraries and header files are needed
#----------------------------------------------------------------------------
FROM base AS dev

WORKDIR /home/ubuntu 
USER ubuntu 

RUN mkdir -p ~/armnn/lib
RUN mkdir -p ~/armnn/include
COPY --from=armnn/sdk /home/ubuntu/armnn-devenv/pkg/install/lib/libprotobuf.so.15 /home/ubuntu/armnn/lib
COPY --from=armnn/sdk /home/ubuntu/armnn-devenv/armnn/build/libarmnnTfParser.so /home/ubuntu/armnn/lib
COPY --from=armnn/sdk /home/ubuntu/armnn-devenv/armnn/build/libarmnn.so /home/ubuntu/armnn/lib
COPY --from=armnn/sdk /home/ubuntu/armnn-devenv/armnn/include/ /home/ubuntu/armnn/include
RUN chrpath -r /home/ubuntu/armnn/lib /home/ubuntu/armnn/lib/libarmnnTfParser.so

ENV LD_LIBRARY_PATH=/home/ubuntu/armnn/lib
COPY . .
RUN make


#----------------------------------------------------------------------------
# Build an image to just run the application, no source code
#----------------------------------------------------------------------------
FROM base

WORKDIR /home/ubuntu 
USER ubuntu 

COPY --from=dev /home/ubuntu/mnist_tf_convol /home/ubuntu/
COPY --from=dev /home/ubuntu/data/ /home/ubuntu/data
COPY --from=dev /home/ubuntu/model/ /home/ubuntu/model
COPY --from=dev /home/ubuntu/armnn/ /home/ubuntu/armnn

ENV LD_LIBRARY_PATH=/home/ubuntu/armnn/lib
ENTRYPOINT ["./mnist_tf_convol"]
CMD ["1", "10"]
