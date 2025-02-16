nanomsg-role
=====

## An [ansible role](https://docs.ansible.com/ansible/playbooks_roles.html#roles) for [nanomsg](https://github.com/nanomsg/nanomsg)

Input Variables
-----

- **Name**
  - `nanomsg_role_nanomsg_version`
- Value
  - The version of nanomsg to install. Defaults to "1.0.0"
- Usage
  ```yaml
  nanomsg_role_nanomsg_version: "1.0.0"
  ```

Ansible Role Development with Vagrant
---

Requirements
-----
- Install [Vagrant >= 2.0.0 ](https://www.vagrantup.com/downloads.html) flavor for you host operating system.
- Install the [vagrant-env](https://github.com/gosuri/vagrant-env) vagrant plugin. A vagrant plugin to load environment variables from .env into ENV
  ```shell
  vagrant plugin install vagrant-env
  ```
- Default virtualization is configured with [**VirtualBox**](https://www.virtualbox.org).
  - [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads).
  - Install VirtualBox for your host operating system.
  - Install the [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) vagrant plugin. A Vagrant plugin to keep your VirtualBox Guest Additions up to date.
    ```shell
    vagrant plugin install vagrant-vbguest
    ```
- If deploying to [**Azure**](https://portal.azure.com/), install the [vagrant-azure](https://github.com/Azure/vagrant-azure) vagrant plugin. A Vagrant plugin to enable Vagrant to manage virtual machines in Microsoft Azure.
  ```shell
  vagrant plugin install vagrant-azure
  ```

Getting Started
----
- Copy `sample.env` and make a local environment script `.env`.
  > Note: `*.env` is present in the `.gitignore` file, with the explicit exception for `sample.env`.
  ```shell
  # bash
  cp ./sample.env ./.env

  # windows
  copy sample.env .env
  ```
- Update `.env` with your own settings.
  - Ensure you have your personal public and private RSA keypair located `~/.ssh/id_rsa`. Otherwise update the `RSA_PUB_KEY` variable in this file to point to the correct path for your key pair. We you do not have or use an RSA key pair for security you can [follow these simple instructions](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/.

Local Ansible Role Development 
-----
- This project is setup as a [multi-machine vagrant environment](https://www.vagrantup.com/docs/multi-machine/). If you are new to this project start by viewing the configured vagrant vms.
  ```shell
  vagrant status
  ```
- You can optionally check the status of a specific vm instance
  ```shell
  vagrant status vbox-ubuntu-16.04

  # or 

  vagrant status azure-ubuntu-16.04
  ```

Local Ansible Role Development with Vagrant/VirtualBox
-----
- Create and provision an image. Provisioning will provision the ansible role using the `ansible_local` vagrant provisioner.
  ```shell
  vagrant up vbox-ubuntu-16.04 --provision

  # PLAY RECAP *********************************************************************
  # vbox-ubuntu-16.04          : ok=13   changed=8    unreachable=0    failed=0
  ```
- Connect to new vm image via ssh to diagnose any issues.
  ```shell
  vagrant ssh vbox-ubuntu-16.04

  # Poke around and then exit when finished.
  exit
  ```
- Reload the vm and reprovision with ansible role. If desired state is achieved with your role subsequent reloads should yield `changed=0` results.
  ```shell
  vagrant reload vbox-ubuntu-16.04 --provision-with=ansible_local

  # PLAY RECAP *********************************************************************
  # vbox-ubuntu-16.04          : ok=6    changed=0    unreachable=0    failed=0
  ```
- Connect to the vm via ssh.
  ```shell
  vagrant ssh vbox-ubuntu-16.04
  ```
- Bring virtual machine to starting state. Save some space and destroy the vm.
  ```shell
  vagrant destroy -f vbox-ubuntu-16.04
  ```

Azure Ansible Role Development with Vagrant
-----
- Install Azure Vagrant Plugin
  ```shell
  vagrant plugin install vagrant-azure --plugin-version '2.0.0.pre8'
  ```
- Add Azure Dummy Box
  ```shell
  vagrant box add azure https://github.com/azure/vagrant-azure/raw/v2.0/dummy.box
  ```
- You must have an App Registrant setup for your azure account, [instructions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). This will be needed for the next step.
- Update `./.env` with your own azure values, public rsa key, azure region, etc. 
- Use Vagrant to provision azure provider image.
  ```shell
  vagrant up azure-ubuntu-16.04 --provision

  # PLAY RECAP *********************************************************************
  # azure-ubuntu-16.04      : ok=13   changed=8    unreachable=0    failed=0
  ```
- Reload the vm and reprovision with ansible role. If desired state is achieved with your role subsequent reloads should yield `changed=0` results.
  ```shell
  vagrant reload azure-ubuntu-16.04 --provision-with=ansible_local

  # PLAY RECAP *********************************************************************
  # azure-ubuntu-16.04      : ok=6    changed=0    unreachable=0    failed=0
  ```
- Connect to the vm via ssh.
  ```shell
  vagrant ssh azure-ubuntu-16.04
  ```
- Destroy existing azure image to save some fiat.
  ```shell
  vagrant destroy -f
  ```
- Destroy vm and .
  ```shell
  vagrant destroy -f
  ```

License
-----
See the [LICENSE](LICENSE.md) file for license rights and limitations (MIT).


Donations Accepted:
-----
- BTC: 1PrMDWtCCB4HutiEvFZx3X7pW3PQma3G8u
- LTC: LR2XX2KegbEBp6DTU9kPBRdPmVW8qhQafu
- KMD: RL7Dykpozn3ahJrNbG1gfq8mvWHSeyqZ2w
