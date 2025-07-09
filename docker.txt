# Use official Python base image with Rust installed
FROM python:3.11-slim

# Install Rust and build dependencies
RUN apt-get update && \
    apt-get install -y curl build-essential gcc && \
    curl https://sh.rustup.rs -sSf | sh -s -- -y && \
    export PATH="/root/.cargo/bin:$PATH"

# Set working directory
WORKDIR /app

# Copy project files
COPY . .

# Install Python dependencies
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Collect static files
RUN python manage.py collectstatic --noinput

# Run migrations
RUN python manage.py migrate

# Expose port 8000
EXPOSE 8000

# Start Gunicorn server
CMD ["gunicorn", "codeai.wsgi:application", "--bind", "0.0.0.0:8000"]
