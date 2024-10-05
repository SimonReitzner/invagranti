# Invagranti

1. [About the Project](#about-the-project)
2. [Built with](#built-with)
3. [Usage](#usage)
4. [Example Configurations](#example-configurations)
5. [Settings Overview](#settings-overview)
6. [Contact](#contact)

<br />

### About the Project <a id="about-the-project"></a>
Invagranti is a Vagrant-based project that simplifies the configuration and provisioning of virtual machines (VMs) through YAML files. It allows users to define single or multiple VM setups with custom settings like networking, resources, and provisioning tools. The project is perfect for developers, testers, and system admins who need to easily manage reproducible environments.

<p align="right">(<a href="#top">back to top</a>)</p>

### Built with <a id="built-with"></a>
This project is built using **Vagrant** and **VirtualBox** for virtual machine management and configuration. It provides a simple and flexible approach to automate VM creation and setup.

The key tools used include:

* [Vagrant](https://www.vagrantup.com/) – an open-source tool to create and configure reproducible development environments.
* [VirtualBox](https://www.virtualbox.org/) – a powerful virtualization product for running multiple operating systems.

<p align="right">(<a href="#top">back to top</a>)</p>

### Usage <a id="usage"></a>
To set up and launch virtual machines using this project, follow these steps:

1. **Clone the repository**:
    ```bash
    git clone https://github.com/SimonReitzner/invagranti
    ```
   
2. **Navigate to the project directory**:
    ```bash
    cd invagranti
    ```

3. **Review the configuration files** inside the `configs/` folder:
    - `multi_vm.yml`: Configurations for multiple VMs.
    - `single_vm.yml`: Configuration for a single VM.

4. **Customize the configuration files** according to your needs. You can modify settings such as VM names, memory, CPUs, network configurations, and more.

5. **Start the VM(s)**:
    ```bash
    vagrant up
    ```

6. Vagrant will read the configuration files and spin up the VMs with the desired settings.

7. **Access the VMs**:
    ```bash
    vagrant ssh <vm_name>
    ```

8. **Manage the VMs**:
    - To stop the VMs:
      ```bash
      vagrant halt
      ```
    - To destroy the VMs:
      ```bash
      vagrant destroy
      ```

<p align="right">(<a href="#top">back to top</a>)</p>

### Example Configurations <a id="example-configurations"></a>
Here’s an example of a multi-VM configuration defined in `multi_vm.yml`:

```yaml
vagrant:
  global:
    box: 'ubuntu/bionic64'
    provider:
      virtualbox:
        memory: '2048'
        cpus: 2

  vms:
    - vm_name: 'web-server'
      hostname: 'web-vm'
      network:
        forwarded_port:
          guest: 80
          host: 8080
        private_network:
          ip: '192.168.56.10'

    - vm_name: 'db-server'
      hostname: 'db-vm'
      network:
        private_network:
          ip: '192.168.56.11'
```

This configuration sets up two VMs: a web server and a database server, each with its own network settings and resources.

<p align="right">(<a href="#top">back to top</a>)</p>

### Settings Overview <a id="settings-overview"></a>

| **YAML Path**                               | **Description**                                                                                          | **Type**              | **Default**                          | **Example**                                             |
|---------------------------------------------|----------------------------------------------------------------------------------------------------------|-----------------------|--------------------------------------|---------------------------------------------------------|
| `vagrant.global.box`                        | Default Vagrant box to be used for all VMs unless overridden                                               | String                | `'ubuntu/bionic64'`                  | `'ubuntu/focal64'`                                      |
| `vagrant.global.hostname`                   | Default hostname for VMs unless overridden                                                                | String                | `'default-vm'`                       | `'my-vm'`                                               |
| `vagrant.global.network.forwarded_port`     | Global configuration for forwarded ports                                                                  | Hash                  | `{ guest: 80, host: 8080 }`          | `{ guest: 443, host: 8443 }`                            |
| `vagrant.global.network.private_network`    | Global private network configuration                                                                      | Hash                  | `{ type: 'dhcp' }`                   | `{ type: 'static', ip: '192.168.56.10' }`               |
| `vagrant.global.provider.virtualbox.memory` | Global memory allocation (MB)                                                                             | String or Integer     | `1024`                               | `2048`                                                  |
| `vagrant.global.provider.virtualbox.cpus`   | Global number of CPU cores for VMs                                                                        | Integer               | `2`                                  | `4`                                                     |
| `vagrant.global.provider.virtualbox.gui`    | Global flag to start the VMs with a GUI                                                                   | Boolean               | `false`                              | `true`                                                  |
| `vagrant.global.provider.virtualbox.customize[]` | Array of custom VirtualBox commands for all VMs                                                           | Array                 | `[]`                                 | `['modifyvm', :id, '--ioapic', 'on']`                   |
| `vagrant.vms[].vm_name`                     | Name of the individual VM within the Vagrantfile                                                          | String                | `nil`                                | `'web-server'`                                          |
| `vagrant.vms[].hostname`                    | Hostname of the individual VM                                                                             | String                | `'default-vm'`                       | `'web-vm'`                                              |
| `vagrant.vms[].box`                         | Vagrant box to be used for this specific VM                                                               | String                | `nil`                                | `'ubuntu/focal64'`                                      |
| `vagrant.vms[].network.forwarded_port.guest`| The guest VM port to forward                                                                              | Integer               | `80`                                 | `80`                                                    |
| `vagrant.vms[].network.forwarded_port.host` | The host machine port for forwarding traffic                                                              | Integer               | `8080`                               | `8080`                                                  |
| `vagrant.vms[].network.forwarded_port.auto_correct` | Automatically correct port collisions by incrementing the host port                                       | Boolean               | `true`                               | `true`                                                  |
| `vagrant.vms[].network.private_network.type`| Type of private network (usually `dhcp` or `static`)                                                      | String                | `'dhcp'`                             | `'static'`                                              |
| `vagrant.vms[].network.private_network.ip`  | Static IP address assigned to the VM's private network                                                    | String                | `nil`                                | `'192.168.56.10'`                                       |
| `vagrant.vms[].provider.virtualbox.memory`  | Amount of memory (in MB) allocated to the specific VM                                                     | String or Integer     | `'1024'`                             | `'2048'`                                                |
| `vagrant.vms[].provider.virtualbox.cpus`    | Number of CPU cores allocated to the specific VM                                                          | Integer               | `2`                                  | `4`                                                     |
| `vagrant.vms[].provider.virtualbox.gui`     | Whether the VM should start with a GUI (true/false)                                                       | Boolean               | `false`                              | `true`                                                  |
| `vagrant.vms[].provider.virtualbox.customize[]` | Array of VirtualBox customization commands for the specific VM                                            | Array                 | `[]`                                 | `['modifyvm', :id, '--ioapic', 'on']`                   |
| `vagrant.vms[].provision.shell`             | Shell provisioner for running shell scripts inside the VM                                                 | Array       | `[]`                                 | `[{ inline: "echo Hello" }, { path: "script.sh" }]`      |
| `vagrant.vms[].provision.ansible.playbook`  | Path to the Ansible playbook for provisioning                                                             | String                | `nil`                                | `'provisioning/playbook.yml'`                           |
| `vagrant.vms[].provision.ansible.inventory` | Path to the Ansible inventory file                                                                        | String                | `nil`                                | `'provisioning/inventory'`                              |
| `vagrant.vms[].provision.ansible.config_file`| Path to the Ansible configuration file                                                                    | String                | `nil`                                | `'provisioning/ansible.cfg'`                            |
| `vagrant.vms[].provision.ansible.limit`     | Limit the scope of the playbook run to specific groups or hosts                                            | String                | `nil`                                | `'web-server'`                                          |
| `vagrant.vms[].provision.ansible.become`    | Enable privilege escalation (e.g., sudo)                                                                  | Boolean               | `false`                              | `true`                                                  |
| `vagrant.vms[].provision.ansible.extra_vars`| Extra variables passed to the Ansible playbook                                                            | String                  | `{}`                                 | `{ db_host: '192.168.56.10' }`                          |
| `vagrant.vms[].provision.ansible.verbose`   | Increase verbosity of Ansible output                                                                      | Boolean or String     | `false`                              | `'vvv'`                                                 |
| `vagrant.vms[].provision.ansible.galaxy_roles_path` | Path to the directory where Ansible Galaxy roles are stored                                               | String                | `nil`                                | `'roles/'`                                              |
| `vagrant.vms[].provision.ansible.galaxy_role_file`| Path to a file that contains a list of Galaxy roles to be installed                                        | String                | `nil`                                | `'requirements.yml'`                                    |
| `vagrant.vms[].provision.ansible.install`   | Whether to install Ansible inside the VM before running the playbook                                       | Boolean               | `false`                              | `true`                                                  |
| `vagrant.vms[].post_up_message`             | Message displayed after VM startup                                                                        | String                | `"Your VM is up and running!"`       | `"VM setup complete!"`                                  |

This table outlines the key settings available for VM customization in the YAML configuration files.

<p align="right">(<a href="#top">back to top</a>)</p>

### Contact <a id="contact"></a>
Project Link: [Invagranti](https://github.com/SimonReitzner/invagranti)

<p align="right">(<a href="#top">back to top</a>)</p>