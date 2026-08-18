## *`Cloud-1`*

`42 cursus compliant`

#### OVERVIEW

```text
An infrastructure automation project utilizing Ansible to deploy the Inception stack across cloud hosts.
The deployment is orchestrated via site.yml, which sequentially runs roles to provision Docker, configure a UFW firewall, and deploy the Nginx, WordPress, MariaDB, and phpMyAdmin containers.
```

#### INVENTORY and SECRETS

```text
- Targets droplets defined in inventory.yaml (droplet_1 and droplet_2), establishing connections as the root user using an SSH private key.
- Utilizes Ansible Vault (group_vars/cloud/vault.yml) to securely encrypt and inject the database root password into the environment.
- Requires specific Ansible collections defined in requirements.yml: community.docker (v5.2.1) and community.general (v13.2.0).
```

#### SYSTEM ROLES (FIREWALL and DOCKER)

```text
- firewall: Installs UFW, sets the default incoming traffic policy to deny, and explicitly allows incoming connections on ports 22, 80, and 443.
- docker: Purges old conflicting packages (docker-io, docker-compose), imports the official Docker GPG key, and securely installs docker-ce alongside the docker-compose-plugin.
```

#### DEPLOYMENT ROLES (STACK and SERVICES)

```text
- stack: Transfers the container source files to the target and securely renders the configuration into a .env file (mode 0600) using a Jinja2 template.
- database: Starts the MariaDB 11.8 container on the inception network and utilizes the assert module to verify the container's running state.
- wordpress: Starts the WordPress php8.3-fpm container, mounts the necessary volume, and asserts its running state.
- phpmyadmin: Starts the phpMyAdmin 5.2.3 container to manage the database.
- nginx: Starts the Nginx 1.28-alpine reverse proxy, configures TLSv1.2/TLSv1.3 SSL termination, and asserts its running state.
```

#### USAGE

```text
Navigate to the repository root and execute the setup commands:

$ ansible-galaxy collection install -r requirements.yml         # Installs the required community collections
$ ansible-playbook -i inventory.yaml site.yml --ask-vault-pass  # Executes the main playbook against the defined droplets
```
