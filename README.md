# Cassandra AWS

## Generating a KeyStore and TrustStore

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
