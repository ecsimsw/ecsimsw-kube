## VMS
```
Vagrant.configure("2") do |config|
  config.vm.define "nfs1" do |nfs1|
    nfs1.vm.box = "bento/ubuntu-20.04"
    nfs1.vm.network "private_network", ip: "192.168.52.13"
    nfs1.vm.hostname = "nfs1"
    nfs1.vm.disk :disk, size: "50GB", primary: true
    nfs1.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end
  end

  config.vm.define "nfs2" do |nfs2|
    nfs2.vm.box = "bento/ubuntu-20.04"
    nfs2.vm.network "private_network", ip: "192.168.52.14"
    nfs2.vm.hostname = "nfs2"
    nfs2.vm.disk :disk, size: "10GB", primary: true
    nfs2.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end
  end
end
```

## Install NFS_Server 

````
sudo apt-get install nfs-kernel-server portmap
sudo systemctl restart nfs-server
service nfs-server status
````

## Make shared path
```
sudo mkdir -p /mnt/nfs_shared1
sudo chown 777 /mnt/nfs_shared1/
sudo chown -R nobody:nogroup /mnt/nfs_shared1/
```

## Configure access list
```
sudo vi /etc/exports 
```

```
/mnt/nfs_shared1 *(rw,sync,no_subtree_check)
```

- rw : 읽기, 쓰기   
- sync : NFS가 응답 전 변경 내용을 기록함
- no_subtree_check : 하위 트리 검사 비활성화
- no_root_squash : root 권한을 가진 작업이라도 권한이 없는 사용자로 변환한다.

## Apply access list

```
sudo exportfs -a

sudo systemctl restart nfs-kernel-server
```

## Open Firewall

```
sudo ufw status
```
Status: inactive

```
sudo ufw enabl
```
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y   
Firewall is active and enabled on system startup

```
sudo ufw allow 2049
```

```
systemctl status nfs-server.service
```

## Client side

```
sudo apt-get install nfs-common
```

```
mkdir -p nfs1/mnt/shared1
```

```
sudo mount -t nfs 192.168.52.13:/mnt/nfs_shared1 nfs1/mnt/nfs_shared1
```
