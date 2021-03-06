Puppet Tomcat Module
========================

Introduction
-----------------

This Puppet module install Tomcat (customized or clean) and make possible to deploy packages on it

Installation
-----------------

Clone this repository in a tomcat directory in your puppet module directory

```shell
	git clone https://github.com/oscerd/puppet-tomcat-module tomcat
```

Usage
-----------------

If you include the tomcat::setup class by setting source_mode to `web` the module will download the package, extract it and move it 
in a specific directory. If you set the source_mode `local` the tomcat package must be place in `/tomcat/files/` 
folder. The module will do the same operations without download the package. If you need to deploy a war directly, you can use tomcat::deploy. The war must be placed in `/tomcat/files/`.
For more information about the parameters definition see Parameters section. In this example we refer to a generic sample.war package. This is the sample application from 
Apache Community and it comes from this url: __https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/__

If you want to install tomcat and deploy your package with a specific context you can use this example manifest:

```puppet
	tomcat::setup { "tomcat":
	  family => "7",
	  update_version => "55",
	  extension => ".zip",
	  source_mode => "local",
	  installdir => "/opt/",
	  tmpdir => "/tmp/",
	  install_mode => "custom",
	  data_source => "yes",
	  driver_db => "yes",
	  ssl => "no",
	  users => "yes",
	  access_log => "yes",
	  as_service => "yes",
	  direct_start => "yes"
	  }

	tomcat::deploy { "deploy":
	  war_name => "sample",
	  war_versioned => "no",
	  war_version => "",
	  deploy_path => "/release/",
	  context => "/example",
	  symbolic_link => "",
	  external_conf => "yes",
	  external_dir => "report/",
	  external_conf_path => "/conf/",
	  family => "7",
	  update_version => "55",
	  installdir => "/opt/",
	  tmpdir => "/tmp/",
	  hot_deploy => "yes",
	  as_service => "yes",
	  direct_restart => "no",
	  require => Tomcat::Setup["tomcat"]
	  }
```

Otherwise if you just need to install tomcat and deploy a package, without specify a context you can use this example manifest:

```puppet
	tomcat::setup { "tomcat":
	  family => "7",
	  update_version => "55",
	  extension => ".zip",
	  source_mode => "local",
	  installdir => "/opt/",
	  tmpdir => "/tmp/",
	  install_mode => "custom",
	  data_source => "yes",
	  driver_db => "yes",
	  ssl => "no",
	  users => "yes",
	  access_log => "yes",
	  as_service => "yes",
	  direct_start => "yes"
	  }

	tomcat::deploy { "deploy":
	  war_name => "sample",
	  war_versioned => "no",
	  war_version => "",
	  deploy_path => "/webapps/",
	  context => "",
	  symbolic_link => "",
	  external_conf => "yes",
	  external_dir => "report/",
	  external_conf_path => "/conf/",
	  family => "7",
	  update_version => "55",
	  installdir => "/opt/",
	  tmpdir => "/tmp/",
	  hot_deploy => "yes",
	  as_service => "yes",
	  direct_restart => "no",
	  require => Tomcat::Setup["tomcat"]
	  }
```

If your package is versioned and you want to specify a context, you can use a manifest like this:

```puppet
	tomcat::setup { "tomcat":
	  family => "7",
	  update_version => "55",
	  extension => ".zip",
	  source_mode => "local",
	  installdir => "/opt/",
	  tmpdir => "/tmp/",
	  install_mode => "custom",
	  data_source => "yes",
	  driver_db => "yes",
	  ssl => "no",
	  users => "yes",
	  access_log => "yes",
	  as_service => "yes",
	  direct_start => "yes"
	  }

	tomcat::deploy { "deploy":
	  war_name => "sample",
	  war_versioned => "yes",
	  war_version => "1.0",
	  deploy_path => "/release/",
	  context => "/example",
	  symbolic_link => "yes",
	  external_conf => "yes",
	  external_dir => "report/",
	  external_conf_path => "/conf/",
	  family => "7",
	  update_version => "55",
	  installdir => "/opt/",
	  tmpdir => "/tmp/",
	  hot_deploy => "yes",
	  as_service => "yes",
	  direct_restart => "no",
	  require => Tomcat::Setup["tomcat"]
	  }
```

