# Steps to execute this Playbook:

- Make sure you hav ansible is installed pon your local machine either directly using "brew update && brew install ansible" if your OS is MAC or follow this guide (https://www.ansible.com/integrations/infrastructure/windows) for executing on windows OS.
- Get the users ssh public key by executing "cat ~/.ssh/id_rsa.pub".
- If no such file is present, execute "ssh-keygen" (or) follow https://www.ssh.com/ssh/keygen/ to generate an ssh-key to authorise your system with the target server on which the MySQL is to installed.
- Add this key in the ~/.ssh/authorized_keys file in the target server where this play book will be executed.
- In the inventories/production/database file, replace the IP and the ansible_port values with the target machines IP(public IP using "ifconfig" or "hostname -i" command) and ssh port (usually 22)
- Execute this command : "ansible-playbook database.yml" to execute the playbook and append -vvv like "ansible-playbook database.yml -vvv" to check all the execution logs with verbose.


* This will execute all the roles tasks defined for creating database, user and restore the backup into the database as required.

##################################### EXPLINATION #####################################

** Using MariaDB, a database that is forked out of MySQL and is better supported and highly secured. Same can be accomplished by replacing Mariadb with MySQL parameters or even simply by using a highly used ansible-galaxy role like mysql supported by Geerlingguy(https://github.com/geerlingguy/ansible-role-mysql/tree/master/vars).
**

# Ansible role for `mariadb` is as follows:

An Ansible role for managing MariaDB in RedHat/CentOS-based distributions. Specifically, the responsibilities of this role are to:

- Install MariaDB packages from the official MariaDB repositories
- Remove unsafe defaults:
    - set database root password (remark that once set, this role is unable to *change* the database root password)
    - remove anonymous users
    - remove test database
- Create users and databases
- Manage configuration files `network.cnf`, and `server.cnf`

This role supports InnoDB as storage engine.

## Requirements

No specific requirements

## Role Variables

None of the variables below are mandatory. When not defined by the user, the [default values](defaults/main.yml) are used.

### Basic configuration

| Variable                | Default     | Comments                                                                                                    |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------|
| `mariadb_bind_address`  | '127.0.0.1' | Set this to the IP address of the network interface to listen on, or '0.0.0.0' to listen on all interfaces. |
| `mariadb_databases`     | []          | List of dicts specifyint the databases to be added. See below for details.                                  |
| `mariadb_init_scripts`  | []          | List of dicts specifying any scripts to initialise the databases. Se below for details. ta                  |
| `mariadb_port`          | 3306        | The port number used to listen to client requests                                                           |
| `mariadb_root_password` | ''          | The MariaDB root password. **It is highly recommended to change this!**                                     |
| `mariadb_swappiness`    | 0           | "Swappiness" value. System default is 60. A value of 0 means that swapping out processes is avoided.        |
| `mariadb_users`         | []          | List of dicts specifying the users to be added. See below for details.                                      |
| `mariadb_version`       | '10.2'      | The version of MariaDB to be installed. Default is the current stable release.                              |

### Server configuration

The variables below are set in `/etc/my.cnf.d/server.cnf`, specifically in the `[mariadb]` section. For more info on the values, read the [MariaDB Server System Variables documentation](https://mariadb.com/kb/en/mariadb/server-system-variables/).

| Variable                                 | Default   | Comments                                                                                  |
|:-----------------------------------------|:----------|:------------------------------------------------------------------------------------------|
| `mariadb_innodb_buffer_pool_instances`   | 8         |                                                                                           |
| `mariadb_innodb_buffer_pool_size`        | 384M      |                                                                                           |
| `mariadb_innodb_file_format`             | Barracuda |                                                                                           |
| `mariadb_innodb_file_format_check`       | 1         |                                                                                           |
| `mariadb_innodb_file_per_table`          | ON        |                                                                                           |
| `mariadb_innodb_flush_log_at_trx_commit` | 1         |                                                                                           |
| `mariadb_innodb_log_buffer_size`         | 16M       |                                                                                           |
| `mariadb_innodb_log_file_size`           | 48M       |                                                                                           |
| `mariadb_innodb_strict_mode`             | ON        |                                                                                           |
| `mariadb_join_buffer_size`               | 128K      |                                                                                           |
| `mariadb_log_warnings`                   | 1         | Log critical warnings. Set to 0 to turn off, or greater than 1 for more verbose logging.  |
| `mariadb_long_query_time`                | 10        |                                                                                           |
| `mariadb_max_allowed_packet`             | 16M       |                                                                                           |
| `mariadb_max_connections`                | 505       |                                                                                           |
| `mariadb_max_heap_table_size`            | 16M       |                                                                                           |
| `mariadb_max_user_connections`           | 500       |                                                                                           |
| `mariadb_port`                           | 3306      |                                                                                           |
| `mariadb_query_cache_size`               | 0         | The query cache is disabled by default. Set to a nonzero value to enable the query cache. |
| `mariadb_read_buffer_size`               | 128K      |                                                                                           |
| `mariadb_read_rnd_buffer_size`           | 256k      |                                                                                           |
| `mariadb_skip_name_resolve`              | 1         | Use IP addresses only. Set to 0 to resolve host names.                                    |
| `mariadb_slow_query_log`                 | 0         | Set to 1 to enable the slow query log.                                                    |
| `mariadb_sort_buffer_size`               | 2M        |                                                                                           |
| `mariadb_table_definition_cache`         | 1400      |                                                                                           |
| `mariadb_table_open_cache`               | 2000      |                                                                                           |
| `mariadb_table_open_cache_instances`     | 8         |                                                                                           |
| `mariadb_tmp_table_size`                 | 16M       |                                                                                           |

### Adding databases

Databases are defined with a dict containing the fields `name:` (required), and `init_script:` (optional). The init script is a SQL file that is executed when the database is created to initialise tables and populate it with values.

```Yaml
mariadb_databases:
  - name: chembl_23
```

### Adding users

Users are defined with a dict containing fields `name:`, `password:`, `priv:`, and, optionally, `host:`. The password is in plain text and `priv:` specifies the privileges for this user as described in the [Ansible documentation](http://docs.ansible.com/mysql_user_module.html). An example:

```Yaml
mariadb_users:
  - name: chembl
    password: devops
    priv: '*.*:ALL,GRANT'
    host: 'localhost'
```

## Dependencies

No dependencies.
