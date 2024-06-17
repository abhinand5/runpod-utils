FROM pytorch/pytorch:2.3.1-cuda12.1-cudnn8-devel
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN curl -fsSL https://code-server.dev/install.sh | sh

RUN pip install git+https://github.com/huggingface/transformers

RUN pip install datasets \
    numpy \
    scipy \
    scikit-learn \
    huggingface_hub \
    nvitop \
    packaging \
    ninja \
    bitsandbytes \
    accelerate

RUN pip install flash-attn --no-build-isolation

RUN pip install "unsloth[cu121-torch220] @ git+https://github.com/unslothai/unsloth.git"

RUN apt-get update && \
    apt-get install screen -y

CMD ["code-server", "--bind-addr", "0.0.0.0:8080"]