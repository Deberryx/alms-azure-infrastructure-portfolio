# Security Design

## Authentication

The staging design requires **Microsoft OIDC** as the primary provider outside development. Development login is disabled, and Windows/LDAP authentication remains explicitly controlled rather than silently enabled.

Session and browser controls in the reviewed design include:

- Secure and HttpOnly application cookies;
- SameSite Strict behavior;
- bounded idle and absolute session timeouts;
- antiforgery validation for unsafe actions;
- controlled CORS allowlists;
- HSTS and HTTPS enforcement;
- local-only return URL validation;
- provider error details retained in protected logs rather than exposed to users.

## Secret handling

Environment-specific configuration is kept outside Git and images. The source-control review explicitly excluded employee workbooks, cookies, LDAP diagnostics, backups, certificates, private keys, Cloudflare tokens, local Azure parameter files and completed secret configuration.

The architecture favors managed identity for Azure Blob access instead of storage account keys.

## Network exposure

- SQL Server remains on an internal Docker network.
- No application or SQL `ports` mapping is required in Compose.
- Cloudflare Tunnel provides outbound-only application publishing.
- Public SQL access is prohibited.
- Administrative access is intended to be restricted and separately controlled.

## Container controls

The prepared design includes non-root application containers, health checks, restart policies, read-only filesystems/tmpfs where practical, `no-new-privileges`, bounded resources and image vulnerability review before staging release.

## Risk management

The project used time-bounded staging risk-acceptance documents rather than silently ignoring unresolved dependency/container findings. Those approvals explicitly did not authorize production and included expiry/review dates.

The lesson for this portfolio is not that vulnerabilities were 'accepted away'; it is that residual risk was documented, constrained, assigned an owner and required reassessment.
