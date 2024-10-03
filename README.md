# JoeTaylor.me Docker Compose Setup

Welcome to the JoeTaylor.me Docker Compose repository! This setup orchestrates multiple services using Docker Compose, providing a robust and scalable environment for your applications. The primary services include Traefik (as a reverse proxy), Authentik (for authentication), n8n (workflow automation), and a static welcome page.

## Table of Contents

- [Project Overview](#project-overview)
- [Directory Structure](#directory-structure)
- [Prerequisites](#prerequisites)
- [Environment Configuration](#environment-configuration)
- [Setup Instructions](#setup-instructions)
- [Accessing Services](#accessing-services)
- [Managing Services](#managing-services)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## Project Overview

This Docker Compose setup manages the following services:

- **Traefik**: Acts as a reverse proxy and handles SSL termination using Let's Encrypt with Cloudflare DNS challenges.
- **Authentik**: Provides authentication and authorization services.
- **n8n**: Enables workflow automation and integration.
- **Welcome Home**: Serves a static welcome page via Nginx.

## Directory Structure

```
.
├── authentik
│   ├── certs
│   ├── custom-templates
│   └── media
│       └── public
├── docker-compose.yml
├── n8n
├── traefik
│   ├── letsencrypt
│   │   └── acme.json
│   └── traefik.yml
└── welcome_home
    └── index.html
```

- **authentik/**: Configuration and data for Authentik.
  - **certs/**: SSL certificates for Authentik.
  - **custom-templates/**: Custom templates for Authentik.
  - **media/public/**: Public media files for Authentik.
- **n8n/**: Configuration files for n8n (if any).
- **traefik/**: Configuration for Traefik.
  - **letsencrypt/acme.json**: Storage for Let's Encrypt certificates.
  - **traefik.yml**: Traefik configuration file.
- **welcome_home/**: Static files for the welcome page.
  - **index.html**: The welcome page served by Nginx.
- **docker-compose.yml**: Defines and configures all Docker services.

## Prerequisites

Before setting up the project, ensure you have the following installed:

- [Docker](https://docs.docker.com/get-docker/) (v20.10.0 or later)
- [Docker Compose](https://docs.docker.com/compose/install/) (v1.29.0 or later)
- A [Cloudflare](https://www.cloudflare.com/) account for DNS management
- Domain names configured in Cloudflare (e.g., `joetaylor.me` and subdomains)

## Environment Configuration

Create a `.env` file in the root directory with the following environment variables:

```env
# Cloudflare API Credentials
CLOUDFLARE_API_EMAIL=your-cloudflare-email@example.com
CLOUDFLARE_DNS_API_TOKEN=your-cloudflare-dns-api-token

# PostgreSQL Credentials for Authentik
PG_USER=authentik
PG_PASS=your_postgres_password
PG_DB=authentik

# Authentik Configuration
AUTHENTIK_SECRET_KEY=your_authentik_secret_key
AUTHENTIK_ERROR_REPORTING_ENABLED=true
```

**Important:** Replace placeholder values (e.g., `your-cloudflare-email@example.com`, `your_postgres_password`, `your_authentik_secret_key`) with your actual credentials.

### Generating a Secret Key for Authentik

You can generate a secure secret key for Authentik using Python:

```bash
python -c 'import secrets; print(secrets.token_urlsafe(32))'
```

## Setup Instructions

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/joetaylor-me-docker-compose.git
   cd joetaylor-me-docker-compose
   ```

2. **Create the `.env` File**

   As described in the [Environment Configuration](#environment-configuration) section, create a `.env` file with the necessary variables.

3. **Set Permissions for `acme.json`**

   Ensure the `acme.json` file has the correct permissions for Traefik to write certificates:

   ```bash
   touch traefik/letsencrypt/acme.json
   chmod 600 traefik/letsencrypt/acme.json
   ```

4. **Create Docker Network**

   Traefik is configured to use an external Docker network named `traefik_network`. Create it if it doesn't exist:

   ```bash
   docker network create traefik_network
   ```

5. **Deploy the Services**

   Start all services using Docker Compose:

   ```bash
   docker-compose up -d
   ```

   This command will download the necessary Docker images and start the containers in detached mode.

6. **Verify Deployment**

   Check the status of the containers:

   ```bash
   docker-compose ps
   ```

   Ensure all services are up and running without errors.

## Accessing Services

Once the services are up, you can access them via your configured subdomains:

- **Traefik Dashboard:** [https://traefik.joetaylor.me](https://traefik.joetaylor.me)
- **n8n Workflow Automation:** [https://n8n.joetaylor.me](https://n8n.joetaylor.me)
- **Welcome Home Page:** [https://home.joetaylor.me](https://home.joetaylor.me)
- **Authentik Authentication:** [https://authentik.joetaylor.me](https://authentik.joetaylor.me)

**Note:** It might take a few minutes for SSL certificates to be issued and for DNS changes to propagate.

## Managing Services

### Viewing Logs

To view logs for a specific service:

```bash
docker-compose logs -f <service_name>
```

For example, to view Traefik logs:

```bash
docker-compose logs -f traefik
```

### Restarting Services

To restart all services:

```bash
docker-compose restart
```

To restart a specific service:

```bash
docker-compose restart <service_name>
```

### Stopping Services

To stop all services:

```bash
docker-compose down
```

### Updating Services

To update Docker images and recreate containers:

```bash
docker-compose pull
docker-compose up -d
```

## Troubleshooting

- **Certificate Issues:** Ensure your Cloudflare DNS settings are correct and that the `acme.json` file has the appropriate permissions (`chmod 600`).
- **Service Connectivity:** Verify that all services are connected to the `traefik_network`. You can inspect the network with:

  ```bash
  docker network inspect traefik_network
  ```

- **Authentik Setup:** After the first deployment, navigate to the Authentik setup page to complete the initial configuration.

- **Port Conflicts:** Ensure that ports `80` and `443` are not used by other services on your host machine.

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request with your enhancements or bug fixes.

## License

This project is licensed under the [MIT License](LICENSE).

---

*This setup is maintained by Joe Taylor. For any questions or support, please contact [joe@example.com](mailto:joe@example.com).*