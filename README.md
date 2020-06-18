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
MongoDB is a cross-platform document-oriented database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with optional schemas. Percona Server for MongoDB is a free and open-source drop-in replacement for MongoDB Community Edition. It offers all the features and benefits of MongoDB Community Edition, plus additional enterprise-grade functionality.

- Installation type
  - Standalone.
  - Replica Set.
- Configuration
  - Storage engine cache, Index, Connections and other general parameters.
  - Networking and Firewall.
  - Resource limiting.
  - Cluster member relationship.
  - Customized database.
  - Privilege management.
- Backup
  - Scheduled fully backups.
  - Compressed backups.
  - Uninterrupted transaction processing during backups.
  - Cloud storage backups.
- Monitoring
  - Server Status metrics.
  - Replica Set metrics.
  - Replication Oplog metrics.
  - WiredTiger storage-engine metrics.
  - Top Metrics per collection.
- Secure Benchmark
  - Introduces a native encryption option for the WiredTiger storage engine, It protects the privacy of your information, prevents data breaches and helps meet regulatory requirements.
  - Configure TLS/SSL to encrypt client and cluster communications.
  - Encrypt/decrypt local or streaming backup in order to add another layer of protection to the backups.
  - File system permissions protected when potential vulnerability exists.
  - Authentication management makes IT infrastructures more secure by leveraging existing security rules and processes.
  - Auditing provides monitoring and logging of connection and query activity that was performed on MongoDB server. Information will be transferred to SIEM subsection like Graylog or ELK stack.
- Failover
  - Replica sets provide enough redundancy to survive most network partitions and other system failures. These sets also have sufficient capacity for many distributed read operations. Replica sets should always have an odd number of members. This ensures that elections will proceed smoothly.

Replication is referred to the process of ensuring that the same data is available on more than one Mongo DB Server. This is sometimes required for the purpose of increasing data availability. In MongoDB, multiple MongoDB Servers are grouped in sets called Replica sets. The Replica set will have a primary server which will accept all the write operation from clients. All other instances added to the set after this will be called the secondary instances which can be used primarily for all read operations. Because if your main MongoDB Server goes down for any reason, there will be no access to the data. But if you had the data replicated to another server at regular intervals, you will be able to access the data from another server even if the primary server fails. Another purpose of replication is the possibility of load balancing. If there are many users connecting to the system, instead of having everyone connect to one system, users can be connected to multiple servers so that there is an equal distribution of the load.

## Requirements
### Operating systems
This Ansible role installs Percona Server for MongoDB on linux operating system, including establishing a filesystem structure and server configuration with some common operational features. Will work on the following operating systems:

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
* `mongod_authorization`: A boolean value, Enable or Disable authentication.
* `mongod_sa_user`: MongoDB Superuser name.
* `mongod_sa_pass`: MongoDB Superuser password.
* `mongod_path`: Specify the MongoDB data directory.
* `mongod_system_type`: Define instance parameters.
* `mongod_ssl`: A boolean value, whether Encrypting client and cluster communications.
* `mongod_encryption`: A boolean value, whether enabled the WiredTiger storage Data at Rest Encryption.
* `mongod_encryptionKey`: The encryptionKey character string [generator by openssl rand -base64 32].

##### Backup parameters
* `mongod_backupset_arg.keep`: Backup retention cycle in days.
* `mongod_backupset_arg.encryptkey`: BackupSet encryption key.
* `mongod_backupset_arg.cloud_rsync`: Whether rsync for cloud storage.
* `mongod_backupset_arg.cloud_drive`: Specify the cloud storage providers.
* `mongod_backupset_arg.cloud_bwlimit`: Controls the bandwidth limit.
* `mongod_backupset_arg.cloud_event`: Define transfer events.
* `mongod_backupset_arg.cloud_config`: Specify the cloud storage configuration.

##### Listen port
* `mongod_port`: MongoDB instance listen port.
* `mongod_exporter_port`: Prometheus MongoDB Exporter listen port.

