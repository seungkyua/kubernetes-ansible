# Prerequisite #

 - This ansible-playbook is tested in Ubuntu 16.04 LTS
 - Need one Kubernetes deploy node (a.k.a kube-deploy)
 - Login from kube-deploy to all Kubernetes nodes by `root` user without password using hostname
 - kube-deploy should have swap memory over 2G byte
 - Every work should be executed by `stack` user at kube-deploy
 - Every nodes should be installed ansible and python packages

```
$ sudo apt-get update
$ sudo apt-get install -y ansible python2.7 python-minimal
```

 - Group names and node names can not be changed

```
[kube-deploy]
kube-deploy

[kube-masters]
kube-master01
kube-master02

[kube-nodes]
kube-node01
kube-node02
```

 - Have to changed your own password at `add_user_password` field in `group_vars/all` file



## Tips ##

An encrypted password can figure out following command

```
$ sudo apt-get install -y whois
$ mkpasswd --method=SHA-512
[ input password and enter]
```


## Execute order ##

```
$ sudo ansible-playbook -i hosts 00-create-user.yml
$ sudo ansible-playbook -i hosts 00-install-package.yml
$ sudo ansible-playbook -i hosts 01-install-docker.yml
$ sudo chown -R stack.stack ~/.ansible
$ ansible-playbook -i hosts 02-etcd.yml
$ ansible-playbook -i hosts 03-flannel.yml
$ ansible-playbook -i hosts 04-kubernetes.yml
```


## restart service ##

 - restart docker

```
$ sudo ansible-playbook -i hosts --tags="restart docker" 01-install-docker.yml
```

 - restart etcd

```
$ ansible-playbook -i hosts --tags="restart etcd" 02-etcd.yml
```

 - restart flannel

```
$ ansible-playbook -i hosts --tags="restart flannel" 03-flannel.yml
```

 - restart kubernetes

```
$ ansible-playbook -i hosts --tags="restart kube-apiserver,restart kube-controller-manager,restart kube-scheduler,restart kube-proxy,restart kubelet" 04-kubernetes.yml
```








