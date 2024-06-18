FROM pytorch/pytorch:2.3.1-cuda12.1-cudnn8-devel
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN apt-get update && \
    apt-get install screen git build-essential wget curl -y

RUN wget https://github.com/coder/code-server/releases/download/v4.90.2/code-server_4.90.2_amd64.deb && \
    dpkg -i code-server_4.90.2_amd64.deb

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
    accelerate \
    trl \
    peft \
    pandas \
    matplotlib \
    huggingface_hub[cli]

RUN pip install flash-attn --no-build-isolation

# This downloads the cuda again...disabling temporarily!
#RUN pip install "unsloth[cu121-torch230] @ git+https://github.com/unslothai/unsloth.git"

EXPOSE 8080

CMD ["code-server", "--bind-addr", "0.0.0.0:8080"]