If you need to undeploy the package of the example you can do in this way:

```puppet

	tomcat::undeploy { "undeploy":
	  war_name => "sample",
	  war_versioned => "yes",
	  war_version => "1.0",
	  deploy_path => "/release/",
	  context => "/example",
	  symbolic_link => "yes",
	  external_conf => "yes",
	  external_dir => "report/",
	  external_conf_path => "/conf/",
	  family => "7"
	  update_version => "55",
	  installdir => "/opt/",
	  as_service => "yes",
	  direct_restart => "yes",
	  require => Tomcat::Deploy["deploy"]
	  }
```
If you need to uninstall the entire tomcat you can do in this way:

```puppet
	tomcat::uninstall { "uninstall":
	  family => "7",
	  update_version => "55",
	  installdir => "/opt/",
	  as_service => "yes",
	  require => Tomcat::Setup["tomcat"]
	  }
```

It's important to define a global search path for the `exec` resource to make module work. 
This should usually be placed in `manifests/site.pp`. It is also important to make sure `unzip` and `tar` command 
are installed on the target system:

```puppet
	Exec {
	  path => "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
	}

	package { 'tar':
	  ensure => installed
	}

	package { 'unzip':
	  ensure => installed
	}
```

For more information about the module settings and module parameters read the instructions following sections.

Parameters
-----------------

The Puppet Tomcat module use the following parameters in his __Setup__ phase

*  __Family__: Possible values of Apache Tomcat version _6_, _7_, _8_ 
*  __Update Version__: The update version
*  __Extension__: The file extension, possible values _.zip_ and _.tar.gz_
*  __Source Mode__: The source mode, possible values _local_ and _web_. Setting _local_ make module search the package in `tomcat/files/` folder. Setting mode _web_ make the module download the specified package
*  __Install Directory__: The directory where the Apache Tomcat will be installed (default is `/opt/`)
*  __Temp Directory__: The directory where the Apache Tomcat package will be extracted (default is `/tmp/`)
*  __Install Mode__: The installation mode, possible values _clean_ and _custom_. With install mode _clean_ the module will only install Apache Tomcat, while with install mode _custom_ the module will install Apache Tomcat with a customizable version of `server.xml`
*  __Data Source__: Define the data source's presence, possible values _yes_ and _no_. If the data source value is _yes_ (and the installation mode value is _custom_ ) then the module will add data source section in `server.xml` and `context.xml`
*  __Driver Db__: Define the presence of database driver to move in tomcat `/lib/` folder from `tomcat/files/` folder, possible values _yes_ and _no_ (default is _no_). If the driver db value is _yes_ (and the data source value is _yes_ and the installation mode value is _custom_ ) then the module will add database driver (.jar o .zip) to tomcat `/lib/` folder. If you want to use the driver you have to 
place it under `tomcat/files/`
*  __SSL__: Define the presence of SSL support, possible values _yes_ and _no_. If the value is _yes_ put the keystore file in `tomcat/files/` and specify the keystore and the parameter in the hiera file.
*  __Access Log__: Defined if Apache Tomcat access log is enabled, possible values _yes_ and _no_ (default is _no_) and it is used in _custom_ installation
*  __As Service__: Define if Tomcat must be a service, possible values _yes_ and _no_ (default is _no_). If the value is _yes_ a .sh file will be created and tomcat will be a service (the commands will be `service tomcat start`, `service tomcat stop`, `service tomcat restart` and `service tomcat status`)
*  __Direct Start__: Define if Tomcat must directly start, possible values _yes_ and _no_ (default is _no_)

The Puppet Tomcat module use the following parameters in his __Deploy__ phase

