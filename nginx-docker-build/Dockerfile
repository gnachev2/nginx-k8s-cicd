FROM nginx:latest
COPY index.html /usr/share/nginx/html
RUN apt-get update && apt-get install -y procps && rm -rf /var/lib/apt/lists/*
