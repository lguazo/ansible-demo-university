# nvm

Installs Node version Manager tool on Debian/Ubuntu and RHEL/CentOS systems

## Role Variables

| Variable     | Default value | Required | Description                                                                                                  |
| ------------ | ------------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| nvm_version  | v0.35.3       | Yes      | NVM version that will be installed                                                                           |
| nvm_profile  | .bashrc       | No       | Profile where the role is going to put the lines to load nvm                                                 |
| node_version | v12.16.3      | Yes      | Node.js version to install                                                                                   |
| install      | yes           | No       | Flag that indicates which tasks should run the role. If you need to uninstall nvm, then set this var to `no` |

## Example Playbook

### Install

    - hosts: servers
      roles:
         - { role: nvm, install: yes }

### Uninstall

    - hosts: servers
      roles:
         - { role: nvm, nvm_profile: .zshrc, install: no }
