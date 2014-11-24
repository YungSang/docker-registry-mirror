# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

DOCKER_REGISTRY    = "docker-registry-mirror"
DOCKER_REGISTRY_UI = "docker-registry-frontend"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "docker-registry-mirror"

  config.vm.box = "yungsang/boot2docker"

  config.vm.network :private_network, ip: "192.168.33.201"

  config.vm.network :forwarded_port, guest: 2375, host: 2375, disabled: true

  config.vm.synced_folder ".", "/vagrant"

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      docker rm -f #{DOCKER_REGISTRY} 2> /dev/null || true
      docker rm -f #{DOCKER_REGISTRY_UI} 2> /dev/null || true
    EOT
  end

  config.vm.provision :docker do |d|
    d.run "#{DOCKER_REGISTRY}",
      image: "registry",
      args: "-p 5000:5000 -e MIRROR_SOURCE=https://registry-1.docker.io -e MIRROR_SOURCE_INDEX=https://index.docker.io -e SQLALCHEMY_INDEX_DATABASE=sqlite:////tmp/registry/docker-registry.db -v /vagrant/registry:/tmp/registry"
    d.run "#{DOCKER_REGISTRY_UI}",
      image: "konradkleine/docker-registry-frontend",
      args: "-p 80:80 --link #{DOCKER_REGISTRY}:#{DOCKER_REGISTRY} -e ENV_DOCKER_REGISTRY_HOST=#{DOCKER_REGISTRY} -e ENV_DOCKER_REGISTRY_PORT=5000"
  end

  config.vm.provision :shell, run: "always" do |sh|
    sh.inline = <<-EOT
      IS_RUNNING=$(docker inspect --format '{{.State.Running}}' #{DOCKER_REGISTRY} 2> /dev/null)
      if [ "$IS_RUNNING" != "true" ] ; then
        docker start #{DOCKER_REGISTRY}
      fi
      IS_RUNNING=$(docker inspect --format '{{.State.Running}}' #{DOCKER_REGISTRY_UI} 2> /dev/null)
      if [ "$IS_RUNNING" != "true" ] ; then
        docker start #{DOCKER_REGISTRY_UI}
      fi
    EOT
  end
end
