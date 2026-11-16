# Phase 2: Revoke link
> Spec: docs/specs/wishlist-sharing.md (AC-4) · Size: S, ~3 files

## Description
The owner can stop sharing. The old link then shows a friendly "no longer shared" page, and sharing again creates a brand-new link.

## Type
AFK — weil the behavior is fully observable in E2E tests and nothing is irreversible beyond test data.

## How to demo
Owner clicks "Stop sharing". The previously opened guest tab shows "This list is no longer shared" after a reload.

## Acceptance Criteria
- [ ] WHEN the owner clicks "Stop sharing", `DELETE /api/wishlists/:id/share` returns `204`.
- [ ] WHEN a visitor opens the old link, "This list is no longer shared" is shown.
- [ ] WHEN the owner shares again, the new link has a different token than the revoked one.

## Verification
- `npx playwright test tests/e2e/wishlist-share.spec.ts -g "revoke"`

## Blocked By
- Phase 1 — Share link, read-only view: needs the token and the public route.
- Parallel-safe with Phase 3

## Notes
- Edge case from the spec (a revoked link still open in another tab) is completed in Phase 3, where guests can make write requests for the first time.
