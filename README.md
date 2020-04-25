# k8s-vagrant-libvirt

A minimal setup for running multi-node kubernetes in vagrant virtual
machines using libvirt on linux.

Related projects:

* https://github.com/galexrt/k8s-vagrant-multi-node (virtualbox, many features)

Current supported configuration(s):

* guest: centos 7
* network: flannel

# usage

Create and provision the cluster

```bash
vagrant up --provider=libvirt
```

Set the kubectl configuration file

```bash
vagrant ssh master -c "sudo cat /etc/kubernetes/admin.conf" > ${HOME}/.kube/config
```

Test cluster access from your host

```
[~/src/k8s-vagrant-libvirt]$ kubectl get nodes
NAME      STATUS   ROLES    AGE   VERSION
master    Ready    master   30m   v1.13.4
worker0   Ready    <none>   30m   v1.13.4
```

# configuration

The following options may be set in the `Vagrantfile`

```ruby
# number of worker nodes
NUM_WORKERS = 1
# number of extra disks per worker
NUM_DISKS = 1
# size of each disk in gigabytes
DISK_GBS = 10
```

# loading docker images

Use the [vagrant-docker_load](https://rubygems.org/gems/vagrant-docker_load) plugin to upload Docker images into Vagrant machines

```bash
vagrant plugin install vagrant-docker_load
```

An example of loading a [rook@master](https://github.com/rook/rook) build

```bash
[~/src/k8s-vagrant-libvirt]$ vagrant docker-load build-2568df12/ceph-amd64 rook/ceph:master
Loaded image: build-2568df12/ceph-amd64:latest
Loaded image: build-2568df12/ceph-amd64:latest
```

# troubleshooting

The following is a summary of the environments and applications that are known to work

```
[~/src/k8s-vagrant-libvirt]$ lsb_release -d
Description: Fedora release 29 (Twenty Nine)

[~/src/k8s-vagrant-libvirt]$ vagrant version
Installed Version: 2.1.2

[~/src/k8s-vagrant-libvirt]$ vagrant plugin list
vagrant-libvirt (0.0.40, system)
```

Ceph distributed storage via Rook

```
[~/src/k8s-vagrant-libvirt]$ kubectl -n rook-ceph-system logs rook-ceph-operator-b996864dd-l5czk | head -n 1
2019-03-21 16:09:18.168066 I | rookcmd: starting Rook v0.9.0-323.g2447520 with arguments '/usr/local/bin/rook ceph operator'

[~/src/k8s-vagrant-libvirt]$ kubectl -n rook-ceph get pods
NAME                                  READY   STATUS      RESTARTS   AGE
rook-ceph-mgr-a-6b5cdfcb6f-hg7tr      1/1     Running     0          4m33s
rook-ceph-mon-a-6cb6cfdb95-grgsz      1/1     Running     0          4m56s
rook-ceph-mon-b-6477f5fc8c-m5mzg      1/1     Running     0          4m50s
rook-ceph-mon-c-6cdf75fc4c-pgq5h      1/1     Running     0          4m42s
rook-ceph-osd-0-8b5d9c477-5s989       1/1     Running     0          4m11s
rook-ceph-osd-prepare-worker0-x5qqn   0/2     Completed   0          4m17s
rook-ceph-tools-76c7d559b6-vcxhr      1/1     Running     0          3m48s
```
