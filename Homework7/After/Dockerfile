FROM python:3.13-alpine

# Use non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Install only essential packages & verify integrity if needed
RUN apk --no-cache add curl

# Set working directory
WORKDIR /app

# Copy files with minimal layers
COPY requirements.txt .

# Install dependencies as root before dropping privileges
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Drop to non-root user
USER appuser

# Define healthcheck for runtime monitoring
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost:5000/ || exit 1

# Use exec form to avoid shell interpretation
CMD ["python", "app.py"]


