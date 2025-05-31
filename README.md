# SELFHOST

This repository provides a streamlined Docker Compose setup for easily deploying and managing a collection of essential self-hosted services. With this configuration, you can quickly get your personal cloud infrastructure up and running, securely exposed to the internet via Cloudflare Tunnel.

## Services

This suite includes the following services, designed to work together for a robust self-hosting experience:

- **TinyProxy**: A simple HTTP/HTTPS proxy for basic network forwarding.
- **Cloudflared**: Establishes a secure, outbound-only connection to Cloudflare's network, allowing you to expose your local services to the internet without opening inbound firewall ports.
- **Nginx**: A high-performance web server and reverse proxy, used here to route incoming traffic to the correct internal services.
- **Backrest**: A flexible and powerful backup solution to ensure the safety of your data.
- **DUFS**: A straightforward file server for convenient uploading and downloading of files.
- **Aria2**: A versatile command-line download manager, integrated with AriaNg for a user-friendly web UI.
- **LLDAP**: A lightweight LDAP server for centralized user directory management.
- **Authelia**: An open-source authentication and authorization server that provides a single sign-on (SSO) portal and two-factor authentication (2FA) for your applications.
- **Vikunja**: An open-source, self-hostable to-do app that helps you organize your tasks and projects.

## Prerequisites

Before you begin, ensure you have one of the following setups installed on your system:

- Option 1: Docker (Recommended for ease of use)
- Option 2: Containerd with Nerdctl

## Setup Instructions

Follow these steps to get your services up and running.

### 1. Clone the Repository

First, clone this repository to your desired location on your system.

```sh
git clone https://github.com/azamaulanaaa/selfhost
cd selfhost
```

### 2. Create and Configure the .env File

The services rely on environment variables for configuration.

1. **Create the `.env` file**: Copy the `example.env` template to a new file named `.env` in the root of your repository:

   ```sh
   cp example.env .env
   ```

2. **Edit `.env` file**: Open the newly created `.env` file with a text editor. Carefully read and populate it with your specific values, paying close attention to the security-critical variables. The `example.env` file itself contains detailed comments, purpose descriptions, and examples for each variable, including how to generate necessary secrets. Refer directly to `example.env` for comprehensive guidance on each setting.

### 3. Deployment

Your `compose.yaml` file utilizes Docker Compose profiles, which allow you to selectively start groups of services. This is useful for managing different environments or only running specific parts of your application.

By default, when no profiles are specified, Docker Compose runs services that do not have a profiles key defined. In your setup, these services are:

- `tinyproxy`

There is also a dynamic profile named "enable". Services associated with this profile are dynamically included based on the ENABLE_{SERVICE_NAME} environment variables defined in your .env file. For example, if you set ENABLE_DUFS="1" in your .env file, the `dufs` service will be included when the "enable" profile is activated. This allows for flexible enablement of services with single profile.

The following services are associated with specific profiles and will only run when their respective profile (or a combination of profiles) is explicitly activated:

- `nginx`: For the Nginx reverse proxy.
- `cloudflared`: For the Cloudflared tunnel service.
- `backrest`: For the Backrest backup service.
- `dufs`: For the DUFS file server.
- `aria2`: For the Aria2 download manager.
- `lldap`: For the LLDAP server.
- `authelia`: For the Authelia authentication service.
- `vikunja`: For the Vikunja task management service.

To start services associated with a specific profile, use the --profile (or -p) option:

```sh
# Start only the Nginx service
docker compose -f compose.yaml --profile nginx up -d
```

```sh
# Start Backrest and DUFS services
docker compose -f compose.yaml --profile backrest --profile dufs up -d
```

You can also combine profiles with specific service names to start a subset of services within those profiles.

To start specific services (e.g., only Cloudflared and Nginx), regardless of profiles:

```sh
docker compose -f compose.yaml up -d cloudflared nginx
```

To stop all services:

```sh
docker compose -f compose.yaml down --remove-orphans
```

## Accessing Your Services

Once deployed, your services can be accessed in two primary ways, depending on your network configuration and whether you're exposing them publicly or internally:

- **Via Cloudflare Tunnel (Public Access)**: If you have enabled `cloudflared` and configured your domains in Cloudflare DNS to point to your Cloudflare Tunnel, your services will be securely accessible over the internet via the domains you configured in your `.env` file.

- **Via TinyProxy (Internal/Local Network Access)**: The `tinyproxy` service runs by default and can be used to access your services from within your local network or from machines that can reach your server directly (e.g., via VPN). You can configure your local devices to use `tinyproxy` as an HTTP/HTTPS proxy, allowing you to access your services using the same domains defined in your `.env` file, even without public Cloudflare Tunnel exposure.

Here are the domains for each service:

- DUFS: `https://${DUFS_DOMAIN}`
- Backrest: `https://${BACKREST_DOMAIN}`
- Aria2 (AriaNg UI): `https://${ARIA2_DOMAIN}`
- LLDAP: `https://${LLDAP_DOMAIN}`
- Authelia: `https://${AUTHELIA_DOMAIN}`
- Vikunja: `https://${VIKUNJA_DOMAIN}`
