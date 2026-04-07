# webserver-ansibleplaybook

An Ansible playbook collection for automating the setup of a web server — installing and configuring **Nginx** and **Docker** on remote hosts, and serving a basic static site.

---

## What It Does

| Playbook               | Description                                                       |
|------------------------|-------------------------------------------------------------------|
| `webserverSetup.yaml`  | Main playbook — installs Nginx, installs Docker, configures Nginx virtual host, and deploys a static `index.html` |
| `install-apache2.yaml` | Standalone playbook to manage Apache2 (currently removes it and ensures the service is started) |

### `webserverSetup.yaml` — Step by Step

1. Installs Nginx (via `tasks/install-nginx.yaml`)
2. Installs Docker and Docker Compose (via `tasks/install-docker.yaml`)
3. Writes an Nginx virtual host config for `ujjwol.com` at `/etc/nginx/conf.d/ujjwol.conf`
4. Creates the web root directory `/var/www/html/ujjwol.com`
5. Deploys a basic `index.html` — serves `hello from ansible`

### `tasks/install-docker.yaml` — Docker Installation Steps

- Runs `apt update`
- Installs prerequisites: `ca-certificates`, `curl`, `gnupg`, `lsb-release`
- Creates the APT keyring directory
- Downloads and imports the official Docker GPG key
- Detects server architecture and adds the Docker APT repository
- Installs `docker-ce`, `docker-ce-cli`, `containerd.io`, and `docker-compose-plugin`
- Starts and enables the Docker service

---

## Project Structure

```
webserver-ansibleplaybook/
├── inventory               # Ansible inventory — defines the [webservers] group
├── host_vars/              # Host-specific variables (e.g. for ws1)
├── tasks/
│   ├── install-nginx.yaml  # Task file — Nginx installation
│   └── install-docker.yaml # Task file — Docker installation
├── webserverSetup.yaml     # Main playbook — full webserver setup
└── install-apache2.yaml    # Standalone playbook — Apache2 management
```

---

## Inventory

The inventory file defines a `[webservers]` group with a single host alias `ws1`:

```ini
[webservers]
ws1
```

The actual IP/hostname and SSH details for `ws1` are configured in `host_vars/ws1` or your SSH config.

---

## Prerequisites

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html) installed on the control machine
- SSH access to the target server(s) with sudo privileges
- Target hosts running Ubuntu (playbooks use `apt` package manager)

---

## Usage

### Run the Main Webserver Setup

Installs Nginx, Docker, and deploys the static site:

```bash
ansible-playbook -i inventory webserverSetup.yaml
```

### Run with a Specific User or Key

```bash
ansible-playbook -i inventory webserverSetup.yaml --user ubuntu --private-key ~/.ssh/your-key.pem
```

### Run the Apache2 Playbook

```bash
ansible-playbook -i inventory install-apache2.yaml
```

### Check Mode (Dry Run)

Preview what changes will be made without applying them:

```bash
ansible-playbook -i inventory webserverSetup.yaml --check
```

---

## Configuration

To target a different host or domain, update the following:

**`inventory`** — replace `ws1` with your server's IP or hostname:
```ini
[webservers]
your-server-ip
```

**`webserverSetup.yaml`** — update the `server_name` and directory paths to match your domain:
```yaml
server_name your-domain.com;
root /var/www/html/your-domain.com;
```

Host-specific variables (SSH user, port, private key path) can be set in `host_vars/ws1`.

---

## License

This project is open source. See the [LICENSE](LICENSE) file for details.
