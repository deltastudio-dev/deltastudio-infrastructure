# DeltaStudio Infrastructure

This repository manages the infrastructure and deployment configuration for all DeltaStudio websites hosted on the deltastudio-server.

## Structure

```
├── ansible/          # Ansible playbooks and roles
├── docker/           # Docker compose files for production
├── scripts/          # Utility scripts
└── docs/            # Documentation
```

## Managed Websites

- deltastudio-website (deltastudio.com)
- [Add more websites as they are deployed]

## Quick Start

1. Set up your inventory in `ansible/inventories/production/hosts.yml`
2. Configure your websites in `ansible/inventories/production/group_vars/all.yml`
3. Run the initial server setup: `ansible-playbook ansible/playbooks/server-setup.yml`
4. Deploy all websites: `ansible-playbook ansible/playbooks/site.yml`

## Requirements

- Ansible 2.9+
- SSH access to deltastudio-server
- Docker and Docker Compose on the target server