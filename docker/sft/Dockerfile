FROM winglian/axolotl-cloud:main-latest
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN wget https://github.com/coder/code-server/releases/download/v4.90.2/code-server_4.90.2_amd64.deb && \
    dpkg -i code-server_4.90.2_amd64.deb && \
    rm ./code-server_4.90.2_amd64.deb

RUN pip install --upgrade huggingface_hub[cli] \
    nvitop \
    numpy \
    scipy \
    scikit-learn \
    pandas \
    matplotlib \
    accelerate

RUN pip install flash-attn --no-build-isolation

RUN apt-get update && \
    apt-get install screen htop -y

EXPOSE 8080

CMD ["code-server", "--bind-addr", "0.0.0.0:8080"]