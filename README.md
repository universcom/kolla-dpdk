# OpenStack Zed Installation using Kolla-Ansible in a Virtual Environment

This guide will walk you through the steps to install OpenStack Zed version using Kolla-Ansible in a Python virtual environment.

## Prerequisites

1. **A machine (virtual or physical)** with a supported Linux distribution (e.g., Ubuntu 20.04, CentOS 8, or similar).
2. **Python 3.8+** installed.
3. **Ansible** installed.
4. **Root or sudo access** to the system.

## Steps

### 1. Set Up the Virtual Environment

First, create and activate a Python virtual environment to isolate the Kolla-Ansible installation and its dependencies.

```bash
# Install virtualenv if not already installed
sudo apt update
sudo apt install python3-virtualenv

# Create a virtual environment
mkdir ~/openstack-kolla
cd ~/openstack-kolla
python3 -m venv openstack-env

# Activate the virtual environment
source openstack-env/bin/activate
```
