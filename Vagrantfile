# -----------------------------------------------------------------------------
# Custom variables
# -----------------------------------------------------------------------------
vm_hostname = "demo-app.local"
vagrant_plugins = %w(vagrant-hostmanager)
ip_address = "192.168.59.40"
box_version = "bento/centos-7.8"
provisioner = Vagrant::Util::Platform.windows? ? :ansible_local : :ansible


virtual_box_cpu = 2
virtual_box_mem = 4096
# -----------------------------------------------------------------------------
# Main Configuration:
# -----------------------------------------------------------------------------

Vagrant.configure("2") do |config|

  # Install required plugins
  vagrant_plugins.each do |plugin|
    system "vagrant plugin install #{plugin}" \
    unless Vagrant.has_plugin? plugin
  end

  # VM hostname setup
  config.vm.hostname = vm_hostname
  config.vm.define vm_hostname

  # Define Box Image
  config.vm.box = box_version

  # Set private IP interface
  config.vm.network "private_network", ip: ip_address

  # Configure proper hosts entries
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  # Set parameters for Virtual Box Machine:
  config.vm.provider "virtualbox" do |vb|
    vb.name = vm_hostname
    vb.memory = virtual_box_mem
    vb.cpus = virtual_box_cpu
  end

  config.vm.provision "shell", inline: "yum install git -y"
  id_rsa = File.readlines("#{Dir.home}/.ssh/id_rsa").first.strip
  config.vm.provision 'shell', inline: "echo #{id_rsa} >> /home/vagrant/.ssh/id_rsa"

  # Ansible provisioning:
  config.vm.provision "ansible_local" do |ansible|
    ansible.galaxy_role_file = "requirements.yml"
    # Workaround for broken pip installer
    # (https://stackoverflow.com/questions/65896334)
    ansible.pip_install_cmd = "curl -s https://bootstrap.pypa.io/pip/2.7/get-pip.py | sudo python"
    ansible.install_mode = "pip"
    ansible.playbook = "playbook.yml"
   end

end
