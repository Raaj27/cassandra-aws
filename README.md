# Cassandra AWS

This CloudFormation template deploys 3 node Cassandra cluster in a single AWS region 
across multiple Availability Zones.

## Storage

Cassandra storage is backed by AWS Elastic File System. Each cassandra node maintains its own data folder on a single File System.
This file system spans across multiple availability zones for redundancy.

### Why EFS?

- No need to resize volumes when data increases (automatically handled)
- Backups of volumes are less crucial since EFS is replicated across Availability Zones
- Performance is good enough for non-prod environments

**Note** Using EFS to back a cassandra cluster is only suitable for development purposes. 
Consider using EBS volumes or instance storage for production grade setups.

# Deployment

TODO

# Securing the cluster with Node-to-Node and Client-to-Node Encryption

To generate a keystore and truststore for use by cassandra for encryption use
the `truststore-setup` script.

`truststore-setup` does the following:

- Generates a keystore with a user supplied password which contains a private key used by Cassandra to establish secure 
communication amongst nodes in the cluster
- Generates a truststore which contains the public cert (corresponding to the private key in the keystore) for the cluster
- Generates a client PEM file containing the public cert for the cluster intended to be used with **cqlsh** for secure client-to-node communication
- Updates cassandra.yaml with the user supplied password for the keystore and truststore

Run the truststore-setup script and specify the password and cluster name.

```bash
$ ./truststore-setup <password> <clustername>
```

This will generate the keystore and the truststore in `cassandra-config`:

```
cassandra-config/
├── conf
│   └── certs
│       ├── cassandra.keystore
│       └── cassandra.truststore
...
```

For clients connecting to the cluster, the cassandra.truststore can be used to establish secure communication. This is a JKS based truststore.

It will also generate the client pem in the root folder. This file can be used with **cqlsh**

```
cluster-ca-certificate.pem
```

Sync the s3 bucket holding the Cassandra configuration and certs.

```bash
$ aws s3 sync cassandra-config s3://cassandra-configuration/<clustername>
```

**Note:** You must name the CloudFormation stack exactly the same as `<clustername>` defined above in order for 
encryption to work correctly.

For example, we have named our bucket `sandbox-cassandra-configuration` and the name of the cluster is `sandbox-cassandra`:
![image](https://user-images.githubusercontent.com/14280155/33042220-03f05ef0-ce0f-11e7-8729-e4a9798079f2.png)
