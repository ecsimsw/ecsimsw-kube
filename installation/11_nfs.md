## NFS Machines

### configurations
```
0. Virtual machine provider
1. Install NFS_Server 
2. Configure access list
3. Open Firewall
4. How to use in client side
```

## Virtual machine provider
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

## Server side

Sample shared directory path : `/shared` 

### Make shared path
```
sudo mkdir /shared
sudo chmod 755 /shared
sudo chown -R nobody:nogroup /shared
```

### Install nfs-server

````
sudo apt-get update && sudo apt-get install nfs-kernel-server portmap
sudo systemctl restart nfs-server
service nfs-server status
````

### Configure access list
`sudo vi /etc/exports`

```
/shared *(rw,sync,insecure,no_root_squash,all_squash,no_subtree_check,fsid=0f)
```
- rw : 읽기, 쓰기   
- sync : NFS가 응답 전 변경 내용을 기록함
- insecure : 인증되지 않는 엑세스 허용
- no_root_squash : client의 root 엑세스를 root 사용자로 인정 
- all_squash : 모든 사용자 권한을 익명 사용자 권한, nobody:nogroup으로 지정
- no_subtree_check : 하위 트리 검사 비활성화
- no_root_squash : root 권한을 가진 작업이라도 권한이 없는 사용자로 변환한다.

### Apply access list

```
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

### Open Firewall

Check firewall status 

```
sudo ufw status
```

Enable it if it's inactive

```
sudo ufw enable
```

Allow NFS acess port (default : 2049)
```
sudo ufw allow 2049
sudo ufw allow ${NFS_SSH_PORT}
systemctl status nfs-server.service
```

## Client side

```
sudo apt-get install nfs-common
```

Sample mount directory path : `nfs1/mnt/shared1`

```
mkdir -p nfs1/mnt/shared1
sudo chmod 755 nfs1/mnt/shared1
```

Mount NFS shared directory on client mount path
```
sudo mount -t nfs 192.168.52.13:/shared nfs1/mnt/nfs_shared1
```
