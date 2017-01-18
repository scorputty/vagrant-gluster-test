# vagrant-gluster-test
Vagrant test environment for GlusterFS

## Howto
Set the options in the Vagrantfile to suit your needs:
```yaml
PROVTYPE = "provheketi"
GLUSTERSERVER = 3
GLUSTERCLIENT = 2
```
Now run vagrant
```
vagrant up
```
This will spin up 3 gluster servers with each one extra disk and two clients. You can then test/play with heketi.
If you only want to spin up the nodes you can run this
```
vagrant up --no-provision
vagrant --provision
```
Note:
gdeploy will also be a option soon
