# Use specific version for security and reproducibility
FROM python:3.13.5-slim-bookworm

# Security: Update system packages and remove unnecessary packages
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        ca-certificates && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Security: Create non-root user with specific UID/GID
RUN groupadd -r webapp -g 1000 && \
    useradd -r -g webapp -u 1000 -m -d /app -s /bin/bash webapp

# Set working directory
WORKDIR /app

# Security: Change ownership to webapp user
RUN chown -R webapp:webapp /app

# Copy requirements first for better caching
COPY --chown=webapp:webapp requirements.txt .

# Security: Upgrade pip and install packages as non-root user
USER webapp
RUN pip install --no-cache-dir --upgrade pip==25.1 && \
    pip install --no-cache-dir --user -r requirements.txt

# Add user's local bin to PATH
ENV PATH="/app/.local/bin:${PATH}"

# Copy application code
COPY --chown=webapp:webapp . .

# Security: Remove write permissions from application files
USER root
RUN chmod -R 555 /app && \
    chmod -R 755 /app/.local
USER webapp

# Security: Use non-root port
EXPOSE 8011

# Security: Add health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8011/ping')" || exit 1

# Security: Use exec form and specific user
CMD ["python", "-m", "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8011"]
