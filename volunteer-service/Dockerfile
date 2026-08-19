FROM python:3.12.14-alpine3.24
 
# Prevent Python from writing .pyc files
ENV PYTHONDONTWRITEBYTECODE=1
 
# Prevent Python from buffering stdout/stderr
ENV PYTHONUNBUFFERED=1
 
# Set work directory
WORKDIR /app
 
# Install system dependencies (needed for psycopg2 and others)
# NOTE: this is an Alpine image, so apk is used instead of apt-get
RUN apk add --no-cache \
    build-base \
    postgresql-dev \
    curl
 
# Copy dependency file first (better Docker cache usage)
COPY app/requirements.txt .
 
# Install Python dependencies
RUN pip install --upgrade pip "setuptools<82" && \
    pip install --no-cache-dir -r requirements.txt
 
# Copy project files
COPY app/ .
 
# Expose application port
EXPOSE 8083
 
# Start application with gunicorn
CMD ["sh", "-c", "gunicorn --bind 0.0.0.0:${PORT} app:app"]