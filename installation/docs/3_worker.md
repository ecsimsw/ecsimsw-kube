## Worker node

#### Get join command from master node
```
kubeadm token create --print-join-command
```

#### Join
```
kubeadm join ${IP} --token ${TOKEN}
```
