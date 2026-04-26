## Multi-tenant boundary

> Extension: multi-tenant. Enabled when the product isolates data per customer/account/business.

- **Tenant boundary**: which `tenant_id` (or equivalent: `business_id`, `org_id`, `workspace_id`) gates access to this feature?
- **Cross-tenant queries**: any intentional cross-tenant query? If yes, justify and list which roles can do it (e.g. SuperAdmin, support tooling).
- **Isolation guarantee**: at which layer is isolation enforced — middleware, repository, query, RLS policy? Document the chokepoint.
- **Test coverage**: at minimum, one test that proves a user from tenant A cannot access data of tenant B (must return 403/404, never 200 with empty data).
