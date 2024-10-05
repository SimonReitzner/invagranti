# Invagranti

1. [About the Project](#about-the-project)
2. [Built with](#built-with)
3. [Usage](#usage)
4. [Contact](#contact)

<br />

### About the Project <a id="about-the-project"></a>
This project demonstrates a Vagrant-based setup for configuring single or multiple virtual machines (VMs) using YAML configuration files. It enables users to define and deploy VMs with ease by specifying settings such as networking, resources, and providers via YAML files.

<p align="right">(<a href="#top">back to top</a>)</p>

### Built with <a id="build-with"></a>
The project is built using **Vagrant** and **VirtualBox** for VM configuration management.

The main tools and technologies used are:

* [Vagrant](https://www.vagrantup.com/)
* [VirtualBox](https://www.virtualbox.org/)

<p align="right">(<a href="#top">back to top</a>)</p>

### Usage <a id="usage"></a>
Follow the steps below to configure and launch virtual machines using the provided setup.

1. Clone the repository
    ```sh
    git clone https://github.com/SimonReitzner/invagranti
    ```
   
2. Navigate to the project directory
    ```sh
    cd invagranti
    ```

3. Review the configuration files inside the `configs/` folder. These are example configurations:
    - `multi_vm.yml`: Defines configurations for multiple VMs.
    - `single_vm.yml`: Defines configuration for a single VM.
   
4. Customize the YAML configuration files to fit your needs by modifying VM names, memory, CPUs, network settings, etc.

5. Start the VM(s) defined in the YAML configuration file:
    ```sh
    vagrant up
    ```

6. Vagrant will read the configurations from the YAML files, merge them with the default settings, and bring up the defined VMs using the specified provider (e.g., VirtualBox).

7. Access the VMs through SSH:
    ```sh
    vagrant ssh vm_name
    ```

8. To stop or destroy the VMs, use the following commands:
    ```sh
    vagrant halt
    vagrant destroy
    ```

**Example Configurations**

Here is an example of the `multi_vm.yml` configuration file:
```yaml
vagrant:
  global_settings:
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
          ip: '192.168.33.10'

    - vm_name: 'db-server'
      hostname: 'db-vm'
      network:
        private_network:
          ip: '192.168.33.11'
```

<p align="right">(<a href="#top">back to top</a>)</p>

#### Overview of settings

| **YAML Path**                               | **Description**                                                                                                                                   | **Type**            | **Default**                      | **Example**                                             |
|---------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|----------------------------------|---------------------------------------------------------|
| `vagrant.global_settings.box`               | Specifies the Vagrant box to use for all VMs unless overridden at the VM level.                                                                    | String              | `'ubuntu/bionic64'`              | `'ubuntu/focal64'`                                      |
| `vagrant.vms[].vm_name`                     | The name used to define each VM inside the Vagrantfile.                                                                                            | String              | `nil`                            | `'web-server'`                                          |
| `vagrant.vms[].hostname`                    | Sets the hostname of the VM.                                                                                                                      | String              | `'default-vm'`                   | `'web-vm'`                                              |
| `vagrant.vms[].network.forwarded_port.guest`| The guest port inside the VM that is forwarded.                                                                                                    | Integer             | `80`                             | `80`                                                    |
| `vagrant.vms[].network.forwarded_port.host` | The host port that forwards traffic to the guest port.                                                                                             | Integer             | `8080`                           | `8080`                                                  |
| `vagrant.vms[].network.forwarded_port.auto_correct` | If true, Vagrant automatically corrects port collisions on the host by incrementing the host port.                                                        | Boolean             | `true`                           | `true`                                                  |
| `vagrant.vms[].network.private_network.type`| Specifies the type of private network (usually `dhcp` or `static`).                                                                                | String              | `'dhcp'`                         | `'static'`                                              |
| `vagrant.vms[].network.private_network.ip`  | The IP address for the private network (if static is used).                                                                                        | String              | `nil`                            | `'192.168.33.10'`                                       |
| `vagrant.vms[].provider.virtualbox.memory`  | Specifies the amount of memory (in MB) allocated to the VM.                                                                                        | String or Integer   | `'1024'`                         | `'2048'`                                                |
| `vagrant.vms[].provider.virtualbox.cpus`    | Specifies the number of CPU cores allocated to the VM.                                                                                             | Integer             | `2`                              | `4`                                                     |
| `vagrant.vms[].provider.virtualbox.gui`     | If true, starts the VM with a GUI.                                                                                                                 | Boolean             | `false`                          | `true`                                                  |
| `vagrant.vms[].provider.virtualbox.customize[]` | An array of custom VirtualBox commands to further configure the VM (like changing CPU architecture, IO controllers, etc.).                         | Array               | `[]`                             | `['modifyvm', :id, '--ioapic', 'on']`                   |
| `vagrant.vms[].post_up_message`             | A message displayed after the VM is successfully brought up.                                                                                       | String              | `"Your VM is up and running!"`    | `"VM setup complete!"`                                  |
| `vagrant.global_settings.provider.virtualbox.memory` | Globally defines the memory setting for all VMs in the YAML file unless overridden at the VM level.                                                | String or Integer   | `'1024'`                         | `'2048'`                                                |
| `vagrant.global_settings.provider.virtualbox.cpus`   | Globally defines the number of CPU cores for all VMs in the YAML file unless overridden at the VM level.                                            | Integer             | `2`                              | `4`                                                     |
| `vagrant.global_settings.provider.virtualbox.gui`    | Globally specifies whether all VMs should start with a GUI unless overridden at the VM level.                                                      | Boolean             | `false`                          | `true`                                                  |
| `vagrant.global_settings.network.forwarded_port`     | Globally specifies the forwarded port configuration for all VMs unless overridden at the VM level.                                                 | Hash (guest, host)  | `{ guest: 80, host: 8080 }`      | `{ guest: 443, host: 8443 }`                            |
| `vagrant.global_settings.network.private_network`    | Globally specifies the private network configuration for all VMs unless overridden at the VM level.                                                | Hash (type, ip)     | `{ type: 'dhcp' }`               | `{ type: 'static', ip: '192.168.33.10' }`               |
| `vagrant.vms[]`                              | Lists the individual VM configurations. Each VM has its own settings defined under this key.                                                       | Array of Hashes     | `nil`                            | See `multi_vm.yml` example in previous section           |
| `vagrant.global_settings`                   | Defines global settings that apply to all VMs, unless overridden at the individual VM level.                                                       | Hash                | `nil`                            | `{ box: 'ubuntu/bionic64', provider: {...} }`           |

#### Descriptions

1. **`vagrant.global_settings.box`**: Defines a global box to be used by all VMs unless overridden for specific VMs.
2. **`vagrant.vms[].vm_name`**: The unique identifier for each VM, used to define the VM within the Vagrantfile.
3. **`vagrant.vms[].hostname`**: Assigns a hostname for the VM within its network.
4. **`vagrant.vms[].network.forwarded_port.guest`**: Port number on the VM that will be exposed to the host machine.
5. **`vagrant.vms[].network.forwarded_port.host`**: Host machine port through which the VM's guest port will be accessible.
6. **`vagrant.vms[].network.forwarded_port.auto_correct`**: Automatically resolve port conflicts on the host by choosing the next available port.
7. **`vagrant.vms[].network.private_network.type`**: Specifies the type of private network for the VM, either `dhcp` or `static`.
8. **`vagrant.vms[].network.private_network.ip`**: The static IP address assigned to the VM if a private network with static IP is used.
9. **`vagrant.vms[].provider.virtualbox.memory`**: Memory (in MB) assigned to the VM.
10. **`vagrant.vms[].provider.virtualbox.cpus`**: Number of CPU cores assigned to the VM.
11. **`vagrant.vms[].provider.virtualbox.gui`**: Enables/disables the GUI for the VM. If set to `false`, the VM will run in headless mode.
12. **`vagrant.vms[].provider.virtualbox.customize[]`**: An array of custom commands for fine-tuning VirtualBox VM settings.
13. **`vagrant.vms[].post_up_message`**: Custom message displayed after the VM has started.
14. **`vagrant.global_settings.provider.virtualbox.memory`**: Global setting for memory assigned to all VMs, unless individually overridden.
15. **`vagrant.global_settings.provider.virtualbox.cpus`**: Global setting for CPU cores assigned to all VMs, unless individually overridden.
16. **`vagrant.global_settings.provider.virtualbox.gui`**: Global setting to enable or disable the GUI for all VMs, unless overridden.
17. **`vagrant.global_settings.network.forwarded_port`**: Global port forwarding settings, applied to all VMs unless overridden.
18. **`vagrant.global_settings.network.private_network`**: Global private network configuration for all VMs, unless overridden.
19. **`vagrant.vms[]`**: Contains all individual VM configurations, with each VM’s unique settings.
20. **`vagrant.global_settings`**: Contains global configurations for all VMs, such as box, network, and provider settings.

This provides a comprehensive reference for all the options available in the YAML configuration files and their corresponding Vagrantfile paths.

### Contact <a id="contact"></a>
Project Link: [Invagranti](https://github.com/SimonReitzner/invagranti)

<p align="right">(<a href="#top">back to top</a>)</p>