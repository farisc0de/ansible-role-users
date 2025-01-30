# User Management Role

An Ansible role for managing multiple user accounts, home directories, SSH configuration, and sudo privileges on Linux systems.

## Requirements

- Ansible version 2.9 or higher
- Target systems running:
  - Ubuntu (all versions)
  - Debian (all versions)
  - Enterprise Linux (all versions)

## Role Variables

### User Configuration

The role uses a list of users to manage multiple accounts. Each user can have individual settings:

```yaml
users:
  - username: example_user     # Required
    state: present            # Optional: present/absent (default: present)
    shell: /bin/bash         # Optional (default: /bin/bash)
    home: /home/example_user # Optional (default: /home/username)
    system: false            # Optional: create as system user (default: false)
    append_groups: true      # Optional (default: true)
    groups: []               # Optional: list of additional groups
    home_mode: "0750"        # Optional (default: "0750")
    public_html: false       # Optional (default: false)
    public_html_mode: "0755" # Optional (default: "0755")
    generate_ssh_key: false  # Optional: generate SSH key pair (default: false)
    sudo:                    # Optional: sudo configuration
      enabled: false         # Optional (default: false)
      commands:             # Optional: list of allowed sudo commands
        - "/usr/bin/apt"
        - "/usr/bin/systemctl restart service"
    ssh:                     # Optional: SSH configuration
      enabled: true          # Optional (default: true)
      dir_mode: "0700"       # Optional (default: "0700")
      authorized_keys_mode: "0600"  # Optional (default: "0600")
      authorized_keys:       # Optional: list of SSH keys
        - key: "{{ variable_name }}"  # SSH key content from a variable
        - src: "{{ ssh_keys_path }}/user.pub"  # Path to public key file
      config:               # Optional: SSH client configuration
        enabled: false      # Optional (default: false)
        config_mode: "0600" # Optional (default: "0600")
```

### Security Features

The role implements several security best practices by default:

- Home directories are created with `0750` permissions (owner can read/write/execute, group can read/execute)
- SSH directories are created with `0700` permissions (only owner can access)
- SSH keys and config files use `0600` permissions (only owner can read/write)
- Public HTML directories are disabled by default
- SSH key generation is disabled by default
- Sudo access is disabled by default
- System user creation is supported via the `system` parameter

## Dependencies

None

## Example Playbook

```yaml
- hosts: servers
  vars:
    john_ssh_key: "ssh-rsa AAAAB3NzaC1..."  # Define SSH key content
    ssh_keys_path: "/path/to/ssh/keys"      # Define path to SSH keys
  roles:
    - role: farisc0de.users
      vars:
        users:
          - username: john_doe
            groups: ['developers']
            sudo:
              enabled: true
              commands:
                - "/usr/bin/apt"
                - "/usr/bin/dnf"
            ssh:
              enabled: true
              authorized_keys:
                - key: "{{ john_ssh_key }}"
                - src: "{{ ssh_keys_path }}/john.pub"
          
          - username: jane_doe
            shell: /bin/zsh
            groups: ['developers', 'docker']
            public_html: true
            ssh:
              enabled: true
              authorized_keys:
                - src: "{{ lookup('env', 'HOME') }}/.ssh/id_rsa.pub"
              config:
                enabled: true
            
          - username: service_account
            system: true
            shell: /sbin/nologin
            home: /opt/service
            home_mode: "0700"
            
          - username: old_user
            state: absent  # Remove this user
```

## License

MIT

## Author Information

Created by [farisc0de](https://github.com/farisc0de)
