# syntax=docker/dockerfile:1

# Stage 1: Base image with uv
FROM python:3.13-slim AS base

# Install uv for fast dependency management
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Set working directory
WORKDIR /app

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    UV_SYSTEM_PYTHON=1

# Stage 2: Development
FROM base AS development

# Copy dependency files
COPY pyproject.toml ./

# Install dependencies including dev dependencies
RUN uv pip install -e .

# Copy application code
COPY . .

# Expose port
EXPOSE 8080

# Run Django development server
CMD ["python", "manage.py", "runserver", "0.0.0.0:8080"]

# Stage 3: Production
FROM base AS production

# Copy dependency files
COPY pyproject.toml ./

# Install production dependencies only
RUN uv pip install --no-dev .

# Copy application code
COPY . .

# Collect static files
RUN python manage.py collectstatic --noinput || true

# Create non-root user for security
RUN useradd -m -u 1000 django && \
    chown -R django:django /app

USER django

# Expose port
EXPOSE 8080

# Run with Gunicorn
CMD ["gunicorn", "--bind", "0.0.0.0:8080", "--workers", "4", "--threads", "2", "core.wsgi:application"]
