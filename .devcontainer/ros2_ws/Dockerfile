FROM ubuntu:jammy

LABEL org.opencontainers.image.title="ros2_ws"
LABEL org.opencontainers.image.description="ROS2 workspace"
LABEL org.opencontainers.image.vendor="Sentience Robotics"
LABEL org.opencontainers.image.authors="Sentience Robotics"

ARG DEBIAN_FRONTEND=noninteractive
ARG ROS_DISTRO=humble
ARG USERNAME=rosdev
ARG UID=1000
ARG GID=$UID

# Set the locale
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

# Install some dependencies packages
RUN apt update -q && \
    apt upgrade -q -y && \
    apt install -y --no-install-recommends \
    software-properties-common \
    python3-pip \
    git zsh vim curl wget lsb-release gnupg sudo clang clangd \
    libboost-all-dev && \
    apt-get clean

# Install ROS2 & Gazebo
RUN add-apt-repository universe && \
    curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null && \
    apt update -q && \
    apt upgrade -q -y && \
    apt install -y ros-humble-desktop ros-humble-ros-gz ros-dev-tools

# Install ROS2 miscellaneous packages
RUN apt install -y ros-humble-joint-state-publisher ros-humble-joint-state-publisher-gui

# Create and switch to user
RUN groupadd -g $GID $USERNAME \
    && useradd -lm -u $UID -g $USERNAME -s /bin/bash $USERNAME \
    && echo "$USERNAME ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers

# Setup zsh
RUN touch /home/$USERNAME/.zshrc && \
    chsh -s /bin/zsh $USERNAME
ENV SHELL=/bin/zsh
USER $USERNAME
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Create workspace so that user own this directory
RUN mkdir -p /home/$USERNAME/ros2_ws/src
WORKDIR /home/$USERNAME/ros2_ws

# Copy configuration files
RUN echo 'source /opt/ros/'$ROS_DISTRO'/setup.bash' >> /home/$USERNAME/.bashrc \
    && echo 'source /home/'$USERNAME'/ros2_ws/install/local_setup.bash' >> /home/$USERNAME/.bashrc

RUN echo 'source /opt/ros/'$ROS_DISTRO'/setup.zsh' >> /home/$USERNAME/.zshrc \
    && echo 'source /home/'$USERNAME'/ros2_ws/install/local_setup.zsh' >> /home/$USERNAME/.zshrc

CMD ["zsh"]
