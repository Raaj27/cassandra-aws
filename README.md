# Cassandra AWS

## Architecture
This CloudFormation template deploys a multi-node (3 node) Cassandra cluster in a single AWS region (C* datacenter) 
across multiple Availability Zones (multiple C* racks).

## Generating a KeyStore and TrustStore for Node-to-Node and Client-to-Node encryption

Run the truststore-setup script and specify the password and cluster name.

```
./truststore-setup <password> <clustername>
```

This will generate the keystore and the truststore in:

```
cassandra-config/conf/certs
```

It will also generate the client pem in the root folder.

```
cluster-ca-certificate.pem
```

Sync the s3 bucket holding the Cassandra configuration and certs.

```
aws s3 sync cassandra-config s3://cassandra-configuration/<clustername>
```

**Note:** You must name the CloudFormation stack exactly the same as `<clustername>` defined above in order for 
encryption to work correctly.
