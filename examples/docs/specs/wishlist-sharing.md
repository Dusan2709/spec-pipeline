# Wishlist sharing
> Status: Draft · Datum: 2026-11-16 · Quelle: docs/sync-us/2026-11-16-wishlist-sharing.md, nach sync-us (5 Fragen) · Ticket: —

## Problem Statement
Wishlists can only be seen by their owner. To tell friends what they want, owners send screenshots or retype items. Friends can't coordinate, so people end up buying the same gift twice.

## Solution
An owner can create a share link for a wishlist. Anyone with the link sees a read-only version of the list without logging in. Guests can mark an item as "reserved". Other guests see that, but the owner doesn't, so the surprise is kept. The owner can revoke the link at any time.

## User Stories
1. As a wishlist owner, I want to copy a share link, so that I can send my list to friends in any messenger. (P1)
2. As a guest, I want to see the list without creating an account, so that gifting is effortless. (P1)
3. As a wishlist owner, I want to revoke a link, so that I control who can still see my list. (P2)
4. As a guest, I want to mark an item as reserved, so that nobody else buys the same thing. (P2)

## Acceptance Criteria
- AC-1: WHEN the owner clicks "Share", THE SYSTEM SHALL show a link containing a random token of at least 128 bit and a "Copy link" button.
- AC-2: WHEN a logged-out visitor opens a valid share link, THE SYSTEM SHALL show the wishlist items read-only, without edit controls and without prices marked private.
- AC-3: WHEN the owner clicks "Share" on a list that already has an active link, THE SYSTEM SHALL show the existing link (one active link per list).
- AC-4: WHEN the owner revokes the link, THE SYSTEM SHALL show a "This list is no longer shared" page for the old link; sharing again SHALL produce a new token.
- AC-5: WHEN a guest marks an item as reserved, THE SYSTEM SHALL show "reserved" (no name) to all guests, and SHALL NOT show any reservation state in the owner's own view.
- AC-6: WHEN the same guest (same browser cookie) clicks "Undo", THE SYSTEM SHALL remove the reservation; other guests SHALL NOT see an undo control.

### Edge Cases
- Owner deletes an item that is reserved → item disappears for guests, reservation is deleted with it.
- Guest opens a revoked link that's still open in another tab and tries to reserve → request is rejected, page switches to "no longer shared".

## Implementation Decisions
- **Decision** — Share token stored on `wishlists` (nullable `share_token`, unique). · **Warum** — one active link per list makes a separate table unnecessary. · **Verworfen** — `share_links` table with history (no requirement for multiple links).
- **Decision** — Public route `GET /s/:token` outside `requireAuth`. · **Warum** — guests have no account. · **Verworfen** — magic-link login for guests (friction).
- **Decision** — Reservations in a new `item_reservations` (`item_id` unique, `guest_id`, `created_at`); `guest_id` is a random ID in an httpOnly cookie. · **Warum** — undo without an account. · **Verworfen** — storing buyer names (spoils surprise).
- **API contracts:**
  - `POST /api/wishlists/:id/share` → `{ url }` (creates or returns the active token)
  - `DELETE /api/wishlists/:id/share` → `204`
  - `POST /api/s/:token/items/:itemId/reservation` → `201` / `409` if already reserved
  - `DELETE /api/s/:token/items/:itemId/reservation` → `204` / `403` if not the same `guest_id`

## Code References
- `db/schema.sql`: `wishlists`, `wishlist_items`
- `src/routes/wishlist.ts`: all routes behind `requireAuth`
- `src/pages/WishlistPage.tsx`: owner view, no read-only variant yet
- `tests/e2e/` (Playwright), Vitest for units

## Testing Decisions
- E2E (Playwright) for the guest flows: open link logged out, reserve, undo, revoked link.
- Unit tests for token generation and the "owner never sees reservations" rule.
- Prior art: `tests/e2e/wishlist.spec.ts` for owner flows.

## Assumptions
- Guests use a browser with cookies enabled.
- Share links are sent through channels the owner trusts; the link itself is the access control.

## Out of Scope
- Expiring links (owners would have to re-share constantly).
- E-mail invitations: "Copy link" is enough for v1.
- Abuse protection against mass-reserving: accepted risk for v1.
- Ask first: any change to `requireAuth` itself.

## Open Questions
- Abuse protection for mass-reserving: needed after launch? → product owner
