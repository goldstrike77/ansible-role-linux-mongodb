![](https://img.shields.io/badge/Ansible-mongodb-green.svg?logo=angular&style=for-the-badge)

>__Please note that the original design goal of this role was more concerned with the initial installation and bootstrapping environment, which currently does not involve performing continuous maintenance, and therefore are only suitable for testing and development purposes,  should not be used in production environments.__

>__请注意，此角色的最初设计目标更关注初始安装和引导环境，目前不涉及执行连续维护，因此仅适用于测试和开发目的，不应在生产环境中使用。__
___

<p><img src="https://raw.githubusercontent.com/goldstrike77/goldstrike77.github.io/master/img/logo/logo_mongodb.png" align="right" /></p>

__Table of Contents__

- [Overview](#overview)
- [Requirements](#requirements)
  * [Operating systems](#operating-systems)
  * [Mongo Versions](#MongoDB-versions)
- [ Role variables](#Role-variables)
  * [Main Configuration](#Main-parameters)
  * [Other Configuration](#Other-parameters)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
  * [Hosts inventory file](#Hosts-inventory-file)
  * [Vars in role configuration](#vars-in-role-configuration)
  * [Combination of group vars and playbook](#combination-of-group-vars-and-playbook)
- [License](#license)
- [Author Information](#author-information)
- [Contributors](#Contributors)

## Overview
This Ansible role installs  PSMDB on linux operating system, including establishing a filesystem structure and server configuration with some common operational features.

## Requirements
### Operating systems
This role will work on the following operating systems:

  * CentOS 7

### MongoDB versions

The following list of supported the MongoDB releases:

* Percona Server for MongoDB 3.4, 3.6

## Role variables
### Main parameters #
There are some variables in defaults/main.yml which can (Or needs to) be overridden:

##### General parameters
* `mongod_version`: Specify the MongoDB version, minimum 34.
* `mongod_replset`: Cluster name of MongoDB servers that implements replication and automated failover.
* `mongod_servers`: Member list for ReplicaSet.
* `mongod_node_role`: Member role for ReplicaSet.
* `mongod_selinux`: SELinux mongod policy.
* `mongod_authorization`: A boolean value, Enable or Disable authentication.
* `mongod_sa_user`: MongoDB Superuser name.
* `mongod_sa_pass`: MongoDB Superuser password.
* `mongod_path`: Specify the MongoDB data directory.

##### Service Mesh
* `environments`: Define the service environment.
* `consul_public_register`: Whether register a exporter service with public consul client.
* `consul_public_exporter_token`: Public Consul client ACL token.
* `consul_public_clients`: List of public consul clients.
* `consul_public_http_port`: The consul HTTP API port.

#### Backup parameters
* `mongod_backupset_keep`: # Backup retention cycle in days

##### Listen port
* `mongod_port`: MongoDB instance listen port.
* `mongod_exporter_port`: Prometheus MongoDB Exporter listen port.

##### Server System Variables
* `mongod_arg.auditlog`: Enables auditing and specifies where to send audit events.
* `mongod_arg.engine`: Storage Engines, wiredTiger or rocksdb(deprecated in Percona Server for MongoDB 3.6.).
* `mongod_arg.http`: Enables or disables HTTP Status Interface.
* `mongod_arg.maxConns`: The maximum number of simultaneous connections that mongod will accept.
* `mongod_arg.oplogSizeMB`: The maximum size in megabytes for the replication operation log.
* `mongod_arg.redactClientLogData`: Prevent writing sensitive data to the diagnostic log by redacting messages of events before they are logged.
* `mongod_arg.wiredTiger_checkpointSizeMB`: Checkpoint size.
* `mongod_arg.wiredTiger_compressors`: Compression library,  snappy / zlib / none.
* `mongod_arg.wiredTiger_ConcurrentReadTransactions`: Specify the maximum number of concurrent read transactions allowed into the WiredTiger storage engine.
* `mongod_arg.wiredTiger_ConcurrentWriteTransactions`: Specify the maximum number of concurrent write transactions allowed into the WiredTiger storage engine.
* `mongod_arg.wiredTiger_directoryForIndexes`: Enables or disables stores indexes and collections in separate subdirectories under the data (i.e. storage.dbPath) directory.
* `mongod_arg.wiredTiger_prefixCompression`: Enables or disables prefix compression for index data.
* `mongod_arg.wiredTiger_statisticsLogDelaySecs`: Log statistics to a file in the dbPath on the interval defined by.
* `mongod_bu_dbs_arg`: Create a business database if defined.

### Other parameters
There are some variables in vars/main.yml:
* `mongod_folder`: Specify the MongoDB sub data directory.
* `mongod_kernel_parameters`: Operating system variables.
* `mongod_conf_scripts`: Specify the MongoDB configure and script files.
* `mongod_audit_log_commands`: Audit event actions by commands.

## Dependencies
There are no dependencies on other roles.

## Example

### Hosts inventory file
See tests/inventory for an example.

    node01 ansible_host='192.168.1.10' mongod_node_role='arbiter' mongod_is_install='true'
    node02 ansible_host='192.168.1.11' mongod_node_role='replica' mongod_is_install='true'
    node03 ansible_host='192.168.1.12' mongod_node_role='replica' mongod_is_install='true'

### Vars in role configuration
Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: all
      roles:
         - role: ansible-role-linux-mongodb
           mongod_is_install: true

### Combination of group vars and playbook
You can also use the group_vars or the host_vars files for setting the variables needed for this role. File you should change: group_vars/all or host_vars/`group_name`

    mongod_version: '34'
    mongod_replset: 'demo'
    mongod_node_role: 'replica'
    mongod_selinux: false
    mongod_authorization: false
    mongod_sa_user: 'sa'
    mongod_sa_pass: 'password'
    mongod_path: '/data'
    mongod_backupset_keep: '7'
    mongod_port: '27017'
    mongod_exporter_port: '9216'
    mongod_arg:
      auditlog: 'syslog'
      engine: 'wiredTiger'
      http: false
      maxConns: '20000'
      oplogSizeMB: '4096'
      redactClientLogData: true
      wiredTiger_checkpointSizeMB: '1024'
      wiredTiger_compressors: 'snappy'
      wiredTiger_ConcurrentReadTransactions: '512'
      wiredTiger_ConcurrentWriteTransactions: '512'
      wiredTiger_directoryForIndexes: true
      wiredTiger_prefixCompression: true
      wiredTiger_statisticsLogDelaySecs: '0'
    mongod_bu_dbs_arg:
      - dbs: 'example'
        user: 'example'
        pass: 'password'
        role: 'readWrite'
    environments: 'SIT'
    consul_public_register: false
    consul_public_exporter_token: '00000000-0000-0000-0000-000000000000'
    consul_public_http_port: '8500'
    consul_public_clients:
      - '127.0.0.1'

## License
![](https://img.shields.io/badge/MIT-purple.svg?style=for-the-badge)

## Author Information
Please send your suggestions to make this role better.

## Contributors
Special thanks to the [Connext Information Technology](http://www.connext.com.cn) for their contributions to this role.
