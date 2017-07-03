# vagrant-gluster-test
Vagrant test environment for GlusterFS

## Howto
Set the options in the Vagrantfile to suit your needs:
```yaml
PROVTYPE = "provgdeploy"
GLUSTERSERVER = 3
GLUSTERCLIENT = 2
```
Now run vagrant
```
vagrant up
```
This will spin up 3 gluster ganesha servers with each one extra disk and 2 clients fully configured and mounted nfs share.

- Checkout the [Standalone Heketi Demo README.md](https://github.com/scorputty/vagrant-gluster-test/blob/master/provheketi/README.md) for more info.

- `provsimple` will install and configure standard gluster and mount the share on the clients.

If you only want to spin up the nodes you can run this
```
vagrant up --no-provision
vagrant --provision
```
Note:
gdeploy ganasha is now working! (not yet pretty...)
```
- include: install_packages.yml
  tags: install-packages
- include: disable_firewall.yml
  tags: stop-firewall
- include: setup_keys.yml
  tags: setup-keys
- include: setup_keys.yml # Running this twice to be sure the keys are there
  tags: setup-keys
- include: prep_gdeploy.yml
  tags: prep-gdeploy
- include: setup_ganesha.yml
  run_once: true
  tags: setup-ganesha
- include: fix_ganesha.yml
  tags: fix-ganesha
```
Yes still very messy but it works...

## Added provision via Ansible Tower.
To test provisioning with Ansible Tower you need the official Vagrant image, more info here
- https://www.ansible.com/tower-trial

If you did that then you can use this test setup to provision 3 gluster servers, 2 clients and 1 tower server.

# Note2:
If vagrant up fails try running it again, it sometimes happens...
