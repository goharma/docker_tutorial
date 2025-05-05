Vagrant.configure("2") do |config|
  config.vm.define :my_rhel8_vm do |vm| # Define the VM with a custom name
  config.vm.box = "generic/rhel8"
  
  # Map the host folder to the guest folder
  config.vm.synced_folder "./host-folder", "/home/vagrant/guest-folder"

  
  config.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
  end

  # Bridged adapter
  config.vm.network "public_network", bridge: "Intel(R) Wi-Fi 6E AX211 160MHz"

  # Host-only adapter
  # config.vm.network "private_network", ip: "192.168.56.10"

  config.vm.provision "shell", inline: <<-SHELL
      # Exit immediately if a command exits with a non-zero status
      set -e

      
      sudo dnf update -y
      # Create a file (set-env-variables.ps1) with 
      # $env:RH_USERNAME = "USER"
      # $env:RH_PASSWORD = "PASS"
      # Then source it befroe running this script
      # .\set-env-variables.ps1
      sudo subscription-manager register --username=#{ENV['RH_USERNAME']} --password=#{ENV['RH_PASSWORD']}
      sudo subscription-manager repos --enable codeready-builder-for-rhel-8-$(arch)-rpms

      # Install required packages
      sudo dnf install -y dnf-utils device-mapper-persistent-data lvm2

      # Add the Docker CE repository
      sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

      # Install Docker CE (it works for RHEL 8 as well)
      sudo dnf install -y docker-ce docker-ce-cli containerd.io

      # Enable the Docker service
      echo "ENABLE DOCKER"
      sudo systemctl enable --now docker

      # Allow vagrant to run docker commands without sudo
      sudo usermod -aG docker vagrant

      # Map folder
      sudo mkdir -p /home/vagrant/guest-folder
      sudo chown -R vagrant:vagrant /home/vagrant/guest-folder
      

      echo "***************************************"
      echo "Log into the VM with 'vagrant ssh'"
      echo "***************************************"
  SHELL
  end
end