*  __War Name__: The name of war that have to be deployed
*  __War Versioned__: This variable defines if the deploying war is versioned or not. Possible values _yes_ or _no_ (default is _no_)
*  __War Version__: The version of the deploying war. This variable will be ignored if war versioned value is _no_
*  __Deploy Path__: The location where the war must be placed (default is `/webapps/`) 
*  __Context__: The context of the package we are deploying. If _deploy path_ is different from `/webapps/` then the context will be considered, otherwise it will be skipped.
*  __Symbolic link__: This variable defines if we want a symbolic link related to deploying war. Possible values _yes_ or _no_ (default is _no_). If _symbolic link_ is equal to yes, _deploy path_ is different from `/webapps/`, _war versioned_ is equal to yes and there is a defined _context_, the module will create a symbolic link called _war name_.war pointing to the deploying war. With this feature there will be no need to modify the xml file where the context is defined to change the war. You will only need to change the symbolic link and make it pointing to the new war.
*  __External Conf__: This variable defines if the package we are deploying has an external configuration to install. Possible values _yes_ or _no_ (default is _no_)
*  __External Dir__: The directory that contains the external configuration of the package. The module will search for this directory in `tomcat/files/` folder. If external_conf is equal to _no_, then 
this variable will be ignored. If external_conf is equal to _yes_ this variable must be specified.
*  __External Conf Path__: The Tomcat directory that will contains the external configuration directory of the package. Default value is _/conf/_. If external_conf is equal to _no_, then 
this variable will be ignored. If external_conf is equal to _yes_ this variable must be specified.
*  __Family__: Possible values of Apache Tomcat version _6_, _7_, _8_ 
*  __Update Version__: The update version of Apache Tomcat
*  __Install Directory__: The directory where the Apache Tomcat is installed (default is `/opt/`)
*  __Temp Directory__: The directory where the war must be placed before being moved to _deploy path_ directory (default is `/tmp/`)
*  __Hot Deploy__: Define if Tomcat must be shutdown or not before the deploy, possible values _yes_ and _no_.
*  __As Service__: Define if Tomcat is defined as a service, possible values _yes_ and _no_ (default is _no_). If hot deploy is equal to _no_ then this parameter will be ignored.
*  __Direct Restart__: Define if Tomcat must directly restart, possible values _yes_ and _no_ (default is _no_). If hot deploy is equal to _no_ then this parameter will be ignored. If hot deploy is equal to _yes_ and as service is equal to _yes_ then if this parameter is equal to _yes_ then tomcat will be restarted as service (`service tomcat start`) otherwise it will be restarted normally.

The Puppet Tomcat module use the following parameters in his __Undeploy__ phase

*  __War Name__: The name of war that have to be undeployed
*  __War Versioned__: This variable defines if the undeploying war is versioned or not. Possible values _yes_ or _no_ (default is _no_)
*  __War Version__: The version of the undeploying war. This variable will be ignored if war versioned value is _no_
*  __Deploy Path__: The location where the war is placed (default is `/webapps/`) 
*  __Context__: The context of the package we are undeploying. If _deploy path_ is different from `/webapps/` then the context will be considered, otherwise it will be skipped.
*  __Symbolic link__: This variable defines if there is a symbolic link related to the undeploying war. Possible values _yes_ or _no_ (default is _no_). If _symbolic link_ is equal to yes, _deploy path_ is different from `/webapps/`, _war versioned_ is equal to yes and there is a defined _context_, the module will remove the symbolic link called _war name_.war pointing to the undeploying war.
*  __External Conf__: This variable defines if the package we are undeploying has an external configuration. Possible values _yes_ or _no_ (default is _no_)
*  __External Dir__: The directory that contains the external configuration of the package we are undeploying. If external_conf is equal to _no_, then this variable will be ignored. If external_conf is equal to _yes_ this variable must be specified.
*  __External Conf Path__: The Tomcat directory that contains the external configuration directory of the package we are undeploying. Default value is _/conf/_. If external_conf is equal to _no_, then 
this variable will be ignored. If external_conf is equal to _yes_ this variable must be specified.
*  __Family__: Possible values of the Apache Tomcat installation that contains the package we are undeploying (version _6_, _7_, _8_) 
*  __Update Version__: The update version of the Apache Tomcat installation that contains the package we are undeploying 
*  __Install Directory__: The directory where the Apache Tomcat, that contains the package we are undeploying, is installed (default is `/opt/`)
*  __As Service__: Define if Tomcat is defined as a service, possible values _yes_ and _no_ (default is _no_). If hot deploy is equal to _no_ then this parameter will be ignored.
*  __Direct Restart__: Define if Tomcat must directly restart after the undeploy, possible values _yes_ and _no_ (default is _no_). If as service is equal to _yes_ and this parameter is equal to _yes_ then tomcat will be restarted as service (`service tomcat start`) otherwise it will be restarted normally.

