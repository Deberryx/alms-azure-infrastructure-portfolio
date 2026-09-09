# Backup & Disaster Recovery

## Recovery design

The staging design uses a simple, testable recovery model suitable for SQL Server Express and a single-VM application platform.

Prepared controls include:

- full database backup with SQL `CHECKSUM`;
- media verification before off-server acceptance;
- external compression and `age` encryption;
- managed-identity upload to a private Azure Blob container;
- retention controls;
- automated restore testing into a uniquely named disposable database;
- `DBCC CHECKDB` and schema verification after restore;
- explicit separation between application-image rollback and database recovery.

## Core principle

> A successful backup job is not automatically a valid recovery point.

The design marks recovery confidence only after the encrypted backup can be downloaded, decrypted, restored to a disposable database, checked for integrity and recorded as passed.

## Proposed recovery objectives

The source plan proposed:

- RPO: 24 hours;
- RTO: 4 hours for a single-VM rebuild and validated database restore;
- 35-day encrypted backup retention;
- weekly automated restore testing.

These were planning targets requiring approval and measurement. They were **not** represented as measured production SLAs.

## Recovery flow

```text
Incident declared
      |
freeze changes and preserve evidence
      |
select latest restore-validated backup
      |
rebuild reviewed infrastructure
      |
restore external configuration through approved secret process
      |
deploy compatible application release
      |
restore first into disposable/recovery database
      |
integrity + business validation
      |
separate approval for destructive live recovery
```

## Rollback boundary

Application rollback changes the application image only. Database migrations are forward-oriented and are not automatically reversed. A database restore or destructive swap requires a separate recovery/change process.

## Known limitations

The prepared design was cost-optimized and did not claim regional disaster recovery, active-active availability, or zero data loss. A single-region LRS storage design remains a business-risk decision, not an HA claim.
