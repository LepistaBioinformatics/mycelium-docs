# How to install and execute Mycelium API Gateway

This guide will walk you through the steps to install Mycelium API Gateway on
your local machine. Mycelium API Gateway package includes twelve libs and
services, available in Crates.io. It should be installed using the cargo package
manager.

## Prerequisites

Before you start, make sure you have the following installed on your machine:

- **Postgres** (Database for tenant and user management)
- **Redis** (Caching for performance)
- **HashiCorp Vault** (optional but recommended for secret management in
  production environments)
- **Docker** (optional for quick deployment)

## Installation using cargo

The simplest way to install Mycelium API Gateway is to use the `cargo` package
manager.

```bash
cargo install mycelium-api
```

Once installed, the simplest way to start the API Gateway is to run the
following command:

```bash
SETTINGS_PATH=settings/config.example.yaml myc-api
```

Note that the environment variable `SETTINGS_PATH` is pointing to the settings
file. For details of the settings file, please refer to the
[settings](./config.md) documentation.

## Installation using Docker

The easiest way to install Mycelium API Gateway is to use the Docker image.

```bash
docker pull sgelias/mycelium-api:latest
```

## Initialize Postgres Database

Mycelium API Gateway uses Postgres as the main datastore. Thus, the first step
to start the API Gateway is to initialize the Postgres database. To do this,
first guarantee that you have a Postgres instance running. Then, you can use the
[`postgres/sql/up.sql`](https://github.com/LepistaBioinformatics/mycelium/blob/4a8acb401f901c4ae5a10d448163950f1619956b/postgres/sql/up.sql){:target="_blank"}
script to initialize the database using the following command with the `psql`
utility's with the `-v` option to pass the database password:

```bash
psql postgres://postgres:postgres@localhost:5432/postgres \ 
  -f postgres/sql/up.sql \ 
  -v db_password='REPLACE_ME'
```

Replace the connection string with your own Postgres connection string. Note the
`postgres` database is used to initialize the connection, however the script
itself creates an alternative database named `mycelium-v7-dev` as default.

The database name is configurable using the `db_name` variable in the `up.sql`
script. Example:

```bash
psql postgres://postgres:postgres@localhost:5432/postgres \ 
  -f postgres/sql/up.sql \ 
  -v db_name='my-mycelium-database' \ 
  -v db_password='REPLACE_ME'
```

Additional arguments customizations should be done in the `up.sql` script to set
the `db_user` and `db_role` variables. Default values are `mycelium-v7-user` and
`service-role-mycelium-v7` respectively.

## Initialize Vault

Vault is optional but recommended for secret management in production
environments. To initialize Vault, use the standard Vault CLI commands to init
and unseal the Vault.

```bash
vault operator init
```

As output, you will get the root token and the keys to unseal the Vault.

```bash
Unseal Key 1: REPLACE_ME
Unseal Key 2: REPLACE_ME
Unseal Key 3: REPLACE_ME
Unseal Key 4: REPLACE_ME
Unseal Key 5: REPLACE_ME

Initial Root Token: REPLACE_ME
```

Once the Vault is initialized, you can unseal it using the following command:

```bash
vault operator unseal
```

For more information on how to use Vault, please refer to the official
[Vault documentation](https://www.vaultproject.io/docs){:target="_blank"}.

---

Lets code and be happy!
