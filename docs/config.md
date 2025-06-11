# Mycelium API Gateway Configuration Guide

This document describes the configurations of the YAML file used to set up the
server. An example file can be found in the
[`config.example.yaml`](./examples/config.example.yaml) file.

In the next sections, we will describe the configurations options used to start
the API Gateway.

## 1. Vault Configurations (`vault`): Highly recommended for production environments

If users opt to use the Vault server to store the secrets, they should configure
the following options:

- **`url`**: The URL of the Vault server. Should include the protocol, hostname,
  and port number if necessary.
- **`versionWithNamespace`**: The API version used to interact with the Vault
  server. Example: `v1/kv`.
- **`token`**: The token used to authenticate with the Vault server. For obvious
  reasons, this configuration should be performed using direct definition in the
  YAML file (development) or environment variables (production).

> **🔒 KEEP SAFE**
>
> Mycelium provides three different ways to configure the execution environment:
>
> 1. Directly in the YAML file (for development purposes).
> 2. Environment variables (for production purposes but storing secrets as
>    environment variables).
> 3. Externally in a Vault server (for production purposes, highly recommended
>    because the secrets are not exposed as environment variables).
>
> All of the three options are valid and can be used for any configuration, but
> we recommended users to use the Vault server to store the secrets in
> production environments.
>
> Example:
>
> Using directly in the YAML file:
>
> ```yaml
> tokenSecret: "my-secret"
> ```
>
> Using environment variables:
>
> ```yaml
> tokenSecret:
>   env: MYC_TOKEN_SECRET
> ```
>
> Using Vault:
>
> ```yaml
> tokenSecret:
>   vault:
>     path: myc/core/accountLifeCycle
>     key: tokenSecret
> ```
>
> Mycelium will try to resolve the variables at runtime, so it is not necessary
> to restart the API Gateway after changing the configuration.

## 2. Core Configurations (`core`)

Here resides the core configurations of the API Gateway. Configs defined here
should be used to configure the basic application lifecycle and webhooks.

### 2.1 Account Lifecycle (`accountLifeCycle`)

- **`domainName`**: The human friendly name of the domain. Usually this is the
  frontend domain name. Example: `Mycelium`.
- **`domainUrl`**: The URL of the domain. Example: `https://mycelium.com`.
- **`tokenExpiration`**: Token expiration time (in seconds).
- **`noreplyName` / `noreplyEmail`**: Name and email for automatic messages.
- **`supportName` / `supportEmail`**: Name and email for support.
- **`locale`**: Default locale. Emails will be sent in this locale.
- **`tokenSecret`**: A unique secret used to encrypt JWT tokens.

### 2.2 Webhook (`webhook`)

- **`acceptInvalidCertificates`**: Allows self-signed certificates.
- **`consumeIntervalInSecs`**: The interval in seconds between each batch of
  webhook dispatch events.
- **`consumeBatchSize`**: The number of events processed per batch.
- **`maxAttempts`**: The maximum number of attempts to process an event.

## 3. SQL Database Adapter Settings (`diesel`)

- **`databaseUrl`**: The database URL. This is recommended to be stored in the
  Vault server.

## 4. Notifier Adapter Settings (`smtp` and `queue`)

### 4.1 SMTP (`smtp`)

- **`host`**: The SMTP server.
- **`username` / `password`**: Smtp credentials.

### 4.2 Email Queue (`queue`)

- **`emailQueueName`**: The name of the email queue.
- **`consumeIntervalInSecs`**: The interval in seconds between each batch of
  email dispatch events.

## 5. Redis Settings (`redis`)

Redis is used to store cache and session data.

- **`protocol`**: The protocol used to connect to the Redis server.
- **`hostname`**: The address of the Redis server.
- **`password`**: The password used to connect to the Redis server.

## 6. Authentication Settings (`auth`)

Mycelium provides a flexible authentication system that allows users to
configure internal and external authentication providers. Internal
authentication is the default authentication provider and is used to
authenticate users against the Mycelium database. External authentication is
used to authenticate users against any external provider that supports OAuth
2.0.

### 6.1 Internal Authentication (`internal`)

- **`jwtSecret`**: A unique secret used to encrypt JWT tokens.
- **`jwtExpiresIn`**: JWT expiration time.
- **`tmpExpiresIn`**: Temporary token expiration time. This is used to generate
  temporary tokens that are used during password reset and account creation
  processes.

### 6.2 External Authentication (`external`)

Here you can configure any external provider that supports OAuth 2.0. See
example in [`config.example.yaml`](./examples/config.example.yaml).

- **`issuer`**: The issuer of the OAuth 2.0 provider. This is a URL that
  identifies the provider.
- **`jwksUri`**: The URI of the JWKS endpoint. This is a URL that points to the
  JWKS (JSON Web Key Set) endpoint of the provider.
- **`userInfoUrl`**: The URI of the user info endpoint. This is a URL that
  points to the user info endpoint of the provider.
- **`audience`**: The audience of the OAuth 2.0 provider. This is a string that
  identifies the API that the provider is used for.

## 7. API Settings (`api`)

### 7.1 Service Settings

- **`serviceIp`**: Service IP address. Usually `0.0.0.0`.
- **`servicePort`**: Service port. Usually `8080`.
- **`serviceWorkers`**: Number of workers. Usually `4`.
- **`gatewayTimeout`**: Gateway timeout. Usually `30` seconds.
- **`allowedOrigins`**: List of allowed origins. Usually `*`.
- **`healthCheckInterval`**: Health check interval. Usually `120` seconds.
- **`maxRetryCount`**: Maximum retry count. Usually `3`.
- **`cache`**: Cache settings. See below for more details.
- **`cache.jwksTtl`**: JWKS cache TTL. Usually `3600` seconds.
- **`cache.emailTtl`**: Email cache TTL. Usually `120` seconds.
- **`cache.profileTtl`**: Profile cache TTL. Usually `120` seconds.
- **`allowedOrigins`**: List of allowed origins. Usually `*` for development
  purposes.

### 7.2 Logging Settings (`logging`)

- **`level`**: Log level for each module. See examples in
  [`config.example.yaml`](./examples/config.example.yaml). Values can be defined
  globally or individually for each module.
- **`format`**: Log format (jsonl/ansi).
- **`target`**: Log destination (stdout/file/collector). See examples in
  [`config.example.yaml`](./examples/config.example.yaml).

### 7.3 TLS Settings (`tls`)

- **`tlsCert`** / **`tlsKey`**: TLS certificates. If stored in Vault, store the
  raw values as strings.

### 7.4 Route Configurations (`routes`)

- **`routes`**: The file system path to the routes configuration file. See
  [`Routes Configuration`](./downstream-apis.md) for more details.
