FROM python:3.12-slim AS model-downloader

RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir huggingface_hub

ENV HF_HOME=/tmp/huggingface
ARG HF_TOKEN=

RUN mkdir -p /models && python - <<'PY'
from huggingface_hub import hf_hub_download

hf_hub_download(
    repo_id="cduk/embeddinggemma-300m-GGUF-with-dense-modules",
    filename="embeddinggemma-300M-Q8_0.gguf",
    local_dir="/models",
)
PY


FROM ghcr.io/ggml-org/llama.cpp:server-b12429

COPY --from=model-downloader \
    /models/embeddinggemma-300M-Q8_0.gguf \
    /models/embeddinggemma-300M-Q8_0.gguf

EXPOSE 8000

ENTRYPOINT ["/app/llama-server"]

CMD [ \
    "--model", "/models/embeddinggemma-300M-Q8_0.gguf", \
    "--alias", "google/embeddinggemma-300m", \
    "--host", "0.0.0.0", \
    "--port", "8000", \
    "--embedding", \
    "--pooling", "mean", \
    "--ctx-size", "2048", \
    "--batch-size", "2048", \
    "--ubatch-size", "2048" \
]

