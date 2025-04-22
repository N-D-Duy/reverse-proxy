# Caddy Web Server

Caddy is a powerful, enterprise-ready, open source web server with automatic HTTPS written in Go.

## Installation

### Method 1: Direct Download

1. Download the latest version from the [official website](https://caddyserver.com/download)
2. Extract the binary to a location in your PATH

### Method 2: Using Package Manager

#### For Debian/Ubuntu:
```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

#### For macOS:
```bash
brew install caddy
```

### Method 3: Using Docker
```bash
docker run -p 80:80 -p 443:443 -v $PWD/Caddyfile:/etc/caddy/Caddyfile -v caddy_data:/data caddy
```

## Configuration

Caddy uses a file called `Caddyfile` for its configuration. Below is an example Caddyfile with explanation:

```
<url> {
    # Email for Let's Encrypt certificate registration
    tls <email>

    # Handle WebSocket connections at /ws/* path
    handle /ws/* {
        reverse_proxy localhost:8888
    }

    # Handle all other HTTP requests
    handle {
        reverse_proxy localhost:8000
    }

    # Security headers
    header {
        # HSTS for enhanced security
        Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
        # Prevent MIME type sniffing
        X-Content-Type-Options "nosniff"
        # Prevent site from being embedded in iframes
        X-Frame-Options "DENY"
        # Content Security Policy
        Content-Security-Policy "default-src 'self'; style-src 'self'; img-src 'self' data: blob:; script-src 'self' 'unsafe-inline'; connect-src 'self' wss://<url>;"
    }
}
```

## Usage

1. Replace `<url>` with your domain (e.g., `example.com`)
2. Replace `<email>` with your email address for Let's Encrypt
3. Adjust the reverse proxy targets to match your application ports
4. Customize security headers as needed

### Starting Caddy

```bash
caddy run --config Caddyfile
```

### Running as a Service

#### On systemd-based systems:
```bash
sudo systemctl enable caddy
sudo systemctl start caddy
```

## Common Commands

- Validate configuration: `caddy validate`
- Reload configuration: `caddy reload`
- View running configuration: `caddy adapt`
- Stop Caddy: `caddy stop`

## Additional Resources

- [Official Documentation](https://caddyserver.com/docs/)
- [Caddy Community](https://caddy.community/)
- [GitHub Repository](https://github.com/caddyserver/caddy)
