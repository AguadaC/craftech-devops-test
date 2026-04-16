FROM python:3.10-slim

# Use /app as the working directory inside the container.

WORKDIR /app

# Avoid writing .pyc files and force logs to be unbuffered (better for Docker).
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Install system deps:
# - build-essential + libpq-dev: required to build/install psycopg2
# - netcat-openbsd: used by entrypoint.sh to wait for Postgres (nc -z)
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       build-essential \
       libpq-dev \
       netcat-openbsd \
    && rm -rf /var/lib/apt/lists/*

# Copy only requirements first to leverage Docker layer caching.
COPY requirements.txt ./

# Install Python dependencies.
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the backend source code.
COPY . ./

# Make sure the entrypoint script is executable.
RUN chmod +x /app/entrypoint.sh

# Document the port used by Django.
EXPOSE 8000

# Run the entrypoint script when the container starts.
ENTRYPOINT ["/app/entrypoint.sh"]
