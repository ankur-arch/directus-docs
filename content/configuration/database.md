---
stableId: 56701b9c-6b92-44f5-8774-782c33b5739d
title: Database
description: Configuration for database connections.
---


:partial{content="config-env-vars"}

| Variable                           | Description                                                                                                                                        | Default Value                 |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| `DB_CLIENT`                        | **Required**. What database client to use. One of `pg` or `postgres`, `mysql`, `oracledb`, `mssql`, `sqlite3`, `cockroachdb`.                      |                               |
| `DB_HOST`                          | Database host. Required when using `pg`, `mysql`, `oracledb`, or `mssql`.                                                                          |                               |
| `DB_PORT`                          | Database port. Required when using `pg`, `mysql`, `oracledb`, or `mssql`.                                                                          |                               |
| `DB_DATABASE`                      | Database name. Required when using `pg`, `mysql`, `oracledb`, or `mssql`.                                                                          |                               |
| `DB_USER`                          | Database user. Required when using `pg`, `mysql`, `oracledb`, or `mssql`.                                                                          |                               |
| `DB_PASSWORD`                      | Database user's password. Required when using `pg`, `mysql`, `oracledb`, or `mssql`.                                                               |                               |
| `DB_FILENAME`                      | Where to read/write the SQLite database. Required when using `sqlite3`.                                                                            |                               |
| `DB_CONNECTION_STRING`             | When using `pg`, you can submit a connection string instead of individual properties. Using this will ignore any of the other connection settings. |                               |
| `DB_EXCLUDE_TABLES`                | A comma-separated list of tables for Directus to ignore completely.                                                                                | `spatial_ref_sys,sysdiagrams` |
| `DB_CHARSET` / `DB_CHARSET_NUMBER` | Charset/collation to use in the connection to MySQL<sup>[1](https://dev.mysql.com/doc/refman/8.0/en/charset-mysql.html)</sup>/MariaDB<sup>[2](https://mariadb.com/docs/server/reference/data-types/string-data-types/character-sets/supported-character-sets-and-collations)</sup>.                                                                                        | `UTF8_GENERAL_CI`             |
| `DB_VERSION`                       | Database version, in case you use the PostgreSQL adapter to connect a non-standard database. Not usually required.                                |                               |
| `DB_HEALTHCHECK_THRESHOLD`         | Healthcheck timeout threshold in milliseconds.                                                                                                     | `150`                         |

## Additional Database Variables

All `DB_*` environment variables are passed to the `connection` configuration of a [`Knex` instance](https://knexjs.org/guide/#configuration-options). This means you can extend the `DB_*` environment variables with any values you need to pass to the database instance.

This includes:
- `DB_POOL__` prefixed options which are passed to [`tarn.js`](https://github.com/vincit/tarn.js#usage).
- `DB_SSL__` prefixed options which are passed to the respective database driver. For example, `DB_SSL__CA` which can be used to specify a custom Certificate Authority (CA) certificate for SSL connections. This is required if the database server CA is not part of [Node.js' trust store](https://nodejs.org/api/tls.html).

::callout{icon="i-lucide-info"}
**Note**  
`DB_SSL__CA_FILE` may be preferred to load the CA directly from a file.
::

## Managed PostgreSQL

Managed PostgreSQL providers usually give you a connection string. Set it as `DB_CONNECTION_STRING` instead of the individual `DB_HOST`, `DB_PORT`, `DB_DATABASE`, `DB_USER`, and `DB_PASSWORD` variables:

```
DB_CLIENT="pg"
DB_CONNECTION_STRING="postgres://user:password@db.example.com:5432/postgres?sslmode=require"
```

- **SSL**: `sslmode=require` in the connection string turns on SSL, and the PostgreSQL driver verifies the server certificate against the Node.js trust store. You only need the `DB_SSL__` options when the provider signs its certificates with its own CA.
- **Pool size**: Each Directus process opens up to 10 database connections by default. Set `DB_POOL__MAX` to stay within the connection limit of your database plan, and count every Directus process that connects to the same database.
- **Connection poolers**: Some providers offer a pooled connection string next to the direct one. For example, with [Prisma Postgres](https://www.prisma.io/postgres?utm_source=directus&utm_medium=docs&utm_campaign=directus_database_configuration), which pools connections with PgBouncer in transaction mode, Directus installs its system tables, runs its migrations, and serves requests with either connection string.
