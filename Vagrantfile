# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "docker-registry-mirror"

  config.vm.box = "yungsang/boot2docker"

  config.vm.network :private_network, ip: "192.168.33.201"

  config.vm.network :forwarded_port, guest: 2375, host: 2375, disabled: true

  config.vm.provision :docker do |d|
    d.run "docker-registry-mirror",
      image: "registry",
      args: "--restart=always -p 5000:5000 -e STANDALONE=false -e MIRROR_SOURCE=https://registry-1.docker.io -e MIRROR_SOURCE_INDEX=https://index.docker.io"
  end
end
