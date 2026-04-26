## Migration impact

> Extension: persistent-data. Enabled when the feature touches DB schema or existing user data.

- **Migration**: tool (alembic / prisma / sequelize / sqlx / ...) + revision id + what it does (DDL summary)
- **Idempotence**: re-running the migration must be a no-op
- **Backfill plan**: for existing rows, how do they get the new shape? Inline migration vs separate backfill script vs lazy-migrate-on-read
- **Expected downtime**: ideal is zero. If non-zero, document the window and the customer impact
- **Rollback strategy**: can the migration be reverted cleanly? Or is it forward-only?

## Backwards compatibility

- **Does this feature break any existing flow?**
  - If YES: feature flag + deprecation timeline + plan to migrate users on the old version
  - If NO: confirm explicitly the old behavior keeps working
- **Tolerance window**: how long do clients/clients on the old version keep working? When do you remove the legacy code path?