The Puppet Tomcat module use the following parameters in his __Uninstall__ phase

*  __Family__: Possible values of installed Apache Tomcat version _6_, _7_, _8_ 
*  __Update Version__: The update version
*  __Install Directory__: The directory where the Apache Tomcat is installed (default is `/opt/`)
*  __As Service__: Define if Tomcat is installed as a service, possible values _yes_ and _no_ (default is _no_). If the value is _yes_ the etc/init.d/tomcat file will be removed during the uninstallation

Customization
-----------------

When using the _custom_ installation mode, the module will use the template `templates/serverxml.erb` (related to the specific tomcat version) to build a `server.xml` custom file, by using __Hiera__. The module will use the following parameters (listed in tomcat::params class):

```puppet
	# Server.xml parameters
	  
	# Set http port in serverxml.erb
	$http_port = hiera('tomcat::params::http_port')
	  
	# Set https port in serverxml.erb
	$https_port = hiera('tomcat::params::https_port')
	  
	# Set ajp port in serverxml.erb
	$ajp_port = hiera('tomcat::params::ajp_port')
	  
	# Set shutdown port in serverxml.erb
	$shutdown_port = hiera('tomcat::params::shutdown_port')
	  
	# Set connection timeout in http connector in serverxml.erb
	$http_connection_timeout = hiera('tomcat::params::http_connection_timeout')
	  
	# Set max threads in https connector in serverxml.erb
	$https_max_threads = hiera('tomcat::params::https_max_threads')

	# Set the keystore file
	$https_keystore = hiera('tomcat::params::https_keystore')
	  
	# Set the keystore file password
	$https_keystore_pwd = hiera('tomcat::params::https_keystore_pwd')
```

When using the _custom_ installation mode with data source value equal to _yes_, the module will customize `conf/server.xml` and `conf/context.xml` (by using `templates/serverxml.erb` and `templates/context.erb` templates related to the specific tomcat version) to build a data source. The parameters related to data source are the following (listed in tomcat::data_source class):

```puppet
	# Datasource
	  
	# Set Name
	$ds_resource_name = hiera('tomcat::data_source::ds_resource_name')

	# Set MaxActive
	$ds_max_active = hiera('tomcat::data_source::ds_max_active')
	  
	# Set MaxIdle
	$ds_max_idle = hiera('tomcat::data_source::ds_max_idle')
	  
	# Set MaxWait
	$ds_max_wait = hiera('tomcat::data_source::ds_max_wait')
	  
	# Set username
	$ds_username = hiera('tomcat::data_source::ds_username')

	# Set password
	$ds_password = hiera('tomcat::data_source::ds_password')
	  
	# Set driver class name
	$ds_driver_class_name = hiera('tomcat::data_source::ds_driver_class_name')
	  
	# Url variable
	$ds_driver = hiera('tomcat::data_source::ds_driver')
	$ds_dbms = hiera('tomcat::data_source::ds_dbms')
	$ds_host = hiera('tomcat::data_source::ds_host')
	$ds_port = hiera('tomcat::data_source::ds_port')
	$ds_service = hiera('tomcat::data_source::ds_service')
	  
	# Complete URL
	$ds_url = "${ds_driver}:${ds_dbms}:thin:@${ds_host}:${ds_port}/${ds_service}"

  	$ds_drivername = hiera('tomcat::data_source::ds_drivername')
```

