ARG ROS_DISTRO=humble
FROM ros:$ROS_DISTRO-perception
# set deb non-interactive
ARG DEBIAN_FRONTEND=noninteractive
# upgrade
RUN apt-get update \
    && apt-get upgrade -y \
    && rm -rf /var/lib/apt/lists/*
# set time zone
RUN apt-get update \
    && apt-get install -y tzdata \
    && rm -rf /var/lib/apt/lists/*
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# install basic tools
RUN apt-get update \
    && apt-get install -y software-properties-common apt-utils \
                          bash-completion wget curl vim git sudo tmux \
    && rm -rf /var/lib/apt/lists/*
# install gcc-13
RUN add-apt-repository -y ppa:ubuntu-toolchain-r/test \
    && apt-get update \
    && apt-get install -y build-essential gcc-13 g++-13 gdb ninja-build \
    && rm -rf /var/lib/apt/lists/*
# install llvm-18
RUN wget https://apt.llvm.org/llvm.sh \
    && bash llvm.sh 18 all \
    && rm llvm.sh \
    && rm -rf /var/lib/apt/lists/* 
ENV PATH="/usr/lib/llvm-18/bin:$PATH"
# install cmake
RUN wget https://apt.kitware.com/kitware-archive.sh \
    && bash kitware-archive.sh \
    && apt-get install -y cmake \
    && rm kitware-archive.sh \
    && rm -rf /var/lib/apt/lists/*
# install libraries
RUN apt-get update \
    && apt-get install -y \
        ros-$ROS_DISTRO-rosbag2-storage-mcap \
        ros-$ROS_DISTRO-rosbridge-suite \
        ros-$ROS_DISTRO-foxglove-bridge \
    && rm -rf /var/lib/apt/lists/*
# create user
ARG USERNAME=user
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && usermod --shell /bin/bash $USERNAME
USER $USERNAME
WORKDIR /home/$USERNAME
RUN echo "source /opt/ros/$ROS_DISTRO/setup.bash" >> .bashrc