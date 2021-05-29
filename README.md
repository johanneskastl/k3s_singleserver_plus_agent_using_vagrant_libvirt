# k3s_singleserver_plus_agent_using_vagrant_libvirt

Vagrant setup for a k3s cluster with a single server and one worker (aka agent) using the libvirt provider

This setup creates a k3s server VM and one VM as k3s agent (aka worker) and installs k3s using Ansible.

Default OS is openSUSE Leap 15.2, but that can be changed in the Vagrantfile.

## Vagrant

1. You need vagrant obviously.
2. Fetch the box, per default this is `opensuse/Leap-15.2.x86_64`, using `vagrant box add opensuse/Leap-15.2.x86_64`.
3. Make sure the git submodules are fully working by issuing `git submodule init && git submodule update`
4. Run `vagrant up`
5. Run `kubectl --kubeconfig ansible/k3s-kubeconfig get nodes` and you should see your server and agent nodes.
6. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install k3s (because you want to install it yourself), just comment out the following lines in the `Vagrantfile`:
```
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.limit = "all"
      ansible.groups = {
        "k3sservers"  => [ "k3sserver" ],
        "k3sagents"   => [ "k3sagent1" ]
      }
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision
```

## Cleaning up

When tearing down the machine, the kubeconfig and token files that was download does not get deleted unfortunately.
To not cause problems the next time you start, just run `rm ansible/k3s-kubeconfig ansible/k3s-token` and all is well.
