ARG ARCH=
ARG UBUNTU_VERSION=18.04
ARG BASE_IMAGE=ros
ARG BASE_TAG=melodic

# Chosen ROS distro as base image 
FROM ${BASE_IMAGE}:${BASE_TAG} as base

# Prevents bash from asking for user input
ENV DEBIAN_FRONTEND=noninteractive

# Set up environment 
RUN apt-get update && \
    apt-get install -y --no-install-recommends --fix-missing \
        build-essential \
        apt-utils \
        apt-transport-https \
        ca-certificates \
        software-properties-common \
        pkg-config \
        curl \
        wget \
        unzip \
        gpg-agent \
        sudo \ 
        tzdata \
        libomp-dev \
        locales && \
    locale-gen en_US.UTF-8 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# # Newest version of Git
# RUN sudo apt-get install -y software-properties-common \
#     && sudo apt-get update \
#     && sudo add-apt-repository -y ppa:git-core/ppa \
#     && sudo apt-get update \
#     && sudo apt-get install -y git

################################## NVIDIA Docker base ##################################
RUN apt-get update && apt-get install -y --no-install-recommends \
    gnupg2 curl ca-certificates && \
    curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub | apt-key add - && \
    echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list && \
    echo "deb https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/*

ENV CUDA_VERSION 11.2.2

# For libraries in the cuda-compat-* package: https://docs.nvidia.com/cuda/eula/index.html#attachment-a
RUN apt-get update && apt-get install -y --no-install-recommends \
    cuda-cudart-11-2=11.2.152-1 \
    cuda-compat-11-2 \
    && ln -s cuda-11.2 /usr/local/cuda && \
    rm -rf /var/lib/apt/lists/*

# Required for nvidia-docker v1
RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf \
    && echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

# nvidia-container-runtime
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility
ENV NVIDIA_REQUIRE_CUDA "cuda>=11.2 brand=tesla,driver>=418,driver<419 brand=tesla,driver>=440,driver<441 driver>=450,driver<451"


################################## NVIDIA Docker runtime ##################################
ENV NCCL_VERSION 2.8.4

RUN apt-get update && apt-get install -y --no-install-recommends \
    cuda-libraries-11-2=11.2.2-1 \
    libnpp-11-2=11.3.2.152-1 \
    cuda-nvtx-11-2=11.2.152-1 \
    libcublas-11-2=11.4.1.1043-1 \
    libcusparse-11-2=11.4.1.1152-1 \
    libnccl2=$NCCL_VERSION-1+cuda11.2 \
    && rm -rf /var/lib/apt/lists/*

# apt from auto upgrading the cublas package. See https://gitlab.com/nvidia/container-images/cuda/-/issues/88
RUN apt-mark hold libcublas-11-2 libnccl2


################################## NVIDIA Docker devel ##################################
ENV NCCL_VERSION 2.8.4

RUN apt-get update && apt-get install -y --no-install-recommends \
    cuda-cudart-dev-11-2=11.2.152-1 \
    cuda-command-line-tools-11-2=11.2.2-1 \
    cuda-minimal-build-11-2=11.2.2-1 \
    cuda-libraries-dev-11-2=11.2.2-1 \
    cuda-nvml-dev-11-2=11.2.152-1 \
    libnpp-dev-11-2=11.3.2.152-1 \
    libnccl-dev=2.8.4-1+cuda11.2 \
    libcublas-dev-11-2=11.4.1.1043-1 \
    libcusparse-dev-11-2=11.4.1.1152-1 \
    && rm -rf /var/lib/apt/lists/*

# apt from auto upgrading the cublas package. See https://gitlab.com/nvidia/container-images/cuda/-/issues/88
RUN apt-mark hold libcublas-dev-11-2 libnccl-dev
ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs


################################## NVIDIA Docker devel cuDNN ##################################
ENV CUDNN_VERSION 8.1.1.33

RUN apt-get update && apt-get install -y --no-install-recommends \
    libcudnn8=$CUDNN_VERSION-1+cuda11.2 \
    libcudnn8-dev=$CUDNN_VERSION-1+cuda11.2 \
    && apt-mark hold libcudnn8 && \
    rm -rf /var/lib/apt/lists/*



# GitHub Runner
ENV RUNNER_PATH=/home/runner
ENV RUNNER_ALLOW_RUNASROOT=1

RUN mkdir ${RUNNER_PATH}
WORKDIR ${RUNNER_PATH}