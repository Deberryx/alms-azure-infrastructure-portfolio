# Azure Architecture

## Goal

Prepare a low-cost Azure staging architecture for an internal leave-management application while keeping identity, data, and administrative surfaces tightly controlled.

## Infrastructure-as-code scope

The source project used a **resource-group-scoped Bicep template** covering:

- Ubuntu Linux VM;
- system-assigned managed identity;
- VNet, subnet, NSG and NIC;
- SSH-key-only administration;
- private Azure Blob containers;
- managed-identity Blob RBAC;
- explicit outbound connectivity;
- optional bounded monitoring and budget alerts.

The template was statically compiled successfully with zero diagnostics. Azure `validate`, `what-if`, and deployment were approval-gated and were not represented as completed work.

## Application topology

```text
Authorized user
   |
Cloudflare edge / Access
   |
outbound-only Cloudflare Tunnel
   |
unprivileged reverse proxy
   |-------------------|
Next.js frontend     ASP.NET Core API
                         |
                 SQL Server Express
                 internal network only
```

The design uses **no Docker host-port publication** for the application or SQL Server. The NSG does not create public web or SQL ingress rules.

## Identity and storage

The application architecture uses Microsoft Entra ID / OIDC for primary staging authentication. Azure Blob Storage is designed with public access disabled and shared-key access disabled. A system-assigned managed identity receives only the required Blob role rather than embedding storage account credentials in the application.

## Platform decisions

The design intentionally separates responsibilities:

- Azure provides compute, identity-aware access to storage, networking and infrastructure controls.
- Cloudflare Tunnel provides outbound-only application publishing.
- Docker provides application isolation and deployment packaging.
- SQL Server Express provides the relational database while the workload remains safely within edition limits.

## What this demonstrates

This work is evidence of Bicep authoring, Azure architecture, managed identity, RBAC, network design, cost-conscious platform selection and explicit separation between a reviewed plan and a completed deployment.
