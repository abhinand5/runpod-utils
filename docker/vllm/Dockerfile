FROM vllm/vllm-openai:v0.5.4
LABEL org.opencontainers.image.source="https://github.com/abhinand5/runpod-utils"

WORKDIR /workspace

RUN apt-get update && \
    apt-get install screen git curl wget -y

RUN wget https://github.com/coder/code-server/releases/download/v4.90.2/code-server_4.90.2_amd64.deb && \
    dpkg -i code-server_4.90.2_amd64.deb
    
RUN pip install ipykernel ipywidgets
    
EXPOSE 8080

CMD ["code-server", "--bind-addr", "0.0.0.0:8080"]