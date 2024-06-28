FROM runpod/pytorch:2.2.0-py3.10-cuda12.1.1-devel-ubuntu22.04
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN curl -fsSL https://code-server.dev/install.sh | sh

RUN git clone https://github.com/oobabooga/text-generation-webui && \
    cd text-generation-webui && \
    pip install -r requirements.txt

RUN pip install huggingface_hub[cli] nvitop

WORKDIR /workspace/text-generation-webui

CMD [ "python", "server.py" ]