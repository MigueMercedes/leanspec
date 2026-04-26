# specs/features/

Versioned specs of features. Each spec captures the WHAT and WHY of a concrete feature and lives with the code (versioned, reviewable in PRs).

## Path convention

```
specs/features/<area>/<slug>.spec.md
```

**Common areas** (adapt to your domain):

| Area | Covers |
|---|---|
| `auth/` | Login, register, password reset, OTP, sessions, JWT |
| `api/` | Endpoints, validation, rate limiting |
| `billing/` | Payments, subscriptions, invoicing, refunds |
| `notifications/` | Email, SMS, push, webhooks |
| `ui/` | Page-level features, complex components |
| `admin/` | Internal tools, dashboards |

If your feature does not fit, create a new area with kebab-case descriptive name.

**Slug**: kebab-case, descriptive, no redundancy with the area.
- ✅ `billing/installment-payments.spec.md`
- ❌ `billing/billing-installments.spec.md` (redundant)
- ❌ `billing/installments.spec.md` (too generic)

## Rules

1. **One spec per feature**. If scope grows, split into multiple specs instead of one giant.
2. **Link to your tracker** in the spec header if applicable.
3. **Mandatory status field**: `draft | review | implemented | deprecated`.
4. **Never delete deprecated specs**. Mark them with `## Status: deprecated` + reason + replacement. They serve as history.
5. **Drift between spec and code** is treated as a bug. Update the spec in the same commit as the code that changes it.

## Spec vs ADR

If your work introduces an architectural pattern that will apply to multiple future features (e.g. "all tenant queries filter by `tenant_id` at repository layer"), write **ADR** in `../../docs/adr/` in addition to the spec.

If only affects one specific feature, **only spec** here.

## Template and prompts

- Template: [`../templates/feature.spec.md`](../templates/feature.spec.md)
- Pipeline prompts: [`../prompts/`](../prompts/)
- Process detail: [`../../SPEC_PIPELINE.md`](../../SPEC_PIPELINE.md)
