# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

CONTAINER_NAME = "docker-registry-mirror"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "docker-registry-mirror"

  config.vm.box = "yungsang/boot2docker"

  config.vm.network :private_network, ip: "192.168.33.201"

  config.vm.network :forwarded_port, guest: 2375, host: 2375, disabled: true

  config.vm.synced_folder ".", "/vagrant"

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      docker rm -f #{CONTAINER_NAME} 2> /dev/null || true
    EOT
  end

  config.vm.provision :docker do |d|
    d.run "#{CONTAINER_NAME}",
      image: "registry",
      args: "-p 5000:5000 -e STANDALONE=false -e MIRROR_SOURCE=https://registry-1.docker.io -e MIRROR_SOURCE_INDEX=https://index.docker.io -e SETTINGS_FLAVOR=local -v /vagrant/registry:/tmp/registry"
  end

  config.vm.provision :shell, run: "always" do |sh|
    sh.inline = <<-EOT
      IS_RUNNING=$(docker inspect --format '{{.State.Running}}' #{CONTAINER_NAME} 2> /dev/null)
      if [ "$IS_RUNNING" != "true" ] ; then
        docker start #{CONTAINER_NAME}
      fi
    EOT
  end
end
