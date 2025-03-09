# ComfyUI Lite Docker Image

This is a lightweight version of ComfyUI with CUDA support.

## Features

- Based on NVIDIA CUDA container
- Python 3.10
- PyTorch with CUDA support
- ComfyUI with Manager
- Jupyter Notebook support
- NGINX as reverse proxy

## Usage

```bash
docker pull ghcr.io/<username>/comfyui-lite:latest
docker run --gpus all -p 3001:3001 -p 8888:8888 ghcr.io/<username>/comfyui-lite:latest
```

## Build Arguments

- `CUDA_VERSION`: CUDA version (default: "12.1.1")
- `CUDNN_VERSION`: CUDNN version (default: "8")
- `UBUNTU_VERSION`: Ubuntu version (default: "22.04")
- `PYTORCH`: PyTorch version (default: "2.4.0")
- `CUDA`: CUDA version for PyTorch (default: "121")

## Template Requirements

| Port | Type (HTTP/TCP) | Function     |
|------|-----------------|--------------|
| 22   | TCP             | SSH          |
| 3001 | HTTP            | Comfy Web UI |
| 8888 | HTTP            | Jupyter Lab  |
