ARG CUDA_VERSION="11.8.0"
ARG CUDNN_VERSION="8"
ARG UBUNTU_VERSION="22.04"
ARG PYTORCH="2.0.1"
ARG CUDA="118"

#ARG DOCKER_FROM=nvidia/cuda:$CUDA_VERSION-cudnn$CUDNN_VERSION-base-ubuntu$UBUNTU_VERSION
FROM nvidia/cuda:11.8.0-base-ubuntu22.04 as runtime

# Import necessary base images
#FROM runpod/stable-diffusion-models:2.1 as hf-cache
#FROM $DOCKER_FROM AS runtime

# Повторно объявляем ARG после FROM, чтобы они были доступны в этом этапе сборки
ARG PYTORCH
ARG CUDA

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Set working directory and environment variables
ENV SHELL=/bin/bash
# Prevents prompts from packages asking for user input during installation
ENV DEBIAN_FRONTEND=noninteractive
# Prefer binary wheels over source distributions for faster pip installations
ENV PIP_PREFER_BINARY=1
# Ensures output from python is printed immediately to the terminal without buffering
ENV PYTHONUNBUFFERED=1 
# Speed up some cmake builds
ENV CMAKE_BUILD_PARALLEL_LEVEL=8

WORKDIR /

# Set up system
RUN apt-get update --yes && \
    apt-get upgrade --yes && \
    apt install --yes --no-install-recommends git wget curl bash libgl1 software-properties-common openssh-server nginx rsync curl && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt install python3.10-dev python3.10-venv -y --no-install-recommends && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen

# Set up Python and pip
RUN ln -s /usr/bin/python3.10 /usr/bin/python && \
    rm /usr/bin/python3 && \
    ln -s /usr/bin/python3.10 /usr/bin/python3 && \
    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    python get-pip.py

RUN python -m venv /venv
ENV PATH="/venv/bin:$PATH"

# Install necessary Python packages
RUN pip install --upgrade --no-cache-dir pip && \
    pip install --upgrade setuptools && \
    pip install --upgrade wheel
RUN pip install --upgrade --no-cache-dir torch==2.0.1+cu${CUDA} torchvision==0.15.2+cu${CUDA} torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu${CUDA}
RUN pip install --upgrade --no-cache-dir jupyterlab ipywidgets jupyter-archive jupyter_contrib_nbextensions triton xformers==0.0.22 gdown

# Set up Jupyter Notebook
RUN pip install notebook==6.5.5
RUN jupyter contrib nbextension install --user && \
    jupyter nbextension enable --py widgetsnbextension

# Install ComfyUI and ComfyUI Manager
RUN git clone https://github.com/comfyanonymous/ComfyUI.git && \
    cd /ComfyUI && \
    pip install -r requirements.txt && \
    git clone https://github.com/ltdrdata/ComfyUI-Manager.git custom_nodes/ComfyUI-Manager && \
    cd custom_nodes/ComfyUI-Manager && \
    pip install -r requirements.txt

# Create necessary directories and copy necessary files
RUN mkdir -p /usr/share/nginx/html

# NGINX Proxy
COPY official-templates/echeg-comfyui/build-context/nginx.conf /etc/nginx/nginx.conf
COPY official-templates/echeg-comfyui/build-context/readme.html /usr/share/nginx/html/readme.html

# Copy the README.md
COPY official-templates/echeg-comfyui/README.md /usr/share/nginx/html/README.md

# Start Scripts
COPY official-templates/echeg-comfyui/pre_start.sh /pre_start.sh
COPY official-templates/echeg-comfyui/build-context/start.sh /
RUN chmod +x /start.sh

CMD [ "/start.sh" ]
