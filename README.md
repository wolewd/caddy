# Caddy with Docker Compose

[![Docker](https://img.shields.io/badge/Docker-✔-2496ED?logo=docker&logoColor=white)](https://www.docker.com/) [![Caddy](https://img.shields.io/badge/Caddy-✔-00CC88?logo=caddy&logoColor=white)](https://caddyserver.com/)

This repository provides a simple and production-friendly setup for **Caddy** as a reverse proxy.
It’s designed to be minimal yet flexible — ideal for hosting your personal website, portfolio projects, or Docker-based web services.

---

## Purpose

At first, I considered using Nginx or Traefik. However, both felt like overkill for small self-hosted projects and required more configuration effort.

Caddy, on the other hand, offers:
- Simple and declarative configuration
- Automatic HTTPS with Let’s Encrypt
- Excellent Docker integration
- Lightweight and easy to maintain, you can easily switch to another reverse proxy later if needed

That’s why I chose **Caddy** as my go-to reverse proxy for this use case.
---

## Prerequisites
- A domain
- A VPS or local machine running Linux (tested on Ubuntu & Arch Linux)  
- Docker and Docker Compose installed  
- A Docker network named `application_network`  

---

## Installation & Usage

1. Clone this repository:
   ```bash
   git clone https://github.com/wolewd/caddy.git
   cd caddy
   ```

2. Run the Caddy container:
    ```bash
    docker compose up -d
    ```

3. Access your site:
    ```bash
    https://your-domain.com
    ```
---

## Configuration 

#### Using Cloudflare Tunnel
By default, this setup is designed to work with Cloudflare Tunnel, so there’s no need to expose ports 80 or 443 publicly — Cloudflare handles secure routing.

#### Direct Public Access
If you want to expose your Caddy instance directly to the internet, you’ll need to make a few small adjustments:

1. Update your `docker-compose.yaml` to map Caddy’s HTTP and HTTPS ports to your host machine.
    Replace this section:
    ```yaml
    expose:
      - "80"
      - "443"
    ```
    With:
    ```yaml
    ports:
      - "80:80"
      - "443:443"
    ```

2. Update your `Caddyfile` to directly serve your domain:
    Replace your existing configuration with this:
    ```Caddy
    example.com {
        root * /srv
        file_server
        encode gzip

        header {
            X-Content-Type-Options "nosniff"
            X-Frame-Options "DENY"
            Strict-Transport-Security "max-age=31536000; includeSubDomains"
            Referrer-Policy "no-referrer-when-downgrade"
            Permissions-Policy "geolocation=(), microphone=(), camera=()"
            Content-Security-Policy "
                default-src 'self';
                script-src 'self';
                style-src 'self' 'unsafe-inline';
                img-src 'self' data:;
                font-src 'self';
            "
        }

        # Caddy will automatically obtain and manage HTTPS certificates via Let's Encrypt
    }
    ```
    Change `example.com` to your domain — Caddy will automatically handle SSL certificates for you!
---