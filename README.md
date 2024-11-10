# Role ansible_galaxy_role.users

This role will manage user accounts and individual account sudo access.

At minimum the role will just create an user account and add the ssh keys to the
account authorized_keys file.

## Requirements

Users ssh keys must be one level up: `../sshkeys/username.keys`

## Role Variables

| Parameter | Choices | Description |
|---|---|---|
| `users_list` | [] <-- | **Required**<br>List of users to be managed |
| `sudo_access` | `no` <--<br>`yes` | Install sudoers.d file for the user |
| `delete_users` | `no` <--<br>`yes` | Delete users in the list |

---

### `users_list` details

| Variable | Description |
|---|---|
| `name` | User account name |
| `comment` | User full name |
| `password` | Encrypted password |

The following `users_list` come predefined with the role:
* `users_homeco`

This allows us to create the users in different system stacks or repos
without defining the same users in all places.

###### Example `users_list`
```yaml
users_list:
  - name: jdoe
    comment: "Joe Doe"
    password: '$6$H40MJbBt$cQXs5YDJyJBEDJh1zy1wFD'
    
  # Mary's password is disabled. She will sudo to root without a password.
  - name: msue
    comment: "Mary Sue"
    password: '!'
```
---

## Example Playbook

```yaml
- name: PlaybookName
  hosts: all
  roles:
    - { role: ansible_galaxy_role.users, users_list: '{{users_homeco}}', sudo_access: 'yes' }
    - { role: ansible_galaxy_role.users, users_list: '{{users_local}}' }
    - { role: ansible_galaxy_role.users, users_list: '{{users_old}}', delete_users: 'yes' }
```
`users_homeco` is defined in the role, see ./defaults/main.yml.

`users_local` and `users_old` should be defined in `group_vars/all/main.yml` file.

Accounts described in `users_local` will be created without sudo access.<br>
Accounts described in `users_old` will be deleted, including the corresponding
/etc/sudoers.d/20-username file if it exists.
