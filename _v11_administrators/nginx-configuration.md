---
title: Setting up NGINX
tagline: A guide to configuring an NGINX to work with Materia
class: admin
category: first_time
---

# Production-Ready NGINX Configuration

The Materia NGINX image contains two configuration files by default:

- `nginx.conf` for common environment-agnostic configurations
- `sites-enabled/site.conf` for environment-specific configurations

We generally recommend replacing `site.conf` with your own custom NGINX configuration, if needed.


#### nginx.conf

``` nginx
# Number of worker processes - set to 1 for development, use 'auto' or number of CPU cores in production
worker_processes  auto;

# Error log location and level (debug, info, notice, warn, error, crit, alert, emerg)
error_log  /var/log/nginx/error.log warn;

# Process ID file location - using /tmp for non-root user compatibility
pid        /tmp/nginx.pid;


events {
    # Maximum number of simultaneous connections per worker process
    # Total max connections = worker_processes * worker_connections
    worker_connections  1024;
}


http {
    # Include file extension to MIME type mappings
    include       /etc/nginx/mime.types;
    
    # Default MIME type for files without a specific type
    default_type  application/octet-stream;

    # Custom log format including client IP, timestamp, request details, status, and proxy information
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # Access log location and format
    access_log  /var/log/nginx/access.log  main;

    # Performance settings
    # sendfile: Enables efficient file transfer using kernel sendfile() syscall
    sendfile        on;
    
    # tcp_nopush: Optimizes packet sending by waiting for full packets before transmission (works with sendfile)
    tcp_nopush      on;
    
    # tcp_nodelay: Disables Nagle's algorithm for low-latency responses (sends data immediately)
    tcp_nodelay     on;
    
    # Maximum time to keep idle connections open (in seconds)
    keepalive_timeout  65;

    # Global limits
    # Maximum size of client request body (file uploads, POST data)
    client_max_body_size 20M;
    
    # Buffer size for reading client request body - larger values reduce disk I/O
    client_body_buffer_size 32k;

    # Compression settings to reduce bandwidth and improve load times
    # Enable gzip compression
    gzip on;
    
    # Add "Vary: Accept-Encoding" header to prevent caching issues with compressed responses
    gzip_vary on;
    
    # Enable compression for all proxied requests
    gzip_proxied any;
    
    # Compression level (1-9): 6 is a good balance between CPU usage and compression ratio
    gzip_comp_level 6;
    
    # MIME types to compress (static assets, text, and data formats)
    gzip_types text/plain text/css text/xml text/javascript
               application/json application/javascript application/xml+rss
               application/x-javascript application/rss+xml
               font/truetype font/opentype application/vnd.ms-fontobject
               image/svg+xml;
    
    # Disable compression for IE6 due to known bugs
    gzip_disable "msie6";
    
    # Don't compress responses smaller than 1KB (overhead not worth it)
    gzip_min_length 1000;
    
    # Memory buffers for compression (16 buffers of 8KB each)
    gzip_buffers 16 8k;

    # Proxy buffer settings (global for all proxied requests)
    # Buffer size for reading the first part of response from proxied server (headers)
    proxy_buffer_size   16k;
    
    # Number and size of buffers for reading response from proxied server
    proxy_buffers   8 8k;
    
    # Maximum size of buffers that can be busy sending response to client
    proxy_busy_buffers_size   16k;

    # Include additional configuration files from conf.d directory
    include /etc/nginx/conf.d/*.conf;
}
```

#### sites-enabled/site.conf

