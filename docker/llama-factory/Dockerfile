FROM runpod/pytorch:2.2.0-py3.10-cuda12.1.1-devel-ubuntu22.04
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN curl -fsSL https://code-server.dev/install.sh | sh

RUN git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory && \
    cd LLaMA-Factory && \
    pip install -e '.[metrics,deepspeed,bitsandbytes]'

RUN pip install "unsloth[cu121-torch220] @ git+https://github.com/unslothai/unsloth.git"

RUN pip install huggingface_hub[cli] nvitop

RUN apt-get update && \
    apt-get install screen -y

CMD [ "/start.sh" ]