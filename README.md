# Chef NFMS portal cookbook


## Description

Installs and configures a complete UNREDD NFMS portal


## Requirements

Platform:

* Ubuntu

The following Opscode cookbooks:

* apache2
* apt
* ark
* build-essential
* database
* java
* logrotate
* postgresql

The following cookbooks:

* tomcat cookbook by Bryan Berry (https://github.com/bryanwb/chef-tomcat)
* gis cookbook by Mario Rodas (https://github.com/marsam/cookbook-gis)



## Installation on a Vagrant virtual machine

1. Install vagrant (see http://vagrantup.com/v1/docs/getting-started/index.html)
2. Download this repository with submodules: ``git clone --recursive https://github.com/nfms4redd/chef-nfms-portal.git``
3. Run Vagrant:

        cd chef-nfms-portal
        Vagrant up


### Memory allocation

Currently 3GB RAM is allocated for the virtual machine. It can be customized changing the config.vm.customize argument in ``Vagrantfile``:

``config.vm.customize ["modifyvm", :id, "--memory", 3072])``


### Host name

Host name is set to ``unredd`` through the following line in ``Vagrantfile``

``config.vm.host_name = "unredd"``


### Forward ports

Currently only port 80 in the VM is forwarded to port 4567 on the hosting machine.

``config.vm.forward_port 80, 4567`` in ``Vagrantfile``


### Shared folders

A shared folder is created through the line ``config.vm.share_folder "shared", "~/shared", "."`` in ``Vagrantfile``


### Users

Three users are created on the Vagrant virtual machine:

* vagrant
* tomcat6
* postgres

### Applications/services installed


#### Apache 2

Apache 2 is installed through the Opscode apache2 cookbook.

Service name: ``apache2``

The configuration file is ``/etc/apache2/apache2.conf``.

Custom proxy configuration (ajp protocol) is done in ``/etc/apache2/sites-enabled/tomcat_proxy.conf -> `/etc/apache2/sites-available/tomcat_proxy.conf``.

The only other custom configuration is done through ``/etc/apache2/mods-enabled/proxy.conf``


#### Java

Oracle jdk1.6.0_37 is installed using the Opscode java cookbook.


#### Tomcat

Version: 6.0.36

Tomcat is installed with the ``CATALINA_BASE`` method to have multiple instances sharing the same Tomcat installation files.

``CATALINA_HOME=/var/tomcat/default``. ``default`` is a symbolic link to the current Tomcat binary.


#### Postgres

Version: 9.1

``postgres`` user password: ``postgres``

##### Default databases/owners/passwords:

<table>
  <tr>
    <th>Databases</th>
    <th>Owner</th>
    <th>Owner password</th>
  </tr>
  <tr>
    <td>stg_geoserver</td>
    <td>stg_geoserver</td>
    <td>admin</td>
  </tr>
  <tr>
    <td>diss_geoserver</td>
    <td>diss_geoserver</td>
    <td>admin</td>
  <tr>
  <tr>
    <td>stg_geostore</td>
    <td>stg_geostore</td>
    <td>admin</td>
  <tr>
  <tr>
    <td>diss_geostore</td>
    <td>diss_geostore</td>
    <td>admin</td>
  <tr>
</table>


#### Postgis

Installed through the package manager by the gis cookbook. Currently version is ``2.0.1`` is installed.


#### gdal

Installed through the package manager by the gis cookbook. Currently version is ``1.9.2``is installed.


### Web applications


#### stg_geoserver

#### diss_geoserver

#### stg_geostore

#### diss_geostore

#### admin

#### portal

## Demo data

DRC demo data is in /var/tmp/drc

The unredd-nfms-portal::install_test_data recipe sets up stg_geoserver, stg_geostore, and stg_geobath data for testing:

``area.tif`` and ``provinces.tif`` are copied in ``/var/stg_geoserver/extdata/forest_mask_mosaic``
the directory ``/var/stg_geoserver/extdata/forest_mask_mosaic`` is created with the .properties


To setup stg_geostore for the demo data type ``curl -u admin:admin -XPUT -H "Content-type: text/xml" -d @/var/tmp/unredd_geostore_backup.xml http://localhost/stg_geostore/rest/backup/quick/`` on the terminal



## Passwords

<table>
  <tr>
    <th>Application</th>
    <th>user</th>
    <th>pwd</th>
  </tr>
  <tr>
    <td>Admin UI</td>
    <td>admin</td>
    <td>Unr3dd</td>
  </tr>
  <tr>
    <td>GeoServer</td>
    <td>admin</td>
    <td>Unr3dd</td>
  <tr>
  <tr>
    <td>GeoBatch</td>
    <td>admin</td>
    <td>admin</td>
  <tr>
  <tr>
    <td>GeoStore</td>
    <td>admin</td>
    <td>Unr3dd</td>
  <tr>
</table>


## TODO

* install JAI
* Tomcat webapps are deployed in the webapps directory every time chef runs -  check if the webapp version is new and deploy only in that case
* add apache proxy directives from definitions (geobatch.rb, geoserver.rb, and geostore.rb)
* dynamically set schema in geobatch config (now set to public)
* set correct permissions for groovy scripts etc.
* hard coded stuff in geobatch flow config files



GeoStore users are initialized to:

<table>
  <tr>
    <th>user</th>
    <th>role</th>
    <th>pwd</th>
  </tr>
  <tr>
    <td>node['unredd-nfms-portal']['stg_geostore']['web_admin_user']</td>
    <td>ADMIN</td>
    <td>node['unredd-nfms-portal']['stg_geostore']['web_admin_password']</td>
  </tr>
  <tr>
    <td>user</td>
    <td>USER</td>
    <td>node['unredd-nfms-portal']['stg_geostore']['web_admin_password']</td>
  <tr>
</table>

Please note that the user `user` has the same password as the `admin` user
