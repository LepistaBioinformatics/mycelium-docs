# Management Endpoints

Downstream services are configured using a YAML file. An example of such a
definition can be found in the
[`examples/routes.example.yaml`](./examples/routes.example.yaml) file.

## Service Configuration

The service configuration is done in the `services` section of the YAML file.
Each service is defined as a dictionary with the following keys:

**Required keys:**

- `name`: The name of the service. This is used to identify the service and call
  it from the gateway url path. Them, remember to define the service name using
  kebab-case. Example: `my-service`.

- `host` or `hosts`: The host(s) of the service. The host should include the
  port number and ignore the protocol. It can be a single host or a vector of
  hosts.

- `protocol`: The protocol of the service. Example: `http` or `https`.

- `routes`: This is an important field. Thus, it should be described in more
  detail in the [routes section](#routes).

**Optional keys:**

- `discoverable`: Whether the service is discoverable by LLM agents.

- `secrets`: A list of named secrets that are used to authenticate mycelium with
  the downstream service. Mycelium provides two types of secrets:
  - `queryParameter`: A query parameter in the request to the downstream
    service. Query parameters will be added to the request as a query parameter.
    It contains two fields:
    - `name`: The name of the query parameter.
    - `token`: The token to be used to authenticate the request.

  - `authorizationHeader`: An authorization header in the request to the
    downstream service. Authorization headers will be added to the request as an
    authorization header. It contains three fields:
    - `name`: The name of the authorization header.
    - `prefix`: The prefix of the authorization header.
    - `token`: The token to be used to authenticate the request.

> [!NOTE] Mycelium supports the definition of secrets using environment
> variables and vault secrets. For both, the secret should be defined in a
> `JSON` format.
>
> Direct definition:
>
> ```yaml
> - name: secret-query-token
>   queryParameter:
>     name: "token"
>     token: "my-secret-01"
> - name: secret-authorization-header
>   authorizationHeader:
>     name: "Authorization"
>     prefix: "Bearer "
>     token: "my-secret-01"
> ```
>
> Environment variable definition:
>
> ```bash
> export MY_SECRET_01='{"queryParameter": {"name": "token","token": "my-secret-01"}}'
> ```
>
> ```yaml
> - name: secret-query-token
>   env: MY_SECRET_01
> ```
>
> Vault secret definition:
>
> ```yaml
> - name: secret-query-token
>   vault: my-vault-path
> ```

**Optional keys together with `discoverable` field:**

- `id`: The id of the service. If not provided, the id will generated as a UUID
  V3 from the `name` field. We highly recommend to set this field as an UUID V4
  to guarantee the uniqueness and the replicability of the service.

- `description`: The description of the service.

- `openapiPath`: The path to the OpenAPI specification file.

- `healthCheckPath`: The path to the health check endpoint.

- `capabilities`: The capabilities of the service. A vector of strings that
  describe the capabilities of the service. Example: if the service is an
  authentication service, the capabilities could be `authenticate`, `Oauth2`,
  `JWT`, etc.

- `serviceType`: One of the following: `rest-api`, `unknown`. The type should be
  written using kebab-case. New types can be added by opening an issue.

- `isContextApi`: Whether the service is a context API or not. Context APIs are
  used to describe the usage of the service in the context of the gateway. As
  the authentication service is a context API, it is used to authenticate the
  user before accessing the downstream service.

## Routes

Downstream routes should be defined in the `routes` section. It is a vector of
dictionaries. Each dictionary contains the following keys:

**Required keys:**

- `group`: The security group that will be used to protect the route. Currently,
  seven security groups are supported:
  - `public`: The route is public and does not require authentication. Example:

    ```yaml
    - group: public
    ```

  - `authenticated`: The route is authenticated and requires a valid JWT token.
    Mycelium will inject the user email into the request headers named
    `x-mycelium-email` for the downstream service. Example:

    ```yaml
    - group: authenticated
    ```

  - `protected`: Protected paths requires that users are authenticated and have
    a valid profile registered in the gateway. THe full profile should be
    injected as a request header named `x-mycelium-profile`. Example:

    ```yaml
    - group: protected
    ```

  - `protectedByRoles`: Protected paths requires that users are authenticated
    and received a valid role through the mycelium guest system. If the user
    does not have a valid role, the request will be rejected with a 403 error.
    The profile will be injected as a request header named `x-mycelium-profile`.
    Example:

    ```yaml
    - group: !protectedByRoles
        roles:
          - admin
    ```

  - `protectedByPermissionedRoles`: Protected paths requires that users are
    authenticated and received a valid role through the mycelium guest system.
    If the user does not have a valid role, the request will be rejected with a
    403 error. The profile will be injected as a request header named
    `x-mycelium-profile`. Example:

    ```yaml
    - group: !protectedByPermissionedRoles
        permissionedRoles:
          - [admin, write]
          - [editor, read]
    ```

  - `protectedByServiceTokenWithRole`: Mycelium already supports route
    protection by service token with role. The definition is the same of the
    `protectedByRoles` group. Example:

    ```yaml
    - group: !protectedByServiceTokenWithRole
        roles:
          - admin
    ```

    To access the protected route, users should provide a valid service token.
    Service tokens are defined as connection strings that defines the roles that
    the service token should have. Example:

    ```bash
    export MY_SERVICE_TOKEN='tid=c8282c6d-ce0b-4fed-a4e8-8e8a70f5b789;rid=8d7b119b-a12b-4ff1-9db0-5b6d05794282;r=newbie;edt=2025-01-11T21:51:01-03:00;sig=asd132f141e1...3a02f56bf0c4f123'
    ```

    Connection strings should include information about tenant (tid), role
    (rid), the role name (r), and expiration date (edt). The signature (sig) is
    used to verify the validity of the connection string. The role name (r)
    should be checked against the roles defined in the
    `protectedByServiceTokenWithRole` group.

  - `protectedByServiceTokenWithPermissionedRoles`: Mycelium already supports
    route protection by service token with permissioned roles. The definition is
    the same of the `protectedByPermissionedRoles` group. Example:

    ```yaml
    - group: !protectedByServiceTokenWithPermissionedRoles
        permissionedRoles:
          - [admin, write]
          - [editor, read]
    ```

    Service tokens are defined as connection strings that defines the roles that
    the service token has. Example:

    ```bash
    export MY_SERVICE_TOKEN='tid=c8282c6d-ce0b-4fed-a4e8-8e8a70f5b789;rid=8d7b119b-a12b-4ff1-9db0-5b6d05794282;pr=newbie:0;edt=2025-01-11T21:51:01-03:00;sig=dcd32f141e1...3a02f56bf0c4f479'
    ```

    Connection strings should include information about tenant (tid), role
    (rid), the permissioned role name (pr), and expiration date (edt). The
    signature (sig) is used to verify the validity of the connection string. The
    permissioned role name (pr = newbie with view permission) should be used to
    specify the permissioned role name that the service token should have.

- `path`: The path of the route. Users can define as a wildcard. Example:

    ```yaml
    - group: public
        path: /users/*
    ```

- `methods`: A list of HTTP methods that will be used to access the route. In
  addition to default HTTP methods, users can use two reserved words, `ALL` and
  `NONE`. The first one will match all the HTTP methods and the second one will
  match none of the HTTP methods.

**Optional keys:**

- `secretName`: Here users can simple reference the name of the secret defined at
  the service level. Mycelium will resolve the secret at runtime.

  ```yaml
  - group: public
      path: /users/*
      secretName: secret-authorization-header
  ```

- `acceptInsecureRouting`: Whether the route should accept insecure routing.
  This is usual when the downstream service is using a self-signed certificate.
  It is important to note that this should be used with caution, avoiding to use
  it when the downstream service is using a certificate signed by a trusted
  certificate authority. Example:

  ```yaml
  - group: public
      path: /users/*
      acceptInsecureRouting: true
  ```
