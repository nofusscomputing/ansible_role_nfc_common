---
title: Common
description: No Fuss Computings Ansible role nfc_common
date: 2023-10-25
template: project.html
about: https://gitlab.com/nofusscomputing/projects/ansible/roles/common
---

This role is designed to be included in all playbooks as it offers features to dynamically setup the ansible environment.



## Role Details

| Item| Value | Description |
|:---|:---:|:---|
| Dependent Roles | _None_ | |
| Gate Variables | `common_gather_facts`  | _Optional, default not defned which is the same as `false`, gather Ansible facts._|
| Idempotent | _Yes`*`_ | All except when configuring dns. |
| Stats Available | _Not Yet_ | Available under yaml path `nfc_common`. |
| Tags | _Nil_ | If you specify tags for running your playbooks, if this roll is included all tasks will still run as if tag `always` was specified. |


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

- setting the hostname

- DNS. Installs resolvconf and configures.

- [Host details collection for use with automations](#Stat%20Collection)

- updating hosts file

- setting up network interfaces with either static or dynamic settings

- installing software via aptitude

- setting common variables for use


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


### Stat Collection

As part of the initial run-once tasks the role collects the details of the host(s) within the inventory. The information gathered is intended to aid in automations with all host information available under the `hosts` object.

stat output example

``` json
{
  "hosts": {
    "{inventory_hostname}": {
      "manufacturer": "",
      "model": "",
      "serial_number": "",
      "uuid": ""
    },
    "next_host": {} // etc
  }
}
```

!!! info "AWX / Ansible Automation Platform"
    As this role is designed to work with AWX/Ansible Automation Platform the stats are not `per_host` as this is not supported in AWX.


## todo

- iterate through kubernetes hosts `hostvars['hostname'].{whatever ipaddress is}` to update each nodes hosts file. ref <https://stackoverflow.com/a/40029455>

- add ability to set timezone
