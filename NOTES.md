# NOTES.md — Sanctum Sanctorum Bookstore

## Live URL

**App:** https://sanctum-sanctorum-backend.onrender.com  
**API Docs:** https://sanctum-sanctorum-backend.onrender.com/docs  
**GitHub:** https://github.com/VenkataHemaSai/sanctum-sanctorum-backend

---

## What I Finished and What I Did Not

All 202 acceptance tests pass. I completed all five required sections:
Books, Members, Orders, Loans, and Reports.

I did not implement any of the three optional extras (concurrent stock locking,
`GET /members` pagination, or additional edge case tests).

---

## Architectural Decisions

**1. Thin routers, fat services**  
All business logic lives in `app/services/`. Routers only parse HTTP and delegate
to service functions. This keeps rules reusable — `get_member` and
`ensure_can_access_restricted` are shared across both Orders and Loans services
without any duplication.

**2. Three-phase validation in `create_order`**  
Order creation runs three separate loops — existence checks first, permission
checks second, stock checks third — rather than one combined loop. This guarantees
that a 404 for a missing book always takes priority over a 403 for a restricted
one, matching the spec's required error precedence exactly.

---

## AI Usage

I used **Antigravity (Gemini)** throughout the assignment.

**What I used it for:** Learning SQLAlchemy 2.0 syntax such as `func.count`,
`func.sum`, `scalar()` vs `scalars()`, and filtering with `.is_(None)`.
Understanding Pydantic v2 validators. Debugging failing tests by reading error
traces together. Guidance on Render and Neon deployment.

**Where the AI was wrong:**
- The AI missed a typo in the member stats query — `Loan.id == member_id` instead
  of `Loan.member_id == member_id` — causing all loan counts to return 0. I caught
  it by reading the test failure carefully and fixed it myself.
- The existing `tier_at_least` function had a `>` instead of `>=` bug. The AI did
  not flag this until the specific test failed and I investigated the logic myself.
