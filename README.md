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

### 2. Install Kolla-Ansible

With the virtual environment active, install Kolla-Ansible from PyPI.

```bash
# Upgrade pip to the latest version
pip install --upgrade pip

# Install Kolla-Ansible for OpenStack Zed
pip install kolla-ansible==13.0.0
```

### 3. Clone the Kolla-Ansible Repository (Optional)

If you prefer, you can clone the Kolla-Ansible Git repository to work with the latest version of the code.

```bash
# Clone the Kolla-Ansible repository
git clone http://gitlab.ficld.ir/cloud/kolla-anisble-dpdk-integration.git
cd kolla-ansible
git checkout stable/zed
```

### 4. Install Dependencies

Kolla-Ansible requires a few additional dependencies. Install them by running

```bash
# Install additional dependencies required by Kolla-Ansible
pip install -r kolla-anisble-dpdk-integration/requirements.txt
```

### 5. Configure Kolla-Ansible

Now that Kolla-Ansible is installed, you need to configure the deployment.

 1. Copy the configuration files to your working directory.

 ```bash
 # Create necessary directories
 mkdir -p /etc/kolla

 # Copy configuration files
 cp -r kolla-ansible/etc/kolla/* /etc/kolla/

 ```

 2. Edit the globals.yml file to configure your OpenStack deployment (e.g., database, message queue, services).

 ```bash
 # Edit globals.yml to configure OpenStack services
 vi /etc/kolla/globals.yml

 ```

 Set the required configuration options, including:

  - **kolla_base_distro:** Your Linux distribution (e.g., ubuntu, centos).
  - **openstack_release:** Set this to zed (if not already set).
  - **network_interface:** Set this to your network interface.
  - **kolla_internal_vip_address:** Set the VIP address for your internal network.

### 6. Prepare the Servers
### 7. Deploy OpenStack
### 8. Access the OpenStack Dashboard
### 9. Clean Up
### Additional Notes
### END
