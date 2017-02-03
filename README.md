# Logrotate Role for Ansible

[![Build Status](https://travis-ci.org/petemcw/ansible-role-logrotate.svg?branch=master)](https://travis-ci.org/petemcw/ansible-role-logrotate)

This role installs Logrotate which helps to manage your log files by periodically minimizing, backing up, and removing them among other things.

## Requirements

This role requires [Ansible](http://www.ansibleworks.com/) version 2.0 or higher and the Debian/Ubuntu/CentOS/RedHat platforms.

## Role Variables

The variables that can be passed to this role and a brief description about
them are as follows:

```yaml
# Default configuraiton options
logrotate_default_options:
  - "weekly"
  - "rotate 4"
  - "create"
  - "dateext"
  - "compress"

# Default rotation script entries
logrotate_scripts:
  - name: "tmp"
    entries:
      - name: "wtmp"
        comment: "No package owns wtmp, logrotate will manage"
        path: "/var/log/wtmp"
        options:
          - "monthly"
          - "missingok"
          - "create 0664 root utmp"
          - "minsize 1M"
          - "rotate 1"
      - name: "btmp"
        comment: "No package owns btmp, logrotate will manage"
        path: "/var/log/btmp"
        options:
          - "monthly"
          - "missingok"
          - "create 0600 root utmp"
          - "rotate 1"
```

## Examples

1. Install Logrotate with a single entry defined:

    ```yaml
    ---
    # This playbook installs Logrotate

    - name: Install Logrotate to all nodes
      hosts: all
      roles:
        - logrotate
      vars:
        logrotate_scripts:
          - name: nginx
            path: /var/log/nginx/*.log
            options:
              - weekly
              - size 25M
              - rotate 7
              - missingok
              - compress
              - delaycompress
              - copytruncate
            postrotate: "[ -s /run/nginx.pid ] && kill -USR1 `cat /run/nginx.pid`"
    ```

2. Install Logrotate with multiple, complex entries:

    ```yaml
    ---
    # This playbook installs Logrotate

    - name: Install Logrotate to all nodes
      hosts: all
      roles:
        - logrotate
      vars:
        logrotate_scripts:
          - name: phpapp
            path: /var/www/myapp.com/production/logs/*.log
            options:
              - weekly
              - rotate 7
              - missingok
          - name: "rsyslog"
            entries:
              - path: "/var/log/syslog"
                options:
                  - "daily"
                  - "missingok"
                  - "notifempty"
                  - "rotate 7"
                  - "delaycompress"
                postrotate: "invoke-rc.d rsyslog rotate > /dev/null"
              - path:
                  - /var/log/mail.info
                  - /var/log/mail.warn
                  - /var/log/mail.err
                  - /var/log/mail.log
                  - /var/log/daemon.log
                  - /var/log/kern.log
                  - /var/log/auth.log
                  - /var/log/user.log
                  - /var/log/lpr.log
                  - /var/log/cron.log
                  - /var/log/debug
                  - /var/log/messages
                options:
                  - "weekly"
                  - "missingok"
                  - "rotate 4"
                  - "notifempty"
                  - "compress"
                  - "delaycompress"
                  - "sharedscripts"
                postrotate: "invoke-rc.d rsyslog rotate > /dev/null"
    ```

## Dependencies

None.
