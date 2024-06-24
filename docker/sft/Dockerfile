FROM runpod/pytorch:2.2.0-py3.10-cuda12.1.1-devel-ubuntu22.04
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN curl -fsSL https://code-server.dev/install.sh | sh

RUN pip install --upgrade huggingface_hub[cli] \
    nvitop \
    numpy \
    scipy \
    scikit-learn \
    pandas \
    matplotlib \
    accelerate

RUN pip install flash-attn --no-build-isolation

RUN git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory && \
    cd LLaMA-Factory && \
    pip install -e '.[metrics,deepspeed,bitsandbytes]'

RUN git clone --depth 1 https://github.com/OpenAccess-AI-Collective/axolotl && \
    cd axolotl && \
    pip install packaging ninja && \
    # Temporarily loosen the xformers version
    sed -i 's/xformers==0.0.26.post1/xformers/' requirements.txt && \
    pip3 install -e '.'

RUN pip install "unsloth[cu121-torch220] @ git+https://github.com/unslothai/unsloth.git"

RUN apt-get update && \
    apt-get install screen htop -y

CMD [ "/start.sh" ]