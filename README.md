# ansible-hetzner-deployment

This ansible playbook can be used to automize the deployment of [Hetzner servers](https://www.hetzner.de/) with Debian Jessie. It uses the installimage script that is provided by Hetzner inside of their rescue system.

## How to use this playbook

I assume that you already have a working ansible installation. For this example we use the following configuration.

###### ansible.cfg
```
[defaults]
inventory = hosts
```

###### hosts
```
server.example.com

[all:vars]
deployment_key = ~/.ssh/core_deployment
public_root_key = ~/.ssh/root_key.pub
```

1. Generate two pairs of SSH keys (`ssh-keygen -t ed25519`). For example `~/.ssh/core_deployment` and `~/.ssh/root_key`
2. Make sure you specified the correct paths to the keys in your hosts file or in some of the many ways that ansible allows to handle host variables. 
3. Login to your *Hetzner robot* webinterface.
4. Go to your server, select the *Rescue* tab and upload the `core_deployment.pub` public key file in Hetzners key management.
5. Then you enable the rescue system and select the uploaded key for SSH access.
6. Reboot the server. It will boot into Hetzners rescue system.
7. Run the playbook. You need to specify the inventory hostname as an extra variable: `ansible-playbook deploy_debian_hetzner.yml -e 'target_host=server.example.com'`  
**THIS WILL DELETE ALL DATA FROM YOUR SERVER WITHOUT ANY QUESTION**
8. This will install Debian Jessie and wait for the server to be rebooted. In my tests it took about three minutes.

## Disclaimer
I do not work for Hetzner and this software is in no official way connected to Hetzner.