##### Server System Variables
* `mongod_arg.auditlog`: Enables auditing and specifies where to send audit events.
* `mongod_arg.encryptionCipherMode`: The cipher mode for decryption. The supported values are AES256-CBC or AES256-GCM.
* `mongod_arg.engine`: Storage Engines, wiredTiger or rocksdb(deprecated in Percona Server for MongoDB 3.6.).
* `mongod_arg.http`: Enables or disables HTTP Status Interface.
* `mongod_arg.maxConns`: The maximum number of simultaneous connections that mongod will accept.
* `mongod_arg.oplogSizeMB`: The maximum size in megabytes for the replication operation log.
* `mongod_arg.redactClientLogData`: Prevent writing sensitive data to the diagnostic log by redacting messages of events before they are logged.
* `mongod_arg.wiredTiger_cacheSizeGB`: Defines the maximum size of the internal cache that WiredTiger will use for all data.
* `mongod_arg.wiredTiger_checkpointSizeMB`: Checkpoint size.
* `mongod_arg.wiredTiger_compressors`: Compression library,  snappy / zlib / none.
* `mongod_arg.wiredTiger_ConcurrentReadTransactions`: Specify the maximum number of concurrent read transactions allowed into the WiredTiger storage engine.
* `mongod_arg.wiredTiger_ConcurrentWriteTransactions`: Specify the maximum number of concurrent write transactions allowed into the WiredTiger storage engine.
* `mongod_arg.wiredTiger_directoryForIndexes`: Enables or disables stores indexes and collections in separate subdirectories under the data (i.e. storage.dbPath) directory.
* `mongod_arg.wiredTiger_prefixCompression`: Enables or disables prefix compression for index data.
* `mongod_arg.wiredTiger_statisticsLogDelaySecs`: Log statistics to a file in the dbPath on the interval defined by.
* `mongod_bu_dbs_arg`: Create a business database if defined.

##### Service Mesh
* `environments`: Define the service environment.
* `tags`: Define the service custom label.
* `exporter_is_install`: Whether to install prometheus exporter.
* `consul_public_register`: Whether register a exporter service with public consul client.
* `consul_public_exporter_token`: Public Consul client ACL token.
* `consul_public_http_prot`: The consul Hypertext Transfer Protocol.
* `consul_public_clients`: List of public consul clients.
* `consul_public_http_port`: The consul HTTP API port.

### Other parameters
There are some variables in vars/main.yml:
* `mongod_folder`: Specify the MongoDB sub data directory.
* `mongod_kernel_parameters`: Operating system variables.
* `mongod_conf_scripts`: Specify the MongoDB configure and script files.
* `mongod_audit_log_commands`: Audit event actions by commands.

## Dependencies
- Ansible versions >= 2.8
- Python >= 2.7.5

## Example

### Hosts inventory file
See tests/inventory for an example.

    [MongoDB]
    node01 ansible_host='192.168.1.10'
    node02 ansible_host='192.168.1.11'
    node03 ansible_host='192.168.1.12'

    [MongoDB:vars]
    mongod_version='36'
    mongod_replset='ReplicaSet'

### Vars in role configuration
Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
- hosts: MongoDB
  roles:
     - role: ansible-role-linux-mongodb
       mongod_version: '36'
       mongod_replset: 'ReplicaSet'
```

### Combination of group vars and playbook
You can also use the group_vars or the host_vars files for setting the variables needed for this role. File you should change: group_vars/all or host_vars/`group_name`.

```yaml
mongod_version: '36'
mongod_replset: 'demo'
mongod_node_role: 'replica'
mongod_authorization: false
mongod_sa_user: 'sa'
mongod_sa_pass: 'changeme'
mongod_path: '/data'
mongod_system_type: 'autopilot'
mongod_ssl: false
mongod_encryption: false
mongod_encryptionKey: 'GvAjQsuMHn/fMyv570tiyFi6kGf3SbSidFDg4KRy6sk='
mongod_backupset_arg:
  keep: '7'
  encryptkey: 'Yf3ejyv4kjZf'
  cloud_rsync: false
  cloud_drive: 'azureblob'
  cloud_bwlimit: '10M'
  cloud_event: 'sync'
  cloud_config:
    account: 'blobuser'
    key: 'base64encodedkey=='
    endpoint: 'blob.core.chinacloudapi.cn'
mongod_port: '27017'
mongod_exporter_port: '9216'
mongod_arg:
  auditlog: 'syslog'
  enableEncryption: false
  encryptionCipherMode: 'AES256-CBC'
  encryptionKey: 'GvAjQsuMHn/fMyv570tiyFi6kGf3SbSidFDg4KRy6sk='
  engine: 'wiredTiger'
  http: false
  maxConns: '20000'
  oplogSizeMB: '4096'
  redactClientLogData: true
  wiredTiger_cacheSizeGB: '10'
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
    pass: 'changeme'
    role: 'dbOwner'
environments: 'Development'
tags:
  subscription: 'default'
  owner: 'nobody'
  department: 'Infrastructure'
  organization: 'The Company'
  region: 'IDC01'
exporter_is_install: false
consul_public_register: false
consul_public_exporter_token: '00000000-0000-0000-0000-000000000000'
consul_public_http_prot: 'https'
consul_public_http_port: '8500'
consul_public_clients:
  - '127.0.0.1'
```

## License
![](https://img.shields.io/badge/MIT-purple.svg?style=for-the-badge)

## Author Information
Please send your suggestions to make this role better.

## Contributors
Special thanks to the [Connext Information Technology](http://www.connext.com.cn) for their contributions to this role.