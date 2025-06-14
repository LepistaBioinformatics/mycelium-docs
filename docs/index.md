# 🚀 Mycelium: The Ultimate API Gateway

Welcome to **Mycelium API Gateway**, the ultimate solution for secure, flexible,
and multi-tenant API management! Whether you're building a robust platform or
enhancing your downstream APIs, our gateway is designed to provide you with
everything you need for seamless authentication, authorization, and security. 🎉

## 🌟 Key Features

### 🧠 AI-aware API Gateway

- **Service Discovery**: Discover downstream APIs and their capabilities.
Mycelium API Gateway is designed to be AI-aware, meaning it can understand the
capabilities of the downstream APIs and use that information to route requests
appropriately.

- **Full control of downstream APIs**: Downstream APIs can control whether they
  routes should be discovered or not.

- **Health Checks**: Downstream APIs can define health checks to indicate when
  they are ready to receive requests. Health status is automatically updated
  based on the health checks and informed during discovery.

### 🔒 Authentication & Authorization

- **Oauth2**: Support for any OAuth2 identity provider with a fell lines of
  configuration.

- **Two-Factor Authentication (2FA)**: Built-in support for TOTP to ensure an
  extra layer of security when users opts to use the internal authentication
  system.

- **Federated Identity Support**: Integrate with external identity providers
  while maintaining full control over roles and permissions.

- **Role-Based Access Control (RBAC)**: Define granular roles for both the
  gateway and downstream APIs using simple YAML configurations.

### 🏢 Multi-Tenant Architecture

- **Tenant Management**: Create and manage tenants with subscription-based
  accounts.

- **Role Assignment**: Invite users to join tenants and assign them specific
  roles to streamline collaboration.

### 🔑 Secure Secrets Management

- **Vault Integration**: Leverage HashiCorp Vault for secure storage of secrets.

- **Flexible Configurations**: Use secrets stored in Vault, environment
  variables, or define them in YAML.

- **Dynamic Secret Injection**: Automate secure secret delivery to downstream
  APIs.

### 🌐 API Routing & Webhooks

- **Smart API Routing**: Easily configure API routes with support for secure
  token-based authentication.

- **Webhook Support**: Define webhooks with secrets for secure callbacks and
  notifications.

### 📄 YAML-Driven Configuration

- **Simple and Intuitive**: Manage all configurations (tenants, roles,
  permissions, routes, and security) with easy-to-read YAML files.

- **Environment Flexibility**: Combine YAML definitions with environment
  variables for maximum flexibility.

### 🛡️ Security-First Design

- **Downstream Security**: Automatically pass role-based security credentials to
  downstream APIs.

- **Token Management**: Store and securely pass tokens in request headers.

- **Compliance Ready**: Designed with modern security practices to meet
  enterprise compliance requirements.

## 🎯 Why Choose Mycelium API Gateway?

1. **Community-Driven and Open Source**: Leverage a growing community while
   benefiting from an open-source model.

2. **Scalable and Modular**: Designed to grow with your needs, from startups to
   enterprise-scale applications.

3. **Developer-Friendly**: YAML-based configurations, secure secret management,
   and role-based policies make it easy to get started.

## 🚀 Getting Started

### Prerequisites

- **Postgres** (Database for tenant and user management)

- **Redis** (Caching for performance)

- **HashiCorp Vault** (optional but recommended for secret management in
  production environments)

- **Docker** (optional for quick deployment)

## 💬 Join the Community

- [GitHub Issues](https://github.com/LepistaBioinformatics/mycelium/issues) for
  feedback and feature requests

## 🌟 Star Us

If you find this project useful, please give us a ⭐ on GitHub to support our
growth and attract more contributors!

---

### License

Mycelium API Gateway is licensed under the [Apache 2.0
License](https://github.com/LepistaBioinformatics/mycelium/blob/main/LICENSE).
Additional restrictions for commercial use apply under the Commons Clause.

---

We can't wait to see how **Mycelium API Gateway** powers your next big project!
🚀
