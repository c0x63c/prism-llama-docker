FROM pytorch/pytorch:2.13.0-cuda13.0-cudnn9-runtime

# argment
ARG LLAMA_URL
ENV DEBIAN_FRONTEND=noninteractive
ENV LD_LIBRARY_PATH="/usr/local/lib/python3.12/dist-packages/nvidia/cu13/lib/:${LD_LIBRARY_PATH}"

# install download tools
RUN apt-get update && \
    apt-get install -y --no-install-recommends wget ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# llama.cpp (PrismML fork, CUDA build)
RUN mkdir -p /prism-ml/bin && \
    wget -q -O /tmp/llama.tar.gz "${LLAMA_URL}" && \
    tar -xzf /tmp/llama.tar.gz -C /prism-ml/bin && \
    rm -f /tmp/llama.tar.gz

# move binaries
RUN BIN_DIR=$(find /prism-ml/bin f -name 'llama-server' | head -n 1 | xargs dirname) && \
    for f in "$BIN_DIR"/llama-server "$BIN_DIR"/llama-cli; do \
        [ -f "$f" ] && ln -sf "$f" /usr/local/bin/ || true; \
    done && \
    { echo "$BIN_DIR" | grep -q . && echo "$BIN_DIR" > /etc/ld.so.conf.d/llama.conf && ldconfig; } || true

WORKDIR /prism-ml/bin
