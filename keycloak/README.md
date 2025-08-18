# 🔐 Keycloak Identity & Access Management (IAM) with Docker Compose

A secure, production-ready setup of **Keycloak** using Docker Compose with PostgreSQL for persistent storage. This configuration is ideal for development, testing, and small-scale production environments.

🔐 **Single Sign-On (SSO)**, **OAuth 2.0**, **OpenID Connect**, and **user management** made easy.

🌐 **Access Admin Console:** [http://localhost:8080/admin](http://localhost:8080/admin)

---

## 🚀 Features

- ✅ **Production-ready** configuration using `start` mode (not `start-dev`)
- 🛢️ **Persistent data** with PostgreSQL database
- 💾 Volumes for both Keycloak and PostgreSQL to survive container restarts
- 🔐 Secure admin credentials (configurable)
- 🔄 Auto-restart and health checks
- 🧪 Easy to extend for HTTPS, clustering, or reverse proxy
- 🐳 Built with Docker Compose – simple and portable

---

## 📦 Prerequisites

Before you begin, ensure you have:

- [Docker](https://docs.docker.com/engine/install/) installed
- [Docker Compose](https://docs.docker.com/compose/install/) installed
- At least 2 GB of free RAM (Keycloak is memory-intensive)

---

## 🛠️ Configuration Overview

| Component       | Value                                |
|----------------|--------------------------------------|
| Keycloak Image  | `quay.io/keycloak/keycloak:26.2.5`   |
| Admin Username  | `admin`                              |
| Admin Password  | `ChangeMe123!` *(change after first login)* |
| Database        | PostgreSQL 15                        |
| Ports           | `8080:8080` (HTTP)                   |
| Mode            | `start` (production mode)            |
| Health Checks   | Enabled for both services            |

> ⚠️ **Never use default passwords in production!**

---

## 🚦 Quick Start

### 1. Clone or create a project directory

```bash
mkdir keycloak-docker && cd keycloak-docker
```

### 2. Save the following as `docker-compose.yml`

```yaml
version: '3.8'

services:
  keycloak:
    image: quay.io/keycloak/keycloak:26.2.5
    container_name: keycloak
    ports:
      - "8080:8080"
    environment:
      KC_ADMIN: admin
      KC_ADMIN_PASSWORD: ChangeMe123!
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://db:5432/keycloak
      KC_DB_USERNAME: keycloak
      KC_DB_PASSWORD: keycloak_password
      KC_HOSTNAME: localhost
      KC_HOSTNAME_PORT: 8080
      KC_HTTP_ENABLED: 'true'
    command: start
    volumes:
      - keycloak-
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  db:
    image: postgres:15
    container_name: keycloak-db
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: keycloak
      POSTGRES_PASSWORD: keycloak_password
    volumes:
      - db-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U keycloak"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  keycloak-
  db-data:
```

### 3. Start the services

```bash
docker-compose up -d
```

> ⏳ First startup may take 1–2 minutes while the database initializes.

### 4. Access the Admin Console

Open in your browser:

👉 [http://localhost:8080/admin](http://localhost:8080/admin)

Log in with:
- **Username:** `admin`
- **Password:** `ChangeMe123!`

---

## 🔐 Security Recommendations

After first login, **immediately**:

1. **Change the admin password** in the Keycloak admin panel.
2. **Use strong passwords** (minimum 12 characters, mix of letters, numbers, symbols).
3. **Enable 2FA (Two-Factor Authentication)** for admin users.
4. **Create a separate realm** for your application (don’t use `master`).
5. **Never expose port 8080 publicly without HTTPS and a reverse proxy.**

---

## 🧩 What Can You Do With Keycloak?

Keycloak is a powerful **Identity and Access Management (IAM)** solution. You can use it to:

### 🔑 Authentication & Authorization
- Centralized login for multiple applications
- Single Sign-On (SSO) across services
- OAuth 2.0 and OpenID Connect (OIDC) support

### 👥 User Management
- Create, manage, and disable users
- Assign roles and groups
- Self-registration and password reset

### 🌐 Social Login
- Enable login via Google, GitHub, Facebook, etc.
- Identity Brokering with external IdPs

### 📱 Protect APIs
- Secure REST APIs with JWT tokens
- Integrate with Spring Boot, Node.js, React, etc.

### 🏢 Multi-Tenancy
- Use **Realms** to isolate organizations or environments
- Customize themes and login pages per realm

---

## 🛠️ Customization Guide

### Change Admin Credentials

Modify these environment variables in `docker-compose.yml`:

```yaml
environment:
  KC_ADMIN: your-username
  KC_ADMIN_PASSWORD: YourStrongPassword123!
```

> 🔁 Restart the container after changes:
>
> ```bash
> docker-compose down && docker-compose up -d
> ```

### Add Your Application as a Client

1. Go to **Clients** → **Create Client**
2. Enter client ID (e.g., `my-web-app`)
3. Set **Client Type** to `OpenID Connect`
4. Configure redirect URLs (e.g., `http://localhost:3000/*`)
5. Note the **Client Secret** for your app

---

## 🔄 Updating Keycloak

To upgrade to a newer version:

```bash
# Pull the latest image
docker-compose pull

# Recreate containers
docker-compose up -d
```

> ⚠️ Always backup your database before upgrading.

---

## 💾 Backup & Restore

### Backup PostgreSQL Database

```bash
docker exec keycloak-db pg_dump -U keycloak -d keycloak > keycloak-backup.sql
```

### Restore Database

```bash
cat keycloak-backup.sql | docker exec -i keycloak-db psql -U keycloak -d keycloak
```

---

## 🌐 Going to Production?

For production use, add:

- 🔒 **HTTPS with SSL/TLS** (using Nginx, Traefik, or Caddy)
- 🌐 **Reverse Proxy** (for domain routing, e.g., `auth.yourdomain.com`)
- 📊 **Monitoring & Logging** (Prometheus, Grafana, ELK)
- 🔄 **High Availability** (multi-node cluster with shared database)
- 🧰 **CI/CD for Realm Configuration** (use `kc.sh export` and import)

---

## 📚 Learn More

- 📖 [Keycloak Official Documentation](https://www.keycloak.org/documentation)
- 💡 [Keycloak Docker Image Guide](https://www.keycloak.org/server/containers)
- 🎥 [YouTube: Keycloak Tutorials](https://www.youtube.com/results?search_query=keycloak+tutorial)
- 🐱 [GitHub: Keycloak Examples](https://github.com/keycloak/keycloak-quickstarts)

---

## 🙋‍♂️ Troubleshooting

| Issue | Solution |
|------|---------|
| `Port 8080 already in use` | Stop conflicting service or change port |
| `Database connection failed` | Ensure `db` service is healthy |
| `Health check failing` | Wait longer; Keycloak takes time to start |
| `Invalid user credentials` | Double-check `KC_ADMIN_PASSWORD` |

Check logs:
```bash
docker logs keycloak
docker logs keycloak-db
```

---

## 🙌 Feedback & Contributions

Found a bug? Want to improve this setup?  
Open an issue or submit a PR on GitHub!

---

🔐 Made with ❤️ for developers, DevOps engineers, and self-hosters.  
Secure your apps. Simplify authentication.
```

---