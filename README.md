# LeRobot Setup Instructions for Linux

This repository contains comprehensive setup instructions for installing and configuring LeRobot on Linux systems. LeRobot is Hugging Face's toolkit for real-world robotics, providing state-of-the-art pre-trained models, datasets, and tools for robotics.

## Table of Contents
- [System Requirements](#system-requirements)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Method 1: Install from PyPI (Recommended)](#method-1-install-from-pypi-recommended)
  - [Method 2: Install from Source](#method-2-install-from-source)
- [Hardware Setup](#hardware-setup)
- [Verification](#verification)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

## System Requirements

- **Operating System**: Ubuntu 20.04 LTS or later (tested on Ubuntu 20.04, 22.04)
- **Python**: 3.10 or later
- **RAM**: Minimum 8GB (16GB+ recommended for training)
- **Disk Space**: At least 10GB free space
- **GPU** (Optional but recommended): NVIDIA GPU with CUDA 11.8+ support for faster training/inference

## Prerequisites

### 1. Update System Packages

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Install Python 3.10+

Check your Python version:
```bash
python3 --version
```

If you need to install Python 3.10 or later:
```bash
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.10 python3.10-venv python3.10-dev -y
```

### 3. Install pip

```bash
sudo apt install python3-pip -y
python3 -m pip install --upgrade pip
```

### 4. Install System Dependencies

```bash
sudo apt install -y \
    git \
    cmake \
    build-essential \
    libgl1-mesa-glx \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender-dev \
    libgomp1 \
    ffmpeg
```

### 5. (Optional) Install CUDA for GPU Support

If you have an NVIDIA GPU and want to use GPU acceleration:

```bash
# Check if you have an NVIDIA GPU
lspci | grep -i nvidia

# Check recommended drivers for your system
ubuntu-drivers devices

# Install NVIDIA drivers (if not already installed)
# Use the recommended driver version, or a recent stable version like:
sudo apt install nvidia-driver-535 -y
# For newer systems, you may use: sudo apt install nvidia-driver-550 -y

# Reboot after driver installation
sudo reboot
```

After reboot, verify NVIDIA driver installation:
```bash
nvidia-smi
```

For CUDA toolkit installation, follow the official [NVIDIA CUDA installation guide](https://developer.nvidia.com/cuda-downloads).

## Installation

### Method 1: Install from PyPI (Recommended)

This is the simplest method for most users.

1. **Create a virtual environment** (recommended):
```bash
python3 -m venv lerobot-env
source lerobot-env/bin/activate
```

2. **Install LeRobot**:
```bash
pip install lerobot
```

3. **For GPU support**, install PyTorch with CUDA:

First, check your CUDA version if you have it installed:
```bash
nvcc --version  # Check CUDA version
nvidia-smi      # Check driver and CUDA compatibility
```

Then install PyTorch with matching CUDA support:
```bash
# For CUDA 11.8:
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

# For CUDA 12.1:
# pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# For CPU only (no GPU):
# pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
```

### Method 2: Install from Source

For development or to get the latest features:

1. **Clone the repository**:
```bash
git clone https://github.com/huggingface/lerobot.git
cd lerobot
```

2. **Create and activate a virtual environment**:
```bash
python3 -m venv lerobot-env
source lerobot-env/bin/activate
```

3. **Install in editable mode**:
```bash
pip install -e .
```

4. **Install development dependencies** (optional):
```bash
pip install -e ".[dev]"
```

## Hardware Setup

### Setting up Robot Hardware

LeRobot supports various robot platforms. Common setup steps:

1. **USB Permissions** (for connected robots):
```bash
# Add your user to the dialout group for USB device access
sudo usermod -a -G dialout $USER

# You may need to log out and back in for this to take effect
```

2. **Camera Setup** (if using vision-based control):
```bash
# Install v4l-utils for camera management
sudo apt install v4l-utils -y

# List available cameras
v4l2-ctl --list-devices
```

3. **Real-time Permissions** (for real-time control):
```bash
# Add real-time scheduling permissions
sudo groupadd realtime
sudo usermod -a -G realtime $USER
echo "@realtime soft rtprio 99" | sudo tee -a /etc/security/limits.conf
echo "@realtime soft nice -20" | sudo tee -a /etc/security/limits.conf
```

## Verification

### 1. Verify Installation

```bash
python3 -c "import lerobot; print(f'LeRobot version: {lerobot.__version__}')"
```

### 2. Check PyTorch and GPU Support

```bash
python3 -c "import torch; print(f'PyTorch version: {torch.__version__}'); print(f'CUDA available: {torch.cuda.is_available()}')"
```

### 3. Run a Simple Test

```python
# Create a test file: test_lerobot.py
from lerobot import available_robots, available_datasets

print("Available robots:", available_robots())
print("Available datasets:", available_datasets())
```

Run the test:
```bash
python3 test_lerobot.py
```

### 4. Try Running a Pre-trained Model (Optional)

```bash
# Example: Visualize a dataset
python3 -m lerobot.scripts.visualize_dataset \
    --repo-id lerobot/pusht \
    --episode-index 0
```

## Troubleshooting

### Issue: ImportError for OpenGL or display-related errors

**Solution**: Install missing display libraries
```bash
sudo apt install -y libgl1-mesa-glx libglib2.0-0
```

### Issue: "ModuleNotFoundError: No module named 'lerobot'"

**Solution**: 
- Ensure your virtual environment is activated
- Verify installation: `pip list | grep lerobot`
- Reinstall if necessary: `pip install --force-reinstall lerobot`

### Issue: USB device permission denied

**Solution**:
```bash
sudo usermod -a -G dialout $USER
# Log out and log back in, or run:
newgrp dialout
```

### Issue: CUDA out of memory

**Solution**:
- Reduce batch size in your training configuration
- Use gradient accumulation
- Close other GPU-consuming processes: `nvidia-smi` to check

### Issue: Camera not detected

**Solution**:
```bash
# Check connected cameras
ls -l /dev/video*

# Test camera access
sudo apt install cheese -y
cheese  # GUI camera viewer
```

### Issue: Slow performance without GPU

**Solution**:
- Verify CUDA installation: `nvidia-smi`
- Check PyTorch CUDA support: `python3 -c "import torch; print(torch.cuda.is_available())"`
- Reinstall PyTorch with CUDA support (see Installation section)

## Additional Resources

- **Official LeRobot Repository**: https://github.com/huggingface/lerobot
- **Documentation**: https://huggingface.co/docs/lerobot
- **Community Forum**: https://discuss.huggingface.co/
- **Pre-trained Models**: https://huggingface.co/lerobot
- **Datasets**: https://huggingface.co/datasets?search=lerobot

## Getting Started

Once installation is complete, check out these resources to start using LeRobot:

1. **Browse pre-trained models**: Visit https://huggingface.co/lerobot
2. **Explore datasets**: Check https://huggingface.co/datasets?search=lerobot
3. **Run examples**: Look in the `examples/` directory (if installed from source)
4. **Join the community**: Participate in discussions at https://discuss.huggingface.co/

## Contributing

If you find issues with these instructions or want to add support for additional Linux distributions, please open an issue or submit a pull request.

## License

This guide is provided as-is for the LeRobot community. LeRobot itself is licensed under the Apache 2.0 License. 
