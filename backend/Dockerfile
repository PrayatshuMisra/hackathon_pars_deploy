# Stage 1: Builder
FROM python:3.11-slim AS builder

WORKDIR /app

# Install build dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip
RUN pip install --upgrade pip

# Copy requirements
COPY requirements.txt .

# Install dependencies (CPU versions where possible)
# PyTorch CPU
RUN pip install --no-cache-dir torch --index-url https://download.pytorch.org/whl/cpu
# TensorFlow CPU (Much smaller than standard tensorflow)
RUN pip install --no-cache-dir tensorflow-cpu
RUN pip install --no-cache-dir -r requirements.txt


# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app

# Install runtime system dependencies (ffmpeg is needed for audio)
RUN apt-get update && apt-get install -y \
    ffmpeg \
    && rm -rf /var/lib/apt/lists/*

# Copy installed python packages from builder
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY --from=builder /usr/local/bin /usr/local/bin

# Copy application code
COPY . .

# Expose port 8000
EXPOSE 8000

# Run the application
# Use shell form to allow variable expansion for $PORT
CMD uvicorn main:app --host 0.0.0.0 --port ${PORT:-8000}
