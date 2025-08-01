# DeltaStudio Infrastructure Setup Guide

## Prerequisites

1. **SSH Access**: You need SSH access to your DigitalOcean droplet (deltastudio-server)
2. **Domain Setup**: Point your domain(s) to the server IP address
3. **GitHub Tokens**: Create a personal access token for cross-repository triggers

## Initial Setup Steps

### 1. Configure SSH Access

First, ensure you can SSH into your server:

```bash
# Add your SSH key to the server (from DigitalOcean console or recovery console)
ssh-copy-id root@YOUR_SERVER_IP

# Test connection
ssh root@YOUR_SERVER_IP
```

### 2. Update Server Configuration

Edit `ansible/inventories/production/hosts.yml`:
- Replace `ansible_host` with your actual server IP
- Update SSH key path if needed

### 3. Configure Your Websites

Edit `ansible/inventories/production/group_vars/all.yml`:
- Update website configurations
- Set proper domain names
- Configure Docker image paths
- Generate Netdata password hash: `htpasswd -nb admin YOUR_PASSWORD | cut -d: -f2`

### 4. Run Initial Server Setup

```bash
cd ansible
ansible-playbook playbooks/server-setup.yml
```

This will:
- Install Docker and Docker Compose
- Configure firewall
- Create necessary Docker networks
- Set up basic security

### 5. Deploy Infrastructure Services

```bash
# Deploy Caddy and Netdata
ansible-playbook playbooks/deploy-infrastructure.yml
```

### 6. Set Up GitHub Secrets

In your website repository (deltastudio-website), add these secrets:
- `INFRA_DEPLOY_TOKEN`: Personal access token with `repo` scope

In the infrastructure repository, set up:
- Deploy keys or SSH access for automated deployments

### 7. Deploy Your First Website

```bash
# Deploy specific website
ansible-playbook playbooks/deploy-website.yml -e "website_name=deltastudio"

# Or deploy all configured websites
ansible-playbook playbooks/deploy-all.yml
```

## Automated Deployment Flow

1. Push code to website repository's main branch
2. GitHub Actions builds Docker image and pushes to ghcr.io
3. Triggers infrastructure repository workflow
4. Infrastructure repo runs Ansible to deploy new version

## Manual Deployment

```bash
# From infrastructure repository
cd ansible
ansible-playbook playbooks/deploy-website.yml \
  -e "website_name=deltastudio" \
  -e "docker_tag=latest"
```

## Adding New Websites

1. Add website configuration to `group_vars/all.yml`
2. Create Dockerfile in the website repository
3. Set up GitHub Actions workflow
4. Run deployment playbook

## Monitoring

- Access Netdata at: https://monitoring.deltastudio.com
- Use credentials configured in `all.yml`

## Troubleshooting

### SSH Connection Issues
- Ensure your SSH key is added to the server
- Check firewall rules allow port 22
- Verify the correct IP address

### Docker Issues
```bash
# Check Docker status
ssh root@SERVER_IP "docker ps"
ssh root@SERVER_IP "docker logs CONTAINER_NAME"
```

### Caddy Issues
```bash
# Check Caddy logs
ssh root@SERVER_IP "docker logs caddy"
# Validate Caddyfile
ssh root@SERVER_IP "docker exec caddy caddy validate"
```

### Domain Not Working
1. Verify DNS points to server IP
2. Check Caddy is running
3. Ensure ports 80/443 are open
4. Check Caddy logs for certificate issues