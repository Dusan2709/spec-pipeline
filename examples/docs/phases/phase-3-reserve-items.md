# Phase 3: Guests reserve items
> Spec: docs/specs/wishlist-sharing.md (AC-5, AC-6, Edge Cases) · Size: M, ~6 files

## Description
Guests can mark an item as "reserved" and undo their own reservation. Other guests see "reserved" without a name, and the owner sees nothing.

## Type
HITL — weil the wording and visual treatment of "reserved" and the undo control need a UX review before release.

## How to demo
Two private windows open the same link. Guest A reserves an item, and guest B sees "reserved" with no undo button. The owner's view looks unchanged.

## Acceptance Criteria
- [ ] WHEN guest A reserves an item, guest B sees "reserved" without a name.
- [ ] WHEN guest A clicks "Undo", the reservation is removed; guest B sees no undo control.
- [ ] Reserving an already reserved item returns `409` and the UI shows "already reserved".
- [ ] The owner's own wishlist view never shows reservation state.
- [ ] WHEN the link was revoked in the meantime, a reserve attempt is rejected and the page shows "no longer shared".
- [ ] WHEN the owner deletes a reserved item, its reservation is deleted with it.

## Verification
- `npx playwright test tests/e2e/wishlist-reserve.spec.ts`
- Manual: UX review of the "reserved" badge and undo control in light and dark mode.

## Blocked By
- Phase 1 — Share link, read-only view: needs the public page to put the reserve button on.
- Parallel-safe with Phase 2

## Notes
- Spec Open Question "abuse protection for mass-reserving" stays open; it is not addressed in this phase.
