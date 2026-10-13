# Study container for rickiepark/llm-from-scratch. Same lock as the Mac (study-env/uv.lock); the GPU comes from the host driver (>= 570).
FROM python:3.13.7-slim-bookworm
COPY --from=ghcr.io/astral-sh/uv:0.12.3 /uv /uvx /bin/
RUN apt-get update && apt-get install -y --no-install-recommends git && rm -rf /var/lib/apt/lists/*
ARG UID=1000
RUN useradd -m -u ${UID} study
ENV UV_PROJECT_ENVIRONMENT=/opt/venv UV_PYTHON_DOWNLOADS=never UV_LINK_MODE=copy PATH=/opt/venv/bin:$PATH
RUN mkdir /opt/venv && chown study /opt/venv
USER study
WORKDIR /build
COPY --chown=study study-env/pyproject.toml study-env/uv.lock study-env/.python-version ./
RUN uv sync --frozen --no-install-project && uv cache clean
WORKDIR /work
CMD ["sh", "-c", "exec jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --ServerApp.root_dir=/work --IdentityProvider.token=\"$JUPYTER_TOKEN\""]
