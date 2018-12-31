FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04


# Adds metadata to the image as a key value pair example LABEL version="1.0"
LABEL maintainer="Beau <cbeauhilton@gmail.com>"

ARG PYTHON_VERSION=3.6

RUN apt-get update && apt-get install -y --no-install-recommends \
         build-essential \
         cmake \
         git \
         curl \
         vim \
         libjpeg-dev \
         wget \
         bzip2 \
         ca-certificates \
         git-core \
         htop \
         pkg-config \
         unzip \
         unrar \
         tree \
         freetds-dev \
         libpng-dev &&\
     apt-get clean && rm -rf /var/lib/apt/lists/*
     
# Install OpenSlide
RUN apt-get install -y openslide-tools

RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh && \
     /opt/conda/bin/conda config --add channels defaults && \
     /opt/conda/bin/conda config --add channels bioconda && \
     /opt/conda/bin/conda config --add channels conda-forge && \
     /opt/conda/bin/conda install -y python=$PYTHON_VERSION numpy \
     pyyaml \
     scipy \ 
     lmdb \
     h5py \
     mkl \
     mkl-include \
     cython \
     jupyter \
     shap \
     opencv \
     shapely \
     openslide-python && \
     /opt/conda/bin/conda install -y -c pytorch magma-cuda90 && \
     /opt/conda/bin/conda update conda && \
     /opt/conda/bin/conda update conda -y --all && \
     /opt/conda/bin/conda clean -ya

# Set path to conda
ENV PATH /opt/conda/bin:$PATH

# This must be done before pip so that requirements.txt is available

WORKDIR /opt/pytorch
COPY . .

RUN git submodule update --init
RUN TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip install -v .

RUN git clone https://github.com/pytorch/vision.git && cd vision && pip install -v .

# Install Jupyter theme
RUN pip install msgpack jupyterthemes
RUN jt -t grade3

# Install py-wsi from Github 
RUN git clone https://github.com/ysbecca/py-wsi.git

WORKDIR /workspace
RUN chmod -R a+w /workspace
