# keycloak-postgres-ssl

Keycloak docker image for using with PostgreSQL over SSL/TLS.

[![Build Status](https://travis-ci.org/mnasyrov/keycloak-postgres-ssl.svg?branch=master)](https://travis-ci.org/mnasyrov/keycloak-postgres-ssl)


This image extends the original [jboss/keycloak][keycloak] to allow specify abitrary parameters for a JDBC connection string. It is done by adding a few environment variables to `standalone.xml` and `standalone-ha.xml` configuration files.

MySQL is possible supported by the latest builds but not tested.

For full documentation refer to [Keycloak's image page][keycloak]. Below will be described configuration of JDBC query parameters.

This repo will be maintained until Keycloak will not support abitrary JDBC parameters. There is a [JIRA ticket][jira-postgres-ssl] for PostrgeSQL.

[keycloak]: https://hub.docker.com/r/jboss/keycloak/
[jira-postgres-ssl]: https://issues.jboss.org/browse/KEYCLOAK-5231


## Supported tags

See [a list of tags](https://hub.docker.com/r/mnasyrov/keycloak-postgres-ssl/tags/) on Docker Hub.
* `upstream` - alias for the `master` branch. It follows `jboss/keycloak:latest` and is automatically rebuilt, may be unstable.
* `latest` - alias for the `stable` branch.


## Configuration

This image introduces following environment variables:
* `KEYCLOAK_JDBC_PARAMS` - JDBC parameters, it is applied to both PostreSQL and MySQL connection strings. Mainly it is for backward compatibility.
* `POSTGRES_JDBC_PARAMS` - parameters for PostreSQL connection string.
* `MYSQL_JDBC_PARAMS` - parameters for MySQL connection string.

Example value of `POSTGRES_JDBC_PARAMS` is `ssl=true&sslmode=verify-ca`.

For all possible values refer to documentation of drivers:
* [PostgreSQL JDBC Driver](https://jdbc.postgresql.org/documentation/head/connect.html)
* [MySQL Connector/J](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-configuration-properties.html)


### Other useful docs

PostgreSQL
* [Secure TCP/IP Connections with SSL](https://www.postgresql.org/docs/current/static/ssl-tcp.html)
* [The pg_hba.conf File](https://www.postgresql.org/docs/current/static/auth-pg-hba-conf.html)
* [SSL Support](https://www.postgresql.org/docs/current/static/libpq-ssl.html)

PostgreSQL JDBC Driver
* [Initializing the Driver](https://jdbc.postgresql.org/documentation/head/connect.html)
* [Using SSL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)


### Usage example

    docker run --name keycloak-postgres-ssl -p 8080:8080 \
      --link postgres-ssl:postgres \
      -e KEYCLOAK_USER=admin \
      -e KEYCLOAK_PASSWORD=admin \
      -e POSTGRES_PORT_5432_TCP_ADDR=postgres \
      -e POSTGRES_DATABASE=keycloak-db \
      -e POSTGRES_USER=keycloak-user \
      -e POSTGRES_PASSWORD=keycloak \
      -e POSTGRES_JDBC_PARAMS='sslmode=verify-ca&sslrootcert=/opt/jboss/postgres.crt.der' \
      -v ${CERTS_DIR}/postgres.crt.der:/opt/jboss/postgres.crt.der \
      mnasyrov/keycloak-postgres-ssl:upstream


## Development

Docker files are located under `./src`.

Build an image:

    ./scripts/build-image.sh [image_name]

Test an image:
    
    ./scripts/test-all.sh [image_name]

