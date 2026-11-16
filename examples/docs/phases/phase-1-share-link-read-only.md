# Phase 1: Share link, read-only view
> Spec: docs/specs/wishlist-sharing.md (AC-1, AC-2, AC-3) · Size: M, ~5 files

## Description
Walking skeleton of the feature. The owner can create and copy a share link, and a logged-out visitor sees the list read-only. This covers the token column, the public route and the read-only page variant, from end to end.

## Type
AFK — weil every AC can be checked with Playwright and no credentials or UX decisions are needed (the read-only view reuses existing components).

## How to demo
Owner clicks "Share", copies the link, opens it in a private window and sees their list without edit buttons.

## Acceptance Criteria
- [ ] WHEN the owner clicks "Share", a link with a ≥128-bit token and a "Copy link" button appear.
- [ ] WHEN a logged-out visitor opens the link, the items are shown without edit controls.
- [ ] Prices marked private are not shown on the shared page.
- [ ] Clicking "Share" again shows the same link.
- [ ] An unknown token shows a 404-style "list not found" page.

## Verification
- `npx playwright test tests/e2e/wishlist-share.spec.ts`
- `npx vitest run src/lib/shareToken.test.ts`

## Blocked By
None — can start immediately.

## Notes
- The read-only view should reuse `WishlistPage.tsx` with a `readOnly` prop instead of a copy, so phase 3 can add reservations in one place.
