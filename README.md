MySQL cookbook
=====================

The MySQL cookbook exposes the `mysql_service` and `mysql_client`
resources. These resources are utilized by the `mysql::client`
and `mysql::server` recipes, or can be consumed in other recipes by
depending on the MySQL cookbook.

This cookbook does its best to follow platform native idioms at all
times. This means things like logs, pid files, sockets, and service
managers work "as expected" by an administrator familiar with a given
platform.

Scope
-----
This cookbook is concerned with the "MySQL Community Server",
particularly those shipped with F/OSS Unix and Linux distributions. It
does not address forks and value-added repackaged MySQL distributions
like Drizzle, MariaDB, or Percona.

This cookbook does not try to encompass every single configuration
option available for MySQL. Instead, it provides a "just enough" to
get a MySQL server running, then allows the user to specify additional
custom configuration.

Requirements
------------
* Chef 11 or higher
* Ruby 1.9 (preferably from the Chef full-stack installer)

Resources / Providers
---------------------
### mysql_service

The `mysql_service` resource configures the basic plumbing
needed to run a simple mysql_service with a minimal configuration.

### Example

    mysql_service 'default' do
      version '5.5'
      port '3307'
      data_dir '/data'
      template_source 'custom.erb'
      action :create
    end

The `version` parameter will allow the user to select from the
versions available for the platform, where applicable. When omitted,
it will install the default MySQL version for the target platform.
Available version numbers are `5.0`, `5.1`, `5.5`, and `5.6`,
depending on platform. See PLATFORMS.md for details.

The `port` parameter determines the listen port for the mysqld
service. When omitted, it will default to '3307'.

The `data_dir` parameter determines where the actual data files are
kept on the machine. This is useful when mounting external storage.
When omitted, it will default to the platform's native location.

The `template_source` parameter allows the user to override the
default minimal template used by the `mysql_service` resource. When
omitted, it will select one shipped with the cookbook based on the
MySQL version.

### mysql_client

The `mysql_client` resource installs or removes the MySQL client binaries and
development libraries

Recipes
-------
### mysql::server

This recipe calls a `mysql_service` resource, passing parameters
from node attributes.

### mysql::client

This recipe calls a `mysql_client` resource, with action :create

Usage
-----
The `mysql::server` recipe and `mysql_service` recipes are designed to
provide a minimal configuration. The default `my.cnf` dropped off has
an `!includedir` directive. Site-specific configuration should be
placed in the platform's native location.

### run_list

Include `'recipe[mysql::server]'` or `'recipe[mysql::client]'` in your run_list.

### Wrapper cookbook for a server instance

    node.set['mysql']['server_root_password'] = 'yolo'
    node.set['mysql']['port'] = '3308'
    node.set['mysql']['data_dir'] = '/data'
    
    include_recipe 'mysql::server'

    template '/etc/mysql/conf.d/mysite.cnf' do
      owner 'mysql'
      owner 'mysql'      
      source 'mysite.cnf.erb'
      notifies :restart, 'mysql_service[default]'
    end

### Used directly in a recipe

    template '/etc/mysql/conf.d/mysite.cnf' do
      owner 'mysql'
      owner 'mysql'      
      source 'mysite.cnf.erb'
      notifies :restart, 'mysql_service[default]'
    end

    mysql_service 'default' do
      version '5.5'
      port '3307'
      data_dir '/data'
      template_source 'custom.erb'
      action :create
    end

Attributes
----------

    default['mysql']['service_name'] = 'default'
    default['mysql']['server_root_password'] = 'ilikerandompasswords'
    default['mysql']['server_debian_password'] = 'postinstallscriptsarestupid'
    default['mysql']['data_dir'] = '/var/lib/mysql'
    default['mysql']['port'] = '3306'

    ### used in grants.sql
    default['mysql']['allow_remote_root'] = false
    default['mysql']['remove_anonymous_users'] = true
    default['mysql']['root_network_acl'] = nil

License & Authors
-----------------
- Author:: Joshua Timberman (<joshua@opscode.com>)
- Author:: AJ Christensen (<aj@opscode.com>)
- Author:: Seth Chisamore (<schisamo@opscode.com>)
- Author:: Brian Bianco (<brian.bianco@gmail.com>)
- Author:: Jesse Howarth (<him@jessehowarth.com>)
- Author:: Andrew Crump (<andrew@kotirisoftware.com>)
- Author:: Christoph Hartmann (<chris@lollyrock.com>)
- Author:: Sean OMeara (<someara@opscode.com>)

```text
Copyright:: 2009-2014 Chef Software, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
