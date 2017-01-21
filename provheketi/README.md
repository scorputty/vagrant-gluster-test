# Standalone Heketi Demo (edited for this project)

This vagrant-ansible script creates a setup for Heketi to manage GlusterFS.  It creates 3 server VMs (glusterserver1,glusterserver2,glusterserver3) with 20GB drives each server.  The ansible script only installs gluster-server on each of the storage servers and then enables the gluster service.  It does not create or initialize any of the disks.  The disks will later be managed by Heketi.  The script also creates a 2 client VMs (client1,client2) to demo mounting the volume created by Heketi.

# Requisites

* You will need Virtualbox or libvirt, Vagrant, and Ansible installed on your system.

# Setup

* For Virtualbox type: `vagrant up`
* For Libvirt type: `vagrant up --provider=libvirt`

* Now load the topology

```
$ <sudo for libvirt> vagrant ssh glusterserver1
$ export HEKETI_CLI_SERVER=http://localhost:8080
$ heketi-cli topology load --json=topology_<provider>.json
```

Where _provider_ is either `virtualbox` or `libvirt` depending on the vagrant provider.

# Create a volume
Heketi REST API has been created to be consumed by services like OpenStack Manila, Kubernetes, OpenShift, and others.  For simplicity, a command line tool has been provided for use.

To create a 10GiB (is currently the minimum size) Replica 3 volume execute the following:

```
$ heketi-cli volume create --size=10
```

# More information
* See the command line help screen by typing: `./heketi-cli -h`
* Please see the [API](https://github.com/heketi/heketi/wiki/API) for REST commands.
* Edited by scorputty to match my [vagrant-gluster-test](https://github.com/scorputty/vagrant-gluster-test/tree/master/provheketi) project.
