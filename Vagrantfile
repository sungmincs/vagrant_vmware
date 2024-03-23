# -*- mode: ruby -*-
# vi: set ft=ruby :

## configuration variables ##
# max number of worker nodes
N = 3
# each of components to install
k8s_V         = '1.27.4-00'                   # Kubernetes
ctrd_V        = '1.6.21-1'                    # Containerd
controller_ip = '192.168.1.130'
## /configuration variables ##

Vagrant.configure("2") do |config|

  #====================#
  # Control-Plane Node #
  #====================#

    config.vm.define "cp-k8s-#{k8s_V[0..5]}" do |cfg|
      cfg.vm.box = "gyptazy/ubuntu22.04-arm64"
      cfg.vm.provider "vmware_desktop" do |vb|
        vb.cpus = 2
        vb.memory = 4096
      end
      cfg.vm.host_name = "cp-k8s"
      cfg.vm.network "private_network", ip: controller_ip
      cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
      cfg.vm.synced_folder "../data", "/vagrant", disabled: true
      cfg.vm.provision "shell", path: "k8s_env_build.sh", args: [controller_ip, N]
      cfg.vm.provision "shell", path: "k8s_pkg_cfg.sh", args: [ k8s_V, ctrd_V, "CP"]
      cfg.vm.provision "shell", path: "controlplane_node.sh", args: controller_ip
    end

  #==============#
  # Worker Nodes #
  #==============#

  (1..N).each do |i|
    config.vm.define "w#{i}-k8s-#{k8s_V[0..5]}" do |cfg|
      cfg.vm.box = "gyptazy/ubuntu22.04-arm64"
      cfg.vm.provider "vmware_desktop" do |vb|
        vb.cpus = 1
        vb.memory = 2048
      end
      cfg.vm.host_name = "w#{i}-k8s"
      cfg.vm.network "private_network", ip: "192.168.1.23#{i}"
      cfg.vm.network "forwarded_port", guest: 22, host: "6010#{i}", auto_correct: true, id: "ssh"
      cfg.vm.synced_folder "../data", "/vagrant", disabled: true
      cfg.vm.provision "shell", path: "k8s_env_build.sh", args: [controller_ip, N]
      cfg.vm.provision "shell", path: "k8s_pkg_cfg.sh", args: [ k8s_V, ctrd_V, "W" ]
      cfg.vm.provision "shell", path: "worker_nodes.sh", args: controller_ip
    end
  end
end
