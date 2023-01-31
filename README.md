# Autogenerator microk8s cluster. Virtualbox + Vagrant + Ansible

## Installation

```
git clone https://github.com/M4MIND/vagrant-microk8s.git
```

## Vagrant plugin

Need to install vagrant plugin vagrant-hosts

`vagrant plugin install vagrant-hosts`

## How to run

```
cd ./microk8s/
vagrant up
```

## Configuration Vagrant file

Vagrantfile has `machines` and `box` variables with configurations for all machines.

Example: 

````ruby
box = "name-box" # Box name

machines = {
    :bridgeInterface => <string>, # Interface for create bridge network interface on Virtualbox. Example enp0s8
    :node => { # List nodes 
        # First node (Master)
        "master" => {
            :cpus => <int>, # How to CPU will use VM
            :ram => <int>, # How ram will use VM 
            :privateIp => <srting>, # Private network for connection workers
            :publicIp => <string>, # Public IP
            :playbooks => [ # Playbooks list for ansible
                {
                    :file => './ansible/microk8s.yaml', # Install microk8s
                    :extra_vars => {} # You can set custom variables into asimble playbook
                },
                {
                    :file => './ansible/master/playbook.yaml', # Configuration master node
                    :extra_vars => {:addons => ["dns", "dashboard", "ingress", "registry"]} # List of addons for microk8s 
                },
                # Can remove this if you don't open dashboard
                {
                    :file => './ansible/master/add-rule-ingeress-to-dashboard.yaml', # Proxy for dashboard
                    :extra_vars => {}
                }
            ]
        },
        # Worker node
        "node-0" => {
            :cpus => <int>, # How to CPU will use VM
            :ram => <int>, # How ram will use VM 
            :privateIp => <srting>, # Private network for connection workers
            :publicIp => <string>, # Public IP
            :playbooks => [
                {
                    :file => './ansible/microk8s.yaml', # Install microk8s
                    :extra_vars => {},
                },
                {
                    :file => './ansible/worker/playbook.yaml', # Join worker node to master node
                    :extra_vars => {
                        :master_domain => <string> # IP or Domain of master node
                    }
                }
                ........................
                # You can set custom playbooks 
            ]
        },
        ........................
        # Add new worker node or master node 
        ........................
    }
}
````

## Kubectl config

Kubectl config saved .kube directory 

`./microk8s/.kube/config-{ip-master-node}`
