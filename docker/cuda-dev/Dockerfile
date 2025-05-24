# CUDA Development Container for RunPod with VSCode Web and Neovim
FROM nvidia/cuda:12.3.2-devel-ubuntu22.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ENV CODE_SERVER_VERSION=4.20.0
ENV NODE_VERSION=18

# Install system dependencies
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    git \
    build-essential \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release \
    sudo \
    vim \
    nano \
    htop \
    tree \
    unzip \
    zip \
    openssh-server \
    tmux \
    screen \
    python3 \
    python3-pip \
    python3-dev \
    python3-venv \
    cmake \
    ninja-build \
    pkg-config \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Node.js (required for some VSCode extensions)
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs

# Install Neovim from official repository
RUN add-apt-repository ppa:neovim-ppa/unstable -y \
    && apt-get update \
    && apt-get install -y neovim \
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user for development
RUN useradd -m -s /bin/bash -G sudo developer \
    && echo "developer:developer" | chpasswd \
    && echo "developer ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Switch to developer user
USER developer
WORKDIR /home/developer

# Install code-server (VSCode web)
RUN curl -fsSL https://code-server.dev/install.sh | sh -s -- --version=${CODE_SERVER_VERSION}

# Create workspace directory
RUN mkdir -p /home/developer/workspace

# Install Python packages commonly used in CUDA development
RUN pip3 install --user \
    torch \
    torchvision \
    torchaudio \
    numpy \
    pandas \
    matplotlib \
    seaborn \
    jupyter \
    jupyterlab \
    scikit-learn \
    opencv-python \
    pillow \
    tqdm \
    wandb \
    tensorboard \
    cupy-cuda12x \
    numba

# Configure Neovim
RUN mkdir -p ~/.config/nvim \
    && echo 'set number\nset relativenumber\nset autoindent\nset tabstop=4\nset shiftwidth=4\nset smarttab\nset softtabstop=4\nset mouse=a\nset clipboard=unnamedplus\nsyntax on\nset background=dark' > ~/.config/nvim/init.vim

# Install vim-plug for Neovim
RUN sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'

# Configure code-server
RUN mkdir -p ~/.config/code-server \
    && echo 'bind-addr: 0.0.0.0:8080\nauth: none\ncert: false' > ~/.config/code-server/config.yaml

# Install useful VSCode extensions
RUN code-server --install-extension ms-python.python \
    --install-extension ms-vscode.cpptools \
    --install-extension nvidia.nsight-vscode-edition \
    --install-extension ms-toolsai.jupyter \
    --install-extension vscodevim.vim \
    --install-extension ms-vscode.cmake-tools \
    --install-extension twxs.cmake \
    --install-extension ms-vscode.makefile-tools

# Create a startup script
RUN echo '#!/bin/bash\n\
echo "=== CUDA Development Environment ==="\n\
echo "CUDA Version: $(nvcc --version | grep release)"\n\
echo "Python Version: $(python3 --version)"\n\
echo "Node.js Version: $(node --version)"\n\
echo "Neovim Version: $(nvim --version | head -1)"\n\
echo "===================================="\n\
echo ""\n\
echo "VSCode Web is starting on port 8080..."\n\
echo "JupyterLab can be started with: jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root"\n\
echo ""\n\
cd /home/developer/workspace\n\
code-server --bind-addr 0.0.0.0:8080 --auth none\n' > /home/developer/start.sh \
    && chmod +x /home/developer/start.sh

# Create a sample CUDA program
RUN echo '#include <iostream>\n#include <cuda_runtime.h>\n\n__global__ void hello_cuda() {\n    printf("Hello from GPU thread %d!\\n", threadIdx.x);\n}\n\nint main() {\n    std::cout << "Hello from CPU!" << std::endl;\n    \n    // Launch kernel with 10 threads\n    hello_cuda<<<1, 10>>>();\n    \n    // Wait for GPU to finish\n    cudaDeviceSynchronize();\n    \n    return 0;\n}' > /home/developer/workspace/hello_cuda.cu

# Create a Makefile for the sample program
RUN echo 'NVCC = nvcc\nCUDA_FLAGS = -std=c++11\n\nhello_cuda: hello_cuda.cu\n\t$(NVCC) $(CUDA_FLAGS) -o hello_cuda hello_cuda.cu\n\nclean:\n\trm -f hello_cuda\n\n.PHONY: clean' > /home/developer/workspace/Makefile

# Create a Python CUDA test script
RUN echo 'import torch\nimport sys\n\nprint("Python CUDA Environment Test")\nprint("=" * 30)\nprint(f"Python version: {sys.version}")\nprint(f"PyTorch version: {torch.__version__}")\nprint(f"CUDA available: {torch.cuda.is_available()}")\n\nif torch.cuda.is_available():\n    print(f"CUDA version: {torch.version.cuda}")\n    print(f"Number of GPUs: {torch.cuda.device_count()}")\n    for i in range(torch.cuda.device_count()):\n        print(f"GPU {i}: {torch.cuda.get_device_name(i)}")\n    \n    # Simple tensor operation on GPU\n    x = torch.randn(3, 3).cuda()\n    y = torch.randn(3, 3).cuda()\n    z = torch.mm(x, y)\n    print(f"\\nGPU tensor multiplication successful!")\n    print(f"Result shape: {z.shape}")\nelse:\n    print("CUDA not available - running on CPU only")' > /home/developer/workspace/test_cuda.py

# Set working directory
WORKDIR /home/developer/workspace

# Expose ports
EXPOSE 8080 8888

# Start command
CMD ["/home/developer/start.sh"]