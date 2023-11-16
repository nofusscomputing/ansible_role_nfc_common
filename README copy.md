# common role

This role is designed to be included in all playbooks as it offers features to dynamically setup the ansible environment.

when adding this role to a playbook, ensure that `gather_facts` is set to `no` for the play. this role gathers the facts for you. i.e.

Playbook example

``` yaml
---
- hosts: "{{ groups.servers }}"
  gather_facts: no

  roles:

    - name: Common tasks
      role: nfc_common
      vars:
        common_gather_facts: true

# Additional roles or playbook_include below this line
```

!!! tip
    If the variable `common_gather_facts` is not set to `true`, by default facts will not be gathered.

## Features

The following feaatures are available for this role:

- SSH Key setup
- setting the hostname
- updating hosts file
- setting up network interfaces with either static or dynamic settings
- installing software via aptitude
- setting common variables for use

### SSH Key setup

This role will setup the ssh connection to a host. This plugin will dynamically set `ansible_user` as `deploy` and use the ssh certificate for that user to connect to the host. if the host has never had the play run on it before, as long as the expectations are met; as per below. when running ansible from the cli specify in addition the following args `-kK --extra-vars "init=true"`. These arguments bring up the password dialog for the SSH connection and sudo and tell this role that it's an `init` connection, meaning first run.

For SSH connection to a host to be configured the following is expected for this feature to work:

- Clean host that has never had the play run on it

  - OpenSSH Server is installed and password authentication is enabled (OpenSSH defaults to this)

  - A user has been setup on the system with sudo access

  - within the hosts var file the following variables have been set

    - `ansible_host` set to the IP address (for static IP) or DNS name of the host

    - `ansible_user` set to the user that has been configured on the host

    - `ansible_connection` with a value of `ssh`

- A host that has already had the play run on it at least once

  - no further action required, just don't specify any SSH parameters during the play.


### APT

to setup and use apt to either add a repository or install/remove software the following variable template is required

``` yaml
aptSigningKeys:
      - name: docker
        url: https://download.docker.com/linux/debian/gpg
        save_directory: /usr/share/keyrings
        file_extension: asc # file extension to save key as, if `.gpg` specify `.gpg` otherwise specify `.asc` by default

    aptRepositories:
      - name: kubernetes # repo name
        repo: deb [signed-by=/usr/share/keyrings/kubernetes.gpg] https://apt.kubernetes.io/ kubernetes-xenial main # repo file entry

    aptInstall:
      - name: containerd.io # name of apt package
        version: "{{ ContainerDioVersion }}" # optional: if specified, will install that version and lock it from auto update.

```

### Common Variables

This role sets the following variables:

- `host_vars[{hostname}].dynamic_processor_architecture` to the processor architecture, either `amd64`, `arm64` or `x86`.

## todo

- iterate through kubernetes hosts `hostvars['hostname'].{whatever ipaddress is}` to update each nodes hosts file. ref <https://stackoverflow.com/a/40029455>

- add ability to set timezone
