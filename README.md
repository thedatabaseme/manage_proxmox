Manage_Proxmox
=========

This Role enables you to create and delete Containers and VMs within a Proxmox Cluster.

Requirements
------------

- Ansible 2.7
- proxmoxer Module
- requests Module

Both Modules can be installed using the Python Package Manager. pip3 may need to be installed separately.

    pip3 install proxmoxer requests

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

- proxmox_api_user (Default root@pam): Proxmox API Username
- proxmox_api_password (Default Start123): Password of the API User
- proxmox_api_host (Default pvehost1): Hostname of the Proxmox Server
- container_id (Default NULL): Container ID within Proxmox. If not set, it will be calculated by Proxmox
- container_password (Default Start123): Passwort of the root User of the Container
- container_name (Default NULL): Name of the Container you want to create
- container_storage: Proxmox Datacenter Storage where the Container will be created in
- container_description (Default NULL): An optional Description of the Container
- container_os_template (Default NULL): Location and Template Name. E.G. "share:vztmpl/ubuntu-18.04-standard_18.04.1-1_amd64.tar.gz"
- container_start_on_boot (Default false): Controls if the container is started when the Proxmox Server is booted
- container_cores (Default 1): Count of Cores the Container will have assigned
- container_memory (Default 512): Amount of Memory in MB the Container will have assigned
- container_swap (Default 512): Amount of Swap Space the Container will have assigned
- action_timeout (Default 120): Timeout of the Creation or Deletion Action
- start_after_creation (Default true): Controls if the Container will be started after the Creation 

This Role also provides the possibility to add an SSH public key to the authorized keys on the Container. Therefore you need to place your public key(s) inside an id_rsa.pub file under templates and run the Playbook with the ssh_public_key_from_file TAG. (See Example below)

Dependencies
------------

Be aware, that this role will run at the localhost (Ansible Controlhost) by default. When you want to run it on another Node, both proxmoxer and requests Modules are needed to be installed there.
This will be made by a pip installation call in the precheck.yml. You may need to set an appropriate Python Interpreter with ansible_python_interpreter in your Inventory.
Also be aware, this role works with TAGS. So you need to specify on the Playbook Call, if you want to create or delete a Container. See example below.

Example Playbook
----------------

There is an example Playbook included manage_proxmox.yml

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: localhost
      vars:
        proxmox_api_host: "pvehost1"
        proxmox_api_user: "root@pam"
        proxmox_api_password: "Start123"
      roles:
         - role: manage_proxmox

An example Playbook Call looks like this. Ofcourse you may want to specify the Variables within your Playbook or within your Inventory:

    - ansible-playbook manage_container.yml --tags "create" -e "container_password=Start123 container_node=srvoffice2 container_name=ctTest container_os_template=Share:vztmpl/ubuntu-18.04-standard_18.04.1-1_amd64.tar.gz container_start_on_boot=true start_after_creation=true container_storage=vm-single container_cores=2 container_memory=1024" --tags "ssh_public_key_from_file" -k -K -u <username>
    
or this if you want to delete a Container:

    - ansible-playbook manage_container.yml --tags "delete" -e "container_id=105" -k -K -u <username>

Also, if you want to create a new VM on your Proxmox Cluster instead of a Container, you can run the Playbook with the TAG "createvm":

    - ansible-playbook manage_container.yml --tags "createvm" -e "container_id=900 container_clone_id=997 container_node=srvoffice1 container_storage=vm-ssd container_cores=2 container_memory=8192 container_name=vmDemo" -k -K -u <username>

Author Information
------------------

This Role is created by P. Haberkern (thedatabaseme)
