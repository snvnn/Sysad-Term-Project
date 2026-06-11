# 10. Security, Permissions, and Operations Design

## 1. Security Context

The pilot system handles financial-like operational data, mortgage customer names, and weekly income values. Even if it is a course pilot, the design should treat income and mortgage data as sensitive.

## 2. Roles and Permissions

| Role | Investments | Operating Expense | Mortgages | Computations | Reports | Admin |
|---|---|---|---|---|---|---|
| Foundation Manager | Read/Write | Read/Write | Read | Run/Finalize | Read/Export | No |
| Mortgage Program Officer | Read | Read | Read/Write | Read | Read | No |
| Investment Advisor | Read/Write limited | No | No | Read summary | Investment report | No |
| Trustee/Board | Read summary | Read summary | Read summary | Read | Read | No |
| System Administrator | No business edit by default | No business edit by default | No business edit by default | No business edit by default | No business edit by default | Yes |

> For a very small pilot, roles can be collapsed into `Admin` and `Manager`, but the permission model should remain documented.

## 3. Authentication and Authorization

### Minimum Pilot Recommendation

- Single-user local mode: OS-level access plus application password optional.
- Multi-user mode: username/password with role-based access control.

### Authorization Rules

- Only authorized business users can edit financial data.
- Trustees can view reports but should not modify source data.
- System administrator can manage users/backups but should not silently change business values.

## 4. Sensitive Data

| Data | Sensitivity | Protection |
|---|---|---|
| Current combined gross weekly income | High | role-limited access, audit changes |
| Mortgagees last name | Medium | role-limited access |
| Mortgage account number | Medium | role-limited access |
| Investment returns | Medium | manager/advisor access |
| Reports | Medium/High | restrict export paths and sharing |

## 5. Audit Requirements

Audit events should include:

- Investment created/updated/deleted
- Operating expense estimate created/activated
- Mortgage created/updated/status changed
- Weekly computation run/finalized
- Fund allocation recorded
- Report generated/exported
- User login/logout if authentication exists

Audit fields:

- event id
- actor
- timestamp
- action
- resource type/id
- before/after summary where appropriate

## 6. Operational Design

### Environments

| Environment | Purpose |
|---|---|
| Local/Development | implementation and tests |
| Pilot/Test | course demonstration with sample data |
| Production-like Future | if foundation adopts full system |

### Backup

- Backup source data and weekly computation snapshots.
- Minimum: daily local backup for pilot.
- Future: encrypted offsite backup.

### Monitoring

Pilot-level monitoring:

- application error log
- failed computation log
- report generation log
- data validation warnings

### Recovery

- Restore latest backup.
- Re-run weekly computation from stored source data.
- Compare restored report with saved computation snapshot.

## 7. Threat Model

| Threat | Impact | Mitigation |
|---|---|---|
| Unauthorized income data access | Privacy breach | role-based access, local file permissions |
| Accidental data overwrite | Incorrect reports | audit log, backups, validation |
| Formula misconfiguration | Wrong funding decision | formula versioning, test cases, visible warnings |
| Report shared too widely | Sensitive data disclosure | role restrictions, export controls |
| Floating-point calculation bug | Financial inaccuracies | decimal arithmetic, deterministic tests |

## 8. Configuration Principles

- Formula version should be explicit.
- Week start day should be configurable after policy confirmation.
- Rounding mode should be configured and documented.
- Secrets must not be committed to GitHub.

## 9. Operational Open Issues

- Whether the pilot is single-user or multi-user.
- Whether reports need export to PDF/CSV.
- Backup location and retention period.
- Whether sample data is allowed in repository.
