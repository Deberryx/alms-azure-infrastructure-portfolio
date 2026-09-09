# SQL Server Express Compatibility

## Assessment result

The application schema, Dapper repositories, stored procedures, views, transactions, JSON usage, table-valued parameters, indexes and durable notification queue were assessed as compatible with SQL Server 2022 Express.

The architecture was accepted only conditionally because SQL Server Express has material operational limits, including the 10 GB per-database ceiling and the absence of SQL Server Agent.

## Key adaptation

The legacy design included Windows/SQL Agent scheduling and backup assumptions that do not fit an Ubuntu + SQL Server Express target.

The reviewed architecture therefore replaces those dependencies with Linux/systemd workflows:

| Legacy dependency | Prepared replacement |
| --- | --- |
| SQL Server Agent backup jobs | systemd timer invoking external backup workflow |
| SQL Agent daily-report schedule | systemd timer queuing the existing report procedure |
| SQL Agent maintenance | systemd maintenance timer |
| SQL Agent restore validation | scheduled disposable restore test |
| Windows drive / UNC backup assumptions | encrypted off-server Azure Blob workflow |
| Database Mail dependency | host/service monitoring and external alerting path |

## Migration safety

The prepared migration runner:

- sorts approved numbered scripts;
- excludes test-only and SQL Agent scripts;
- hashes the executed production payload;
- records applied migrations in a ledger;
- refuses altered previously applied migrations;
- stops on the first SQL error;
- refuses to guess a baseline for an existing database without its migration ledger.

## Capacity gate

The 10 GB Express ceiling is treated as a hard platform constraint rather than a target. The source assessment recommends stopping before plausible growth approaches that boundary and validating representative workload behavior under Express memory/compute constraints.

## Engineering lesson

Edition limitations should be treated as architecture inputs. The goal is not to force an enterprise design onto a free edition, but to simplify the operating model, document the constraints and define a clear upgrade trigger.
