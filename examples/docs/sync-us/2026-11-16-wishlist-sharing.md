# Wishlist sharing — sync-us interview
Date: 2026-11-16 · Status: completed · Goal: Owners can share a wishlist via link so friends can see it and avoid duplicate gifts · Ticket: —

## Context established (recon, before Q1)
- Wishlists live in `wishlists` + `wishlist_items` tables (`db/schema.sql`); each list belongs to exactly one `user_id`.
- All wishlist routes require login today (`src/routes/wishlist.ts`, middleware `requireAuth`).
- Wishlist page is rendered by `src/pages/WishlistPage.tsx`; there is no read-only variant yet.
- No existing token/link mechanism in the codebase.
- E2E tests use Playwright (`tests/e2e/`), unit tests use Vitest.

## Summary / key decisions
- [Q1] Anyone with the link can view, no login needed.
- [Q2] Links never expire automatically; owners revoke them manually.
- [Q3] Guests can mark items as "reserved". The owner sees nothing (keeps the surprise), other guests see "reserved" without a name.
- [Q4] One active link per wishlist; revoking invalidates it, sharing again creates a new one.
- [Q5] Guests can reserve without an account. A reservation is tied to a browser cookie so the same guest can undo it.
- [Defaults] Unguessable random token (≥ 128 bit) · read-only view hides prices the owner marked private · "Copy link" button, no e-mail sending.

## Open branches
1. ~~Who can open a shared link~~ → DECIDED (Q1)
2. ~~Link lifetime~~ → DECIDED (Q2)
3. ~~Can recipients see what's already bought~~ → DECIDED (Q3)
4. ~~Revoking a link~~ → DECIDED (Q4)
5. ~~Do guests need an account to reserve~~ → DECIDED (Q5) (Folge aus Q3)
6. ~~Defaults~~ → CONFIRMED (batch)

## Q&A log

### Q1 — Who can open a shared link
- Asked: Who should be able to open a shared wishlist link?
- Recommended: Anyone with the link, no login. Lowest friction for gift-givers.
- Decision: Anyone with the link.

### Q2 — Link lifetime
- Asked: Should shared links expire?
- Recommended: Never expire, manual revoke. Wishlists are long-lived (birthdays, holidays).
- Decision: Never expire, owner can revoke.

### Q3 — Can recipients see what's already bought
- Asked: Can guests see whether an item is already taken?
- Recommended: Yes, "reserved" without a name.
- Decision: Yes, "reserved" without a name, and the owner must NOT see reservations ("otherwise the surprise is gone").
- Folge: Q5 (do guests need an account to reserve?)

### Q4 — Revoking a link
- Asked: What happens when the owner revokes and shares again?
- Recommended: One active link per list; re-sharing creates a new token.
- Decision: As recommended.

### Q5 — Do guests need an account to reserve
- Asked: Do guests need to log in to reserve an item?
- Recommended: No; tie the reservation to a cookie so the guest can undo it.
- Decision: No account. Cookie-based. "If they clear cookies they can't undo, that's fine."
- Flags: abuse (someone reserves everything) → accepted risk for v1, revisit if it happens.

## Open flags (pending input)
- Abuse protection for mass-reserving → product owner, after launch data

## Handoff (input for /create-spec)
- New share token per wishlist (one active), public read-only route without `requireAuth`.
- Read-only wishlist view: no edit controls, private prices hidden.
- Reservation state per item, set/unset by guest via cookie identity; hidden from the owner's own view.
- Revoke = invalidate token; old link shows a friendly "no longer shared" page.
- Rejected: expiring links (owners would have to re-share constantly), login for guests (too much friction for gift-givers), showing buyer names (spoils surprise).
