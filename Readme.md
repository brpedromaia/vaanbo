# Vaanbo - The simple way to provision local virtual machines

## Synopsis

Vaanbo (**Va**grant **An**sible **Bo**otstrap) is a simple way to provision virtual machines on virtualbox and to configure using ansible playbook.

To stars ansible playbooks there is a virtual machine called "orchestrator" witch plays that using yml files into playlist folder.

## Motivation

To provision some labs on computer, I don't like to make many vagranfiles and to solve that, I'm developing it to brings me fast provisions and fast configuration.

## Requiremnets
**Linux or MacOS**

git

bash 4

vagrant >= 2.2.9

vagrant plugin install vagrant-hostmanager

ssh


## How's Provisionament Works

The provisionament works on **playlist** folder (on root folder) getting the yml's list in sequence.

## Yml File Example (Nexus on playlists/0001_Nexus.yml)

```yaml
name: nexus
provision: # provisioning step (using vagrant)
  provider: virtualbox
  box: "bento/centos-8"
  ram: 2048
  vcpu: 2
  instances: 
    - instance_name: nexus-master
      type: "private" # dhcp / public / private
      ip: "10.44.44.103" 
      hd: 12GB
  command_line:  # (NOT REQUIRED) to execute some commandline after has provisioned.
      - "sudo yum install -y epel-release"
config: # cofiguring step (using ansible on orchestration vm)
  repository: https://github.com/brpedromaia/nexus-ansible-roles
  playbooks: 
    setup: # ansible playbook name
      tags: # tags list
        - install
    config: # ansible playbook name (without tags works as well)
```

## License

MIT

## Running

```bash
git clone https://github.com/brpedromaia/vaanbo.git
cd vaanbo
#Execute the command bellow to get help information
bash bin/vaanbo
```




## References
- [Ansible Docs](https://docs.ansible.com/)
- [Virtualbox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/docs)
- [Bash](https://www.gnu.org/software/bash/manual/bash.html)
- [Creating Ssh keys](https://confluence.atlassian.com/bitbucketserver/creating-ssh-keys-776639788.html)
