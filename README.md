# Docker Private Registry + Mirror + Web UI

Cf.) https://github.com/docker/docker/blob/master/docs/sources/articles/registry_mirror.md  
Cf.) https://github.com/kwk/docker-registry-frontend  

## Features

- Run a Docker Private Registry + Mirror as a container locally with Vagrant
- Mount a local directory onto the mirrored registry in the container for persistence
- Run a Docker Private Registry Web UI as a container

## Requirements

- [VirtualBox](https://www.virtualbox.org/) >= v4.3.18
- [Vagrant](https://www.vagrantup.com/) >= v1.6.5

## Start a Docker Private Registry + Mirror

```
$ git clone https://github.com/YungSang/docker-registry-mirror.git
$ cd docker-registry-mirror
$ vagrant up
```

It starts a Docker Private Registry + Mirror at http://192.168.33.201:5000 by default.  
You can set any IP Address by editing the following line in Vagrantfile.

```ruby
  config.vm.network :private_network, ip: "192.168.33.201"
```

## Open a Docker Private Registry Web UI

```
$ open http://192.168.33.201/
```

## Use the Registry + Mirror from other Docker hosts

You have to set `--registry-mirror http://192.168.33.201:5000` as an additional parameter for Docker daemon and restart the daemon.

### A sample Vagrantfile for boot2docker

```ruby
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "yungsang/boot2docker"

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      sudo echo 'EXTRA_ARGS="--registry-mirror http://192.168.33.201:5000 --insecure-registry 192.168.33.201:5000"' > /var/lib/boot2docker/profile
      sudo /etc/init.d/docker restart
    EOT
  end
end
```

```
$ cd samples/boot2docker
$ vagrant up
$ vagrant ssh
docker@boot2docker:~$ docker pull yungsang/flannel
Pulling repository yungsang/flannel
f832658626da: Pulling image (latest) from yungsang/flannel, mirror: http://192.168.33.201:5000/v1/
f832658626da: Download complete
511136ea3c5a: Download complete
50215b109eda: Download complete
53f380325ee9: Download complete
1281aa8c00fc: Download complete
0d041d6eb31b: Download complete
0df49300cb95: Download complete
c446317bf8a5: Download complete
7fad046ef01e: Download complete
Status: Downloaded newer image for yungsang/flannel:latest
docker@boot2docker:~$ 
```

### A sample Vagrantfile for CoreOS

```ruby
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "yungsang/coreos-alpha"

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      sudo echo 'DOCKER_OPTS="--registry-mirror http://192.168.33.201:5000"' > /run/docker_opts.env
      sudo systemctl restart docker
    EOT
  end
end
```

```
$ cd samples/coreos
$ vagrant up
$ vagrant ssh
core@localhost ~ $ docker pull yungsang/flannel
Pulling repository yungsang/flannel
f832658626da: Pulling image (latest) from yungsang/flannel, mirror: http://192.168.33.201:5000/v1/
f832658626da: Download complete
511136ea3c5a: Download complete
50215b109eda: Download complete
53f380325ee9: Download complete
1281aa8c00fc: Download complete
0d041d6eb31b: Download complete
0df49300cb95: Download complete
c446317bf8a5: Download complete
7fad046ef01e: Download complete
Status: Downloaded newer image for yungsang/flannel:latest
core@localhost ~ $ 
```

### A sample Vagrantfile for Fedora Atomic

```ruby
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "yungsang/fedora-atomic"

  config.vm.provision :shell do |sh|
    sh.inline = <<-EOT
      sudo echo 'OPTIONS="--selinux-enabled --registry-mirror http://192.168.33.201:5000 --insecure-registry 192.168.33.201:5000"' > /etc/sysconfig/docker
      sudo systemctl restart docker
    EOT
  end
end
```

```
$ cd samples/atomic
$ vagrant up
$ vagrant ssh
[vagrant@localhost ~]$ docker pull yungsang/flannel
Pulling repository yungsang/flannel
f832658626da: Pulling image (latest) from yungsang/flannel, mirror: http://192.168.33.201:5000/v1/
f832658626da: Download complete
511136ea3c5a: Download complete
50215b109eda: Download complete
53f380325ee9: Download complete
1281aa8c00fc: Download complete
0d041d6eb31b: Download complete
0df49300cb95: Download complete
c446317bf8a5: Download complete
7fad046ef01e: Download complete
Status: Downloaded newer image for yungsang/flannel:latest
[vagrant@localhost ~]$ 
```
