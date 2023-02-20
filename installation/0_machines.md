## Machines

### Set vagrant environment

#### configurations
```
0. Virtual machine provider
1. Operating system
2. Private network ip
3. Hostnames 
4. Resources (Cpu, Memory)
```

#### Vagrantfile
```
Vagrant.configure("2") do |config|
  config.vm.define "master" do |master|
    master.vm.box = "bento/ubuntu-20.04"
    master.vm.network "private_network", ip: "192.168.52.10"
    master.vm.hostname = "master"
    master.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
  end

  config.vm.define "worker1" do |worker1|
    worker1.vm.box = "bento/ubuntu-20.04"
    worker1.vm.network "private_network", ip: "192.168.52.11"
    worker1.vm.hostname = "worker1"
    worker1.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
  end

  config.vm.define "worker2" do |worker2|
    worker2.vm.box = "bento/ubuntu-20.04"
    worker2.vm.network "private_network", ip: "192.168.52.12"
    worker2.vm.hostname = "worker2"
    worker2.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
  end
end
```

#### Provisions the vagrant environment

```
cd machines
vagrant up
```

#### List machines status
```
vagrant status
```

#### Connects to machine via SSH
```
vagrant ssh master
vagrant ssh worker1
vagrant ssh worker2
```
