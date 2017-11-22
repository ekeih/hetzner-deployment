# ansible-hetzner-deployment

This ansible playbook can be used to automize the deployment of [Hetzner servers](https://www.hetzner.de/) with Debian Stretch. It uses the `installimage` script that is provided by Hetzner inside of their rescue system.

## What does it do?
Primary it calls the `installimage` script which you can find in a running rescue system of Hetzner. Usually you have to configure the script manually in an editor. This playbook answers all questions as parameters for a fully automated installation. It also reboots the server after the installation.

In its current state the playbook makes a lot of assumptions about your desired configuration.

1. The inventory hostname is used as the new hostname.
2. grub is installed as bootloader.
3. *sda* and *sdb* are configured as a level 1 software raid.
4. All partitions are ext4.
5. 1GB for */boot* (Yes that is a lot...)
6. 50GB for */*
7. A swap partition half as big as your RAM.
8. The rest of the disks is configured as a LVM group with the name `vm`

To force the `installimage` script to create a volume group without any volumes the playbook creates a dummy volume and removes it after the installation is complete. Of course this is a bad way to do this but it works for now.

## "But this playbook should be more flexible!?"
Yes, you are right! In the current state it works for my personal use cases. If my requirements change in the future I will update the playbook accordingly. If you use this playbook and update it to make it more configurable then please open a pull request! :)

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
8. This will install Debian Stretch and wait for the server to be rebooted. In my tests it took about three minutes.
9. Login to your server with the `root_key` SSH key.

## Disclaimer
I do not work for Hetzner and this software is in no official way connected to Hetzner.
