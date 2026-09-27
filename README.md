# cPanel / WHM Deployment with Ansible

Ansible playbook for automated installation and configuration of **cPanel / WHM** on one or multiple servers.

This project is designed to simplify the cPanel deployment process, reduce manual configuration, and minimize human error by automating the installation and initial server configuration.

---

## Features

The playbook automates the following deployment and configuration tasks:

* **OS Prerequisites** — Prepare the operating system and install the required prerequisites.
* **cPanel / WHM Installation** — Download and install cPanel / WHM.
* **EasyApache Configuration** — Configure EasyApache and PHP versions.
* **PHP Configuration** — Copy and tune `php.ini` configuration.
* **CSF Firewall** — Install and configure ConfigServer Security & Firewall.
* **WHM Tweak Settings** — Configure WHM Tweak Settings.
* **Pure-FTPd** — Install and configure the Pure-FTPd FTP server.
* **MariaDB** — Configure and tune MariaDB.
* **Hostname & DNS** — Configure the server hostname and DNS zone.
* **cPanel Account Creation** — Create a cPanel account using server-specific variables.
* **SSH Configuration** — Configure SSH and change the SSH port.

---

## Project Structure

```text
cpanel/
├── host_vars/
├── roles/
├── ansible.cfg
├── hosts
├── Logs
├── playbook.yml
└── README.md
```

### Directory & File Description

| Path           | Description                        |
| -------------- | ---------------------------------- |
| `host_vars/`   | Server-specific variables          |
| `roles/`       | Ansible roles and task definitions |
| `ansible.cfg`  | Ansible configuration file         |
| `hosts`        | Ansible inventory file             |
| `Logs`        | Playbook execution logs            |
| `playbook.yml` | Main Ansible playbook              |
| `README.md`    | Project documentation              |

---

## Requirements

Before running the playbook, make sure you have:

* Ansible installed on the control machine
* SSH access to the target server
* Root or sufficient administrative privileges
* A server prepared for cPanel installation
* A Linux distribution supported by your cPanel version
* The required cPanel licensing available for the target server

> Make sure to check the current cPanel system requirements before deploying the playbook.

---

## Configuration

### Inventory

The target servers are defined in the `hosts` inventory file.

Example:

```ini
[cPanel]
192.168.1.1
```

Add your server IP address to the appropriate inventory group.

Make sure that SSH access from the Ansible control machine to the target server is working correctly before running the playbook.

---

## Host Variables

Server-specific configuration is stored in the `host_vars` directory.

The name of each file inside `host_vars` must match the IP address of the target server.

For example:

```text
host_vars/
└── 192.168.1.1
```

Example configuration:

```yaml
DOMAIN: example.com
IP: 192.168.1.1
USERNAME: cpaneluser
PASSWORD: "your-secure-password"
EMAIL: admin@example.com
```

### Available Variables

| Variable   | Description                                      | Example                |
| ---------- | ------------------------------------------------ | ---------------------- |
| `DOMAIN`   | Domain name used for the cPanel account          | `example.com`          |
| `IP`       | IP address of the target server                  | `192.168.1.1`          |
| `USERNAME` | Username for the cPanel account                  | `cpaneluser`           |
| `PASSWORD` | Password for the cPanel account                  | `your-secure-password` |
| `EMAIL`    | Email address associated with the cPanel account | `admin@example.com`    |

> **Important:** The filename inside `host_vars` must match the IP address of the target server.

For example:

```text
192.168.1.1
```

must be used as the filename for the server with IP address `192.168.1.1`.

---

## Usage

### 1. Clone the Repository

Clone the repository and enter the project directory:

```bash
git clone <repository-url>
cd cpanel
```

Replace `<repository-url>` with the URL of your GitHub repository.

---

### 2. Configure the Inventory

Edit the `hosts` file:

```bash
vim hosts
```

Add the target server:

```ini
[cPanel]
192.168.1.1
```

---

### 3. Configure Host Variables

Create or edit the corresponding file inside `host_vars`:

```bash
vim host_vars/192.168.1.1
```

Configure the required variables:

```yaml
DOMAIN: example.com
IP: 192.168.1.1
USERNAME: cpaneluser
PASSWORD: "your-secure-password"
EMAIL: admin@example.com
```

---

### 4. Run the Playbook

Run the complete deployment:

```bash
ansible-playbook playbook.yml
```

Or explicitly specify the inventory:

```bash
ansible-playbook -i hosts playbook.yml
```

The playbook will execute the configured tasks in sequence.

---

## Ansible Tags

The playbook provides tags that allow individual sections to be executed without running the entire deployment.

### Available Tags

