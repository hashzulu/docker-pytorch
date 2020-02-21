FROM nvidia/cuda:8.0-runtime-ubuntu16.04

# Install some basic utilities
RUN apt-get update && apt-get install -y \
    curl \
    ca-certificates \
    sudo \
    git \
    bzip2 \
    libx11-6 \
    && rm -rf /var/lib/apt/lists/*

# Create a working directory
RUN mkdir /app
WORKDIR /app

# Create a non-root user and switch to it
RUN adduser --disabled-password --gecos '' --shell /bin/bash user \
    && chown -R user:user /app
RUN echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-user
USER user

# All users can use /home/user as their home directory
ENV HOME=/home/user
RUN chmod 777 /home/user

# Install Miniconda
RUN curl -so ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-4.5.11-Linux-x86_64.sh \
    && chmod +x ~/miniconda.sh \
    && ~/miniconda.sh -b -p ~/miniconda \
    && rm ~/miniconda.sh
ENV PATH=/home/user/miniconda/bin:$PATH
ENV CONDA_AUTO_UPDATE_CONDA=false

# Create a Python 3.6 environment
RUN /home/user/miniconda/bin/conda install conda-build \
    && /home/user/miniconda/bin/conda create -y --name py36 python=3.6.5 \
    && /home/user/miniconda/bin/conda clean -ya
ENV CONDA_DEFAULT_ENV=py36
ENV CONDA_PREFIX=/home/user/miniconda/envs/$CONDA_DEFAULT_ENV
ENV PATH=$CONDA_PREFIX/bin:$PATH

# CUDA 8.0-specific steps
RUN conda install -y -c pytorch \
    cuda80=1.0 \
    magma-cuda80=2.3.0 \
    pytorch=0.3.0 \
    torchvision=0.2.0 \
    && conda clean -ya
    
RUN sudo dpkg -i cuda-repo-ubuntu1604_8.0.44-1_amd64.deb \
    sudo apt-get update \
    sudo apt-get install cuda-toolkit-8-0 \

# Install HDF5 Python bindings
RUN conda install -y h5py=2.8.0 \
    && conda clean -ya
RUN pip install h5py-cache==1.0

#Install Pygame
#install dependencies
RUN sudo apt-get install git python3-dev python3-setuptools python3-numpy python3-opengl \
    libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsmpeg-dev \
    libsdl1.2-dev libportmidi-dev libswscale-dev libavformat-dev libavcodec-dev \
    libtiff5-dev libx11-6 libx11-dev fluid-soundfont-gm timgm6mb-soundfont \
    xfonts-base xfonts-100dpi xfonts-75dpi xfonts-cyrillic fontconfig fonts-freefont-ttf libfreetype6-dev

RUN sudo pip install https://github.com/pygame/pygame.git

# Install Torchnet, a high-level framework for PyTorch
RUN pip install torchnet==0.0.4

# Install Requests, a Python library for making HTTP requests
RUN conda install -y requests=2.19.1 \
    && conda clean -ya
    
# Install TensorboardX
RUN conda install -c conda-forge tensorboardx

# Install Graphviz
RUN conda install -y graphviz-python \
    && conda clean -ya
RUN pip install graphviz==0.8.4

# Copy the requirement file to the continer 
ADD /requirements.txt /app/
RUN pip install -r requirements.txt

# Install OpenCV3 Python bindings
RUN sudo apt-get update && sudo apt-get install -y --no-install-recommends \
    libgtk2.0-0 \
    libcanberra-gtk-module \
    && sudo rm -rf /var/lib/apt/lists/*
RUN conda install -y -c menpo opencv3=3.1.0 \
    && conda clean -ya

# Set the default command to python3
CMD ["python3"]
