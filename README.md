# Ansible Playbook for acme.sh and Fail2ban

This repository contains Ansible playbooks for installing and configuring **nginx with brotli** and custom modules as webserver, **acme.sh** for managing SSL certificates and **Fail2ban** for securing server services.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Usage](#usage)
4. [Variables](#variables)
5. [Example Configuration](#example-configuration)
6. [License](#license)

## Prerequisites

- Ansible (version 2.9 or higher) installed
- Access to the target server with root privileges
- Internet connection for package installation and certificate registration

## Installation

1. Clone this repository onto your server:

   ```bash
   git clone https://github.com/NoirPi/nginx-brotli-ssl-ansible.git
   cd acme-fail2ban-playbook
   ```

## Usage

To run the playbook, use the following command:

```bash
ansible-playbook setup.yml -i inventory.ini --ask-become-pass
```

- `main.yml` is the main playbook that controls the installation and configuration of **acme.sh** and **Fail2ban**.
- `inventory.yml` contains the target hosts for execution.

## Variables

The variables for the configuration are bundled in the `variables.yml` file. Here are the key variables you can customize:


| Variable                      | Description                                                                                                      | Default Value      |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------- | ------------------ |
| `nginx_server_header`         | Custom NGINX Server Header                                                                                       | nginx              |
| `nginx_mainline_version`      | Installs the mainline version from the NGINX repos instead of the release version (mainline is current)          | `true`             |
| `nginx_remove_mail_modules`   | Configures NGINX without the flags: [`--with-mail`, `--with-mail_ssl_module`]                                    | `false`            |
| `nginx_remove_stream_modules` | Configures NGINX without the flags: [`--with-stream`, `--with-stream_realip_module`, `--with-stream_ssl_module`] | `false`            |
| `acme_sh_path`                | Installation path for ACME.sh.                                                                                   | `/usr/local/sbin`  |
| `cloudflare_api_key`          | API key for Cloudflare DNS updates. [Zone:read, DNS:write]                                                       | -                  |
| `domains`                     | List of domains to manage.                                                                                       | [ ]                |
| `wildcard`                    | Enables wildcard certificates when set to`true`.                                                                 | `false`            |
| `generate_domain_configs`     | If`true`, generates a separate NGINX configuration for each domain.                                              | `false`            |
| `cert_path`                   | Path where generated certificates are stored.                                                                    | `/etc/ssl/private` |
| `acme_sh_dry_run`             | Should a dry run of ACME be performed? (Prevents rate limits from Let's Encrypt)                                 | `true`             |
| `account_email`               | Email address for the Let's Encrypt account                                                                      | -                  |

## Example Configuration

To use an example configuration, create the `variables.yml` file in the root directory of the repository and add the required variables.>
You can also create an example inventory file (`inventory.yml`) that contains the target hosts.

### Example for `inventory.yml`:

```yaml
all:
  hosts:
    your-server:
      ansible_host: YOUR_SERVER_IP
      ansible_user: YOUR_SSH_USER
```

### Example for `setup.yml`:

The `setup.yml` playbook looks like this:

```yaml
- hosts: all
  become: yes
  vars_files:
    - variables.yml
  tasks:
    - include_tasks: acme.sh/tasks/main.yml
    - include_tasks: fail2ban/tasks/main.yml
    - include_tasks: nginx/tasks/main.yml
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
