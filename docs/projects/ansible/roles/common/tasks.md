---
title: Role Tasks
description: No Fuss Computings Ansible role nfc_common tasks
date: 2023-11-17
template: project.html
about: https://gitlab.com/nofusscomputing/projects/ansible/roles/common
---

This page details the tasks that this Ansible role runs.


=== "Time Sync/Zone"

    Configuration of time sync with an NTP server and the NTP servers to sync from is available within tis role. By default this role will install a time sync service `systemd-timesyncd`, configure the timezone to be `UTC` and enable NTP sync. Customization of the timezone and ntp servers is available by setting the following variables.

    ``` yaml
    hostConfig:
      config:
        timezone: UTC    # Optional, string. A value as found from command `timedatectl list-timezones`
        ntp:             # Optional, list. list of NTP server to sync from.
          - pool.ntp.org
    ```

    !!! tip
        This configuration will only be run once. where ever you first include the role nfc_common is when this configuration will be set. including this role again will not run this section of the role.
