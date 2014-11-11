This is an [Ansible](http://www.ansible.com/home) role for installing
[PHP-FPM](http://php-fpm.org/).

# What it Does

## Software

Installs the `php5-fpm` package.

It also deletes the default (`www`) resource pool.  See the usage
section below for how to create your own pools.  You can disable this
feature by setting `php_fpm_delete_default_pool` to `false`.

## Configuration & Logging

Creates the files:

* `/etc/php5/fpm/php.ini` -- configuration file for PHP

## Services

Provides the service `php5-fpm` but it won't start running until you
define a pool (or unless you allowed the default pool to persist).

# Usage

Use the role in a playbook like this:

```yaml
- hosts: all
  roles:
    - php-fpm
```

To set up your own pool, you can use the provided template:

```yaml
- name: Create PHP-FPM pool for MyApp
  template: src=${PHP-FPM}/templates/pool.conf.j2 dest={{ php_fpm_conf_dir }}/pool.d/my-app.conf owner=root group=root mode=0644
  notify:
    - restart php-fpm
  tags:
    - my-app
    - php-fpm
	- php
  with_items:
    - name:        my-app
      root:        /srv/my-app
      user:        www-data
      group:       www-data
      listen:      /srv/my-app/tmp/php5-fpm.sock"
      status_path: /_fpm_status
      log_dir:     /var/log/my-app
```

See the complete list of variables below.

## Variables

The following variables are exposed for configuration:

* `php_fpm_delete_default_pool` -- set to `false` to disable deleting the default (`www`) PHP-FPM resource pool
* `php_fpm_post_max_size` -- PHP post max size (default: 8M)
* `php_fpm_max_execution_time` -- PHP max execution time (default: 30)
* `php_fpm_max_input_time` -- PHP max input time (default: 60)
* `php_fpm_memory_limit` -- PHP memory limit (default: 128M)
* `php_fpm_time_zone` -- PHP time zone (default: UTC)
* `php_fpm_upload_max_file_size` -- PHP upload max file size (default: 2M)

When using the `pool.conf.j2` template (see above), the following
variables are exposed:

* `name` -- name of the pool
* `root` -- change to this directory before starting processes
* `user` -- user of the processes in the pool
* `group` -- group of the processes in the pool
* `listen` -- either a port or path for a UNIX socket
* `status_path` -- expose the PHP-FPM status path
* `ping_path` -- expose the PHP-FPM ping path
* `log_dir` -- the log directory for PHP-FPM processes
