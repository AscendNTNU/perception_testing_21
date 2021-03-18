ARG ARCH=
ARG CUDA=10.1
ARG UBUNTU_VERSION=18.04
ARG BASE_IMAGE=ros
ARG BASE_TAG=melodic

# NVIDIA CUDA Image
FROM nvidia/cuda${ARCH:+-$ARCH}:${CUDA}-base-ubuntu${UBUNTU_VERSION} AS base

# Multi-stage build with chosen ROS distro 
FROM ${BASE_IMAGE}:${BASE_TAG}

# Prevents bash from asking for user input
ENV DEBIAN_FRONTEND=noninteractive

# Set up environment 
RUN apt-get update && \
    apt-get install --no-install-recommends --fix-missing \
        build-essential \
        apt-utils \
        apt-transport-https \
        ca-certificates \
        software-properties-common \
        pkg-config \
        curl \
        wget \
        git \
        unzip \
        gpg-agent \
        sudo \ 
        tzdata \
        locales && \
    locale-gen en_US.UTF-8 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# GitHub Runner
ENV RUNNER_PATH=/home/runner
ENV RUNNER_ALLOW_RUNASROOT=1

RUN mkdir ${RUNNER_PATH}
WORKDIR ${RUNNER_PATH}