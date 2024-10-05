require 'yaml'

# Define the folder where YAML config files are stored
config_folder = 'configs/'

# Function to merge nested configuration hashes
def merge_config(default_config, override_config)
  default_config.merge(override_config) do |key, default_val, override_val|
    if default_val.is_a?(Hash) && override_val.is_a?(Hash)
      merge_config(default_val, override_val)
    else
      override_val.nil? ? default_val : override_val
    end
  end
end

# Define default settings
default_config = {
  'box' => 'ubuntu/bionic64',  # Default box in case global or VM-specific box isn't provided
  'hostname' => 'default-vm',
  'network' => {
    'forwarded_port' => { 'guest' => 80, 'host' => 8080, 'auto_correct' => true },
    'private_network' => { 'type' => 'dhcp' }
  },
  'provider' => {
    'virtualbox' => { 'memory' => '1024', 'cpus' => 2, 'gui' => false, 'customize' => [] }
  },
  'post_up_message' => "Your VM is up and running!"
}

# Helper function to load global and per-VM settings from a YAML file
def load_vm_configs(file, default_config)
  config_data = YAML.load_file(file)['vagrant']

  # Extract global settings if present
  global_settings = config_data['global'] || {}

  # If 'vms' key exists, it's a multi-VM file
  if config_data.key?('vms')
    config_data['vms'].map do |vm_config|
      # Merge default, global, and individual VM settings
      vm_config = merge_config(global_settings, vm_config)
      merge_config(default_config, vm_config)
    end
  else
    # If no 'vms', treat it as a single VM configuration
    [merge_config(default_config, merge_config(global_settings, config_data))]
  end
end

# Read all YAML (.yml and .yaml) files in the config folder
vm_configs = Dir[File.join(config_folder, '*.{yml,yaml}')].flat_map { |file| load_vm_configs(file, default_config) }

# Configure each VM based on its respective YAML configuration
Vagrant.configure("2") do |config|
  vm_configs.each do |vm_config|

    # Ensure each VM has a box specified, even if it's missing in the YAML config
    if vm_config['box'].nil? || vm_config['box'].empty?
      raise "A box must be specified for VM '#{vm_config['vm_name']}'"
    end

    # Define the VM inside the Vagrantfile using the 'vm_name' from the YAML file
    config.vm.define vm_config['vm_name'] do |vm|

      # Basic VM setup
      vm.vm.box = vm_config['box']
      vm.vm.hostname = vm_config['hostname']

      # Networking Configuration
      if vm_config['network']['forwarded_port']
        port = vm_config['network']['forwarded_port']
        vm.vm.network "forwarded_port", guest: port['guest'], host: port['host'], auto_correct: port['auto_correct']
      end

      if vm_config['network']['private_network']
        private_net = vm_config['network']['private_network']
        if private_net['ip']
          vm.vm.network "private_network", ip: private_net['ip']
        else
          vm.vm.network "private_network", type: private_net['type']
        end
      end

      # Provider Configuration (VirtualBox in this case)
      vm.vm.provider "virtualbox" do |vb|
        vb.memory = vm_config['provider']['virtualbox']['memory']
        vb.cpus = vm_config['provider']['virtualbox']['cpus']
        vb.gui = vm_config['provider']['virtualbox']['gui']
        if vm_config['provider']['virtualbox']['customize']
          vm_config['provider']['virtualbox']['customize'].each do |custom|
            vb.customize custom
          end
        end
      end

      # Post-Up Message
      if vm_config['post_up_message']
        vm.vm.post_up_message = vm_config['post_up_message']
      end

    end
  end
end