In _custom_ installation mode with users value equal to _yes_, the module will customize `conf/tomcat-users.xml` (by using `templates/users.erb` template). The parameters related to users are the following (listed in tomcat::users class):

```puppet
	# Users
	  
	# Set Default Roles
	$tomcat_roles = hiera('tomcat::roles::list')
	  
	# Set Users
	$tomcat_users = hiera('tomcat::users::list')
	  
	# Set Mapping users-roles
	$tomcat_map = hiera('tomcat::users::map')
```

To use __Hiera__ it's required to define the following variables in a specific file (or splitted in different files). We specify this variable in different file.
The first is called `configuration.yaml` (because in my example I always use YAML format):

```yaml
	---
	tomcat::params::http_port: 8082
	tomcat::params::https_port: 8083
	tomcat::params::ajp_port: 8007
	tomcat::params::shutdown_port: 8001
	tomcat::params::http_connection_timeout: 20000
	tomcat::params::https_max_threads: 150
	tomcat::params::https_keystore: keystore
	tomcat::params::https_keystore_pwd: password
	tomcat::params::web_repository: http://apache.fastbull.org/tomcat/
```
If you don't want to use SSL and so you don't need a keystore leave the keystore related parameters empty or with random values, they will be ignored, same for the https port.

The second is called `data_source.yaml`:

```yaml
	---
	tomcat::data_source::ds_resource_name: jdbc/ExampleDB
	tomcat::data_source::ds_max_active: 100
	tomcat::data_source::ds_max_idle: 20
	tomcat::data_source::ds_max_wait: 10000
	tomcat::data_source::ds_username: username
	tomcat::data_source::ds_password: password
	tomcat::data_source::ds_driver_class_name: oracle.jdbc.OracleDriver
	tomcat::data_source::ds_driver: jdbc
	tomcat::data_source::ds_dbms: oracle
	tomcat::data_source::ds_host: 192.168.52.128
	tomcat::data_source::ds_port: 1521
	tomcat::data_source::ds_service: example
	tomcat::data_source::ds_drivername: ojdbc6.jar
```

The third is called `users.yaml`:

```yaml
	tomcat::roles::list:
	      - manager-gui
	      - manager-script
	      - manager-jmx
	      - manager-status
	      - admin-gui
	      - admin-script

	tomcat::users::list:
	      - username: username
		    password: password
	      - username: username1
		    password: password

	tomcat::users::map:
	      - username: username
		    roles: manager-gui,admin-gui
	      - username: username1
		    roles: manager-gui
```

In this file it is possible to declare all the tomcat roles and associates a username to one or more roles. In this example there is the common tomcat roles.

and declare a `hiera.yaml` of this form (if you are using __Vagrant__)

```yaml
	---
	:backends:
	  - yaml

	:hierarchy:
	  - "data_source"
	  - "configuration"
	  - "users"

	:yaml:
	  :datadir: '/vagrant/hiera'
```

or with the _datadir_ you usually use with __hiera__ if you are not usign __Vagrant__

Testing
-----------------

The Puppet tomcat module has been tested on the following Operating Systems: 

1. CentOS 6.5 x64
1. Debian 7.5 x64
1. Fedora 20.0 x86_64
1. Ubuntu 14.04 x64

Working Example
-----------------

I'm starting to commit some working example of the module. The first is:

* A CentOS 6.5 x64 Vagrant Machine with a clean installation of tomcat and the deploy of sample Application WAR. Here is the repository: __https://github.com/oscerd/vagrant-centos6.5-tomcat__
* A CentOS 6.5 x64 and Debian 7.5 x64 Vagrant Multi-Machine. The two VMs have a custom installation of tomcat and the deploy of sample Application WAR with different context. Here is the repository: __https://github.com/oscerd/vagrant-multiVM-tomcat__

Contributing
-----------------

Feel free to contribute by testing, opening issues and adding/changing code

Puppet Forge
-----------------

The Puppet Tomcat Module has been published on Puppet Forge: __https://forge.puppetlabs.com/oscerd/tomcat__

License
-----------------

Copyright 2014 Oscerd and contributors

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
