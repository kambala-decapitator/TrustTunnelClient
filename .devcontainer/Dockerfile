FROM ubuntu:24.04

ARG TARGETARCH

# Install build dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    ninja-build \
    clang \
    clang-tools-18 \
    libc++-dev \
    libc++abi-dev \
    python3 \
    python3-pip \
    python3-venv \
    git \
    curl \
    lldb \
    && rm -rf /var/lib/apt/lists/*

RUN ln -sf /usr/bin/clang-scan-deps-18 /usr/local/bin/clang-scan-deps

# Install Rust
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- --default-toolchain 1.85 -y
ENV PATH="/root/.cargo/bin:${PATH}"

# Install Go
RUN case "${TARGETARCH}" in \
        amd64) GOARCH=amd64 ;; \
        arm64) GOARCH=arm64 ;; \
        *) echo "Unsupported TARGETARCH: ${TARGETARCH}" >&2; exit 1 ;; \
    esac \
    && curl -LO "https://go.dev/dl/go1.22.0.linux-${GOARCH}.tar.gz" \
    && tar -C /usr/local -xzf "go1.22.0.linux-${GOARCH}.tar.gz" \
    && rm "go1.22.0.linux-${GOARCH}.tar.gz"
ENV PATH="/usr/local/go/bin:${PATH}"

# Install Conan and Python dependencies
RUN python3 -m pip install conan --break-system-packages
COPY requirements.txt /tmp/requirements.txt
RUN python3 -m pip install -r /tmp/requirements.txt --break-system-packages --ignore-installed

WORKDIR /workspace

# Default command: start lldb-server with constrained port range
CMD ["lldb-server", "platform", "--server", "--listen", "*:12345", "--min-gdbserver-port", "12346", "--max-gdbserver-port", "12350"]