| Tag                  | Task                         |
| -------------------- | ---------------------------- |
| `os`                 | Install OS prerequisites     |
| `installcpanel`      | Install cPanel / WHM         |
| `ea-apache`          | Configure EasyApache         |
| `phptune`            | Copy and tune `php.ini`      |
| `csftune`            | Install and configure CSF    |
| `tweaksetting`       | Configure WHM Tweak Settings |
| `ftp`                | Configure Pure-FTPd          |
| `database`           | Configure MariaDB            |
| `hostname-ns`        | Configure hostname and DNS   |
| `create-user-cpanel` | Create cPanel account        |
| `SSHconfig`          | Configure SSH                |

---

### Run a Specific Task

For example, to run only the cPanel installation:

```bash
ansible-playbook playbook.yml --tags installcpanel
```

To configure MariaDB:

```bash
ansible-playbook playbook.yml --tags database
```

To configure SSH:

```bash
ansible-playbook playbook.yml --tags SSHconfig
```

---

### Run Multiple Tasks

Multiple tags can be executed together:

```bash
ansible-playbook playbook.yml --tags "ea-apache,phptune"
```

---

## Execution Order

When the playbook is executed without tags, the main tasks are included in the following order:

```text
1. OS Prerequisites
        │
        ▼
2. Install cPanel
        │
        ▼
3. EasyApache AutoConfig
        │
        ▼
4. PHP Configuration
        │
        ▼
5. CSF Firewall
        │
        ▼
6. WHM Tweak Settings
        │
        ▼
7. Pure-FTPd
        │
        ▼
8. MariaDB
        │
        ▼
9. Hostname & DNS
        │
        ▼
10. Create cPanel Account
        │
        ▼
11. SSH Configuration
```

The tasks are included from the role's main task file.

---

## Deployment Tasks

### OS Prerequisites

The `os` task prepares the operating system for the cPanel installation.

```text
Tag: os
```

---

### cPanel Installation

Installs cPanel / WHM on the target server.

```text
Tag: installcpanel
```

---

### EasyApache

Configures EasyApache and the required PHP environment.

```text
Tag: ea-apache
```

---

### PHP Tuning

Copies and tunes the PHP configuration.

```text
Tag: phptune
```

---

### CSF Firewall

Installs and configures ConfigServer Security & Firewall.

```text
Tag: csftune
```

---

### WHM Tweak Settings

Applies the configured WHM Tweak Settings.

```text
Tag: tweaksetting
```

---

### Pure-FTPd

Installs and configures the Pure-FTPd FTP server.

```text
Tag: ftp
```

---

### MariaDB

Configures and tunes MariaDB.

```text
Tag: database
```

---

### Hostname & DNS

Configures the server hostname and DNS zone according to the server-specific variables.

```text
Tag: hostname-ns
```

---

### cPanel Account

Creates a cPanel account using the configuration defined in `host_vars`.

```text
Tag: create-user-cpanel
```

---

### SSH Configuration

Configures the SSH service and changes the SSH port.

```text
Tag: SSHconfig
```

---

## Logs

Playbook execution logs are stored in the `Logs` directory.

```text
Logs/
```

The logs can be used to review the execution process and troubleshoot issues that may occur during deployment.

---

## Important Notes

### Fresh Server

cPanel modifies a significant number of system-level services and configurations.

It is recommended to use a **fresh server** prepared specifically for the cPanel installation.

Do not run the playbook on a server containing existing production services unless you have verified that the deployment is safe for your environment.

---

### Firewall

The playbook includes CSF firewall configuration.

Make sure the required services and ports are properly configured before applying firewall changes.

---

### SSH Port

The playbook changes the SSH port.

Make sure the new SSH port is allowed by the firewall before applying the SSH configuration.

Otherwise, you may lose SSH access to the server.

---

### Credentials

The `host_vars` files contain sensitive information such as the cPanel account password.

**Do not commit production credentials to a public GitHub repository.**

For production environments, it is recommended to use **Ansible Vault** or another secure secrets-management solution.

For example:

```bash
ansible-vault encrypt host_vars/192.168.1.1
```

---

## Security Recommendations

For production deployments:

* Do not store plaintext production passwords in Git.
* Use Ansible Vault for sensitive variables.
* Use strong passwords for cPanel accounts.
* Verify firewall rules before changing the SSH port.
* Review the deployment logs after execution.
* Test the playbook in a staging environment before using it in production.
* Keep Ansible, cPanel, and the target operating system up to date.

---

## Author

Created in **2024** by **[Alireza Aghaei](https://github.com/ialirezaaghaei)**.

This project was developed to automate and simplify cPanel/WHM server deployment for **Personal / Open Source**.

---

## Contributing

Contributions, improvements, and bug reports are welcome.

If you find an issue or have an improvement for the playbook, feel free to open an issue or submit a pull request.

---

## Disclaimer

This playbook performs system-level configuration changes, including firewall and SSH configuration.

Always review the configuration and test the playbook in a non-production environment before deploying it to production servers.