``` nginx
# Main server block for production environment
server {
	# Listen on all interfaces on port 80 (HTTP)
	listen *:80;
	
	# Server name - catches all requests (default server)
	server_name default;

	# ===== SSL Configuration (when NOT behind a load balancer) =====
	# Uncomment the following section if nginx is directly handling SSL/TLS
	# and not behind a load balancer or reverse proxy
	#
	# # Listen on port 443 with SSL/TLS
	# listen *:443 ssl http2;
	# 
	# # SSL certificate and private key paths
	# ssl_certificate /etc/nginx/ssl/certificate.crt;
	# ssl_certificate_key /etc/nginx/ssl/private.key;
	# 
	# # SSL protocols - only allow TLS 1.2 and 1.3 for security
	# ssl_protocols TLSv1.2 TLSv1.3;
	# 
	# # SSL cipher suites - prioritize modern, secure ciphers
	# ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
	# 
	# # SSL session cache for performance - shared 10MB cache
	# ssl_session_cache shared:SSL:10m;
	# ssl_session_timeout 10m;

	# ===== End SSL Configuration =====

	# Root directory for serving static files
	root /var/www/html/staticfiles;
	
	# Default index file
	index index.html;

	# Security headers
	# HSTS: Force HTTPS for 1 year including all subdomains
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
	
	# Prevent MIME type sniffing (security hardening)
	add_header X-Content-Type-Options "nosniff" always;
	
	# Control referrer information sent with requests
	add_header Referrer-Policy "strict-origin-when-cross-origin" always;

	# Static files with aggressive caching
	location / {
		# Try to serve static file first, fallback to Django application
		try_files $uri @handler;
		
		# Cache static assets for 30 days
		expires 30d;
		
		# Mark as publicly cacheable and immutable (won't change)
		add_header Cache-Control "public, immutable";
	}

	# Named location for proxying requests to Django application
	location @handler {
		# Forward requests to Python/Django application server
		proxy_pass http://python:8001;

		# Preserve original host header
		proxy_set_header Host $host;
		
		# Pass real client IP address
		proxy_set_header X-Real-IP $remote_addr;
		
		# Chain of proxy IPs for load balancer compatibility
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		
		# Preserve original protocol (http/https)
		proxy_set_header X-Forwarded-Proto $scheme;

		# Timeout for reading response from proxied server (5 minutes)
		proxy_read_timeout    300;
		
		# Timeout for transmitting request to proxied server (5 minutes)
		proxy_send_timeout    300;
		
		# Timeout for establishing connection to proxied server (5 minutes)
		proxy_connect_timeout 300;

		# Don't rewrite redirects from the proxied server
		proxy_redirect off;
		
		# Hide version info for security
		proxy_hide_header X-Powered-By;
	}

	# Theme image handling with fallback
	# Automatically check to see if an image theme override exists, then fall back to default
	location ~/static/img/(.*)$ {
		# Try themed image first, fallback to default image
		try_files /theme/img/$1 /img/$1;
		
		# Cache images for 7 days
		expires 7d;
		
		# Mark as publicly cacheable
		add_header Cache-Control "public";
	}

	# Static assets directory
	location /static {
		# Map /static URL to staticfiles directory
		alias /var/www/html/staticfiles;
		
		# Cache static assets for 7 days
		expires 7d;
		
		# Mark as publicly cacheable
		add_header Cache-Control "public";
	}

	# JavaScript files with caching
	location /js {
		# Map /js URL to JavaScript directory
		alias /var/www/html/staticfiles/js;
		
		# Cache JavaScript files for 7 days
		expires 7d;
		
		# Mark as publicly cacheable
		add_header Cache-Control "public";
	}

	# Widget static assets - return 404 instead of Django error page
	# Prevent Django's 404 page from appearing on static assets
	# Firefox freaks out when it's expecting a .js file and gets Django's HTML instead
	location /widget/ {
		# Serve file if it exists, otherwise return proper 404 (not Django's HTML 404)
		try_files $uri =404;
	}

	# Health check endpoint for load balancers (ELB, ALB, etc.)
	# ELB health check endpoint (no logging to reduce noise)
	location /health/ {
		# Disable access logging for health checks to reduce log volume
		access_log off;
		
		# Set response content type
		add_header Content-Type text/plain;
		
		# Return HTTP 200 with 'ok' message
		return 200 'ok';
	}
}

```
