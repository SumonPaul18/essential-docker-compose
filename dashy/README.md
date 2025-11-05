# 🖥️ Dashy Dashboard with Docker

A simple, beautiful, and customizable homepage for your server, built with [Dashy](https://github.com/Lissy93/dashy).  
This setup uses Docker Compose for easy deployment, persistent configuration, and automatic restarts.

🎯 **Access your dashboard at:** [http://localhost:8085](http://localhost:8085)

---

## ✅ Features

- 🔧 Easy configuration via `dashy-conf/conf.yml`
- 💾 Persistent data — settings survive container restarts
- 🐳 Dockerized with `docker-compose` for hassle-free setup
- 🔄 Auto-restart and health checks
- 📁 Simple folder structure for config and logs
- 🚀 Ready for production use

---

## 📦 Prerequisites

- [Docker](https://docs.docker.com/engine/install/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) installed

---

## 🚀 Quick Start

1. **Clone or create project directory:**

```bash
mkdir dashy-dashboard && cd dashy-dashboard
```

2. **Create the folder structure:**

```bash
mkdir -p dashy-conf dashy-logs
```

3. **Create a default config file:**

```bash
cat > dashy-conf/conf.yml << 'EOF'
appConfig:
  theme: dark
  layout: auto
  hideHeader: false

services:
  - title: Example
    description: My first service
    url: https://example.com
    icon: icon-park-outline:website
EOF
```

4. **Save the following as `docker-compose.yml`:**

```yaml
version: "3.8"
services:
  dashy:
    container_name: dashy
    image: lissy93/dashy:latest
    ports:
      - "8085:8080"
    volumes:
      - ./dashy-conf:/app/public/conf
      - ./dashy-logs:/app/logs
    environment:
      - NODE_ENV=production
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "node", "/app/services/healthcheck"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

5. **Start the container:**

```bash
docker-compose up -d
```

6. **Open in your browser:**

👉 [http://localhost:8085](http://localhost:8085)

---

## 🛠️ Customize Your Dashboard

Edit the configuration file:

```bash
nano dashy-conf/conf.yml
```

> 💡 No restart needed! Just save and refresh your browser.

📌 Learn how to configure Dashy:  
👉 [Official Dashy Docs - Configuration](https://dashy.to/docs/config)

---

## 🗂️ Directory Structure

```
dashy-dashboard/
├── docker-compose.yml     # Docker setup
├── dashy-conf/            # Your config (persists)
│   └── conf.yml           # Main config file
└── dashy-logs/            # Logs from Dashy (optional)
```

---

## 🔄 Updating Dashy

To update to the latest version:

```bash
docker-compose pull
docker-compose up -d
```

---

## 🛑 Stop the Service

```bash
docker-compose down
```

---

## 🌐 Optional: Secure with HTTPS (Advanced)

To expose securely over the internet, use a reverse proxy like:
- [Nginx Proxy Manager](https://nginxproxymanager.com/)
- [Traefik](https://traefik.io/)
- Caddy or Nginx with Let's Encrypt

---

## 📚 Learn More

- 🚀 Dashy GitHub: [https://github.com/Lissy93/dashy](https://github.com/Lissy93/dashy)
- 📖 Dashy Docs: [https://dashy.to/docs](https://dashy.to/docs)

---

## 🙌 Feedback & Issues

Found a bug or want a feature? Open an issue or contribute on GitHub!

---

Made with ❤️ for self-hosters and tinkerers.

---
