# Ultra-lightweight PyTorch CUDA image for RunPod.io
FROM nvidia/cuda:12.6.0-runtime-ubuntu24.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ENV PATH="/opt/conda/bin:${PATH}"
ENV TORCH_CUDA_ARCH_LIST="7.0 7.5 8.0 8.6 8.9 9.0+PTX"

# Install minimal system dependencies in a single layer
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    curl \
    git \
    ca-certificates \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender-dev \
    libgomp1 \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Miniconda (lighter than full Anaconda)
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -afy

# Install PyTorch and essential ML packages in one go
RUN conda install -y python=3.11 && \
    conda install -y -c pytorch -c nvidia \
    pytorch torchvision torchaudio pytorch-cuda=12.6 && \
    conda install -y numpy scipy pandas matplotlib && \
    conda clean -afy && \
    pip cache purge

# Install code-server (VSCode in browser)
RUN curl -fsSL https://code-server.dev/install.sh | sh && \
    rm -rf /tmp/* /var/tmp/*

# Create workspace directory
WORKDIR /workspace

# Expose ports
EXPOSE 8080 6006

# Create startup script
RUN echo '#!/bin/bash\n\
# Start code-server\n\
code-server --bind-addr 0.0.0.0:8080 --auth none --disable-telemetry /workspace &\n\
\n\
# Keep container running\n\
tail -f /dev/null' > /start.sh && \
    chmod +x /start.sh

# RunPod.io specific environment variables
ENV RUNPOD_PORT=8080
ENV PUBLIC_KEY=""
ENV JUPYTER_PASSWORD=""

# Set the default command
CMD ["/start.sh"]
