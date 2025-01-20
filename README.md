# User Management Role

An Ansible role for managing multiple user accounts, home directories, and sudo privileges on Linux systems.

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
  - username: example_user    # Required
    state: present           # Optional: present/absent (default: present)
    shell: /bin/bash        # Optional (default: /bin/bash)
    generate_ssh_key: true  # Optional (default: true)
    append_groups: true     # Optional (default: true)
    groups: []              # Optional: list of additional groups
    home_mode: "0775"       # Optional (default: "0775")
    public_html: true       # Optional (default: true)
    public_html_mode: "0755" # Optional (default: "0755")
    sudo_enabled: false     # Optional (default: false)
    sudo_commands: []       # Optional: list of allowed sudo commands
```

### Default Values

You can override these default values for all users:

```yaml
default_user_shell: /bin/bash
default_user_state: present
default_user_generate_ssh_key: true
default_user_append_groups: true
default_user_home_mode: "0775"
default_user_public_html: true
default_user_public_html_mode: "0755"
default_user_sudo_enabled: false
default_user_sudo_commands: []
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: servers
  roles:
    - role: farisc0de.users
      vars:
        users:
          - username: john_doe
            groups: ['developers']
            sudo_enabled: true
            sudo_commands:
              - "/usr/bin/apt"
              - "/usr/bin/dnf"
          
          - username: jane_doe
            shell: /bin/zsh
            groups: ['developers', 'docker']
            public_html: false
            
          - username: service_account
            state: absent  # Remove this user
```

## Role Tags

- `user`: All user management tasks
- `user_creation`: User creation tasks
- `home`: Home directory configuration tasks
- `sudo`: Sudo privileges configuration tasks

## License

MIT

## Author

Faris Alotaibi
