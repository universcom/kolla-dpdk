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
sudo apt install python3-venv git python3-dev libffi-dev gcc libssl-dev

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

# Install Ansible for OpenStack Zed
pip install 'ansible>=4,<6'
```

### 3. Clone the Kolla-Ansible Repository

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
pip install kolla-anisble-dpdk-integration/.
```

### 5. Configure Kolla-Ansible

Now that Kolla-Ansible is installed, you need to configure the deployment.

 1. Copy the configuration files to your working directory.

 ```bash
 # Create necessary directories
 mkdir -p /etc/kolla

 # Copy configuration files
 cp -r /path/to/venv/share/kolla-anisble-dpdk-integration/etc/kolla/* /etc/kolla/

 ```

 2. Copy Inventory file

 ```bash
 cp /path/to/venv/share/kolla-anisble-dpdk-integration/ansible/inventory/* .

 ``` 

 3. Generate password

 ```bash
 cd kolla-ansible/tools
 ./generate_passwords.py
```

 4. Edit the globals.yml file to configure your OpenStack deployment (e.g., database, message queue, services).

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

Run the ***bootstrap-servers*** command to prepare the servers for deployment. This step is necessary to configure the servers, including setting up SSH keys and necessary prerequisites.

```bash
# Bootstrap the servers
kolla-ansible -i <your_inventory_file> bootstrap-servers
kolla-ansible -i <your_inventory_file> prechecks
```

### 7. Deploy OpenStack

Once the servers are prepared, you can deploy OpenStack.

```bash
# Deploy OpenStack
kolla-ansible -i <your_inventory_file> deploy
```

### 8. Access the OpenStack Dashboard

After the deployment, the OpenStack services should be running. You can access the OpenStack Horizon dashboard by navigating to:

```bash
http://<your_vip_address>/dashboard
```

### 9. Destroy

If you want to completely remove containers, networks, and volumes, run:

```bash
kolla-ansible -i <your_inventory_file> --configdir <configs dir> --passwords <password file path> destroy --yes-i-really-really-mean-it --limit <node>
```

The destroy command will:

- Stop and remove the OpenStack containers
- Remove all networks and volumes used by the containers

### 10. Using Openstack client

1. Install the OpenStack CLI client:

```bash
pip install python-openstackclient -c https://releases.openstack.org/constraints/upper/zed
```

2. OpenStack requires a clouds.yaml or adminrc file where credentials for the admin user are set. To generate this file:

```bash
kolla-ansible post-deploy
```

### 11. Enable DPDK

To enable dpdk on the specific host take effect the following changes in the globals.yml file

```yaml
####################
# OVS-DPDK options
####################
neutron_external_interface: "ens192,ens224" #usecase on normal compute only
neutron_bridge_name: "br-ex,br-ex2"
dpdk_bridge_name: "br_dpdk"
ovs_dpdk_bond_interfaces: "ens2f0,ens2f1"
huge_pages: "350"
huge_page_size: "1G"
dpdk_interface_driver: "uio_pci_generic"
dpdk_interface_mtu: "9100"
enable_ovs_dpdk: "yes"
#the nic that will stanblish geneve tunnel
arbitry_dpdk_tunnel_interface_name: "ovs-eth200"
bonding_tunnel_interface_vlan_tag: "81"
#configuration will apply on patch-ports (uplinks from br-exx to br_dpdk)
dpdk_br_ex_type_mapping:  #values in [tag,trunks]
   br-ex: tag
   br-ex2: trunks
dpdk_br_ex_vni_mapping:
   br-ex:
    - "86"
   br-ex2:
    - "80-85"
    - "144"
```

In the inventory file add node under [ovs-dpdk] section. following the exaple: 

```bash
[ovs-dpdk]
cmp33-ddk   dpdk_tunnel_interface_CIDR=192.168.0.119/24
```

Then run the following command: 

```bash
kolla-ansible -i <your_inventory_file> --configdir <configs dir> --passwords <password file path> reconfigure -t ovs-dpdk --limit <node>
```

## Additional Notes

- ***Inventory File:*** Ensure your inventory file (<your_inventory_file>) is correctly configured with the correct IPs for your OpenStack nodes. You can refer to the Kolla-Ansible documentation for more details on how to set up your inventory file.
- ***Kolla-Ansible Configuration:*** Depending on your OpenStack environment, you may need to adjust additional settings in the Kolla-Ansible configuration files. Refer to the Kolla documentation for advanced configuration options.
- ***Troubleshooting:*** If you encounter issues during deployment, you can use the -v flag to run Kolla-Ansible in verbose mode for more detailed output.
