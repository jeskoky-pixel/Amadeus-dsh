# Life v1 final hardening and isolated acceptance

Date: 2026-10-25 (Asia/Shanghai). Life PR #2: `e9e0764955f7eb5e260e32e2622729463b2f589c`. The prior baseline was `7855cdd`. The 0.1.0 development package is not a published v1.0 release.

Codex first added five failing regressions to the existing Life branch (`f7acf26`): **0/5 passed** on the v8 code. The original official DSH Desktop cordis Creator Session repaired the five reported defects in its existing Workspace. Codex then found three further boundary failures, added tests, and sent a focused repair to the same Session. A final first-run bootstrap task made the setup preset usable without manual Host JS; Codex added a revocation guard so bootstrap cannot silently restore a revoked binding. No second Agent or headless substitute performed the principal implementation.

| Review issue | Result |
| --- | --- |
| Session revocation affected other Sessions of the same Persona | Revocation now targets only the owner Session; the other Session's activity status, revision, lease, and work remain intact. |
| Legacy model tool could mark Provider work failed | Durable provider-managed flag and central transition checks reject untrusted failed/completed/cancel/pause/resume; terminal Provider results require evidence. |
| Session rebind mixed old identity data | Persona/Scene switching in the same DSH Session is rejected; historical data remains. |
| Cross-scene shared view leaked internals | Only explicit shared+granted records appear; legacy unassigned rows and checkpoint/resource/owner scope are omitted. Grants can be revoked. |
| Estimated and actual tokens double counted; limits were Session-only | Durable per-wake reservation and idempotent official-usage settlement; Session, Persona, and global scopes are checked atomically. Missing usage remains reserved. |

Codex independently ran `node --test test/*.test.js`: **161 passed, 0 failed**. `npm test` passed all eleven groups: 18, 20, 9, 19, 28, 26, 15, 9, 8, 4, 5. The original 137 tests were retained. The 24 additional tests cover security, budget competition, v8→v9 migration and rollback, setup generation, and restart idempotency. See the [Life detailed report](https://github.com/jeskoky-pixel/amadeus-life/blob/codex/life-core/docs/verification/FINAL_HARDENING_2026-10-25.md).

The real isolated DSH Desktop was stopped by verified development process identity. SQLite/WAL/SHM were cold copied and SHA-256 checked. On restart the developer database migrated **v8→v9**, with `PRAGMA integrity_check=ok` before/after and matching old-column digests and row counts for 13 historical tables. Plugin Manager applied disable, enable, remove, and reinstall; Life was left enabled. The original Creator Session stayed readable with an empty queue.

On v9, a synthetic Session with no human message completed a due wake and selected local virtual Provider start/status/finish; the finished activity had Provider start/complete evidence and official usage was recorded. In a separate real model turn, it attempted legacy `life_activity_transition` on Provider work and received `LIFE_UNAUTHORIZED`; status/revision were preserved. With Persona and global wake limits set to zero, a later synthetic autonomous wake was deferred with `budget deferred: wake_limit`. A different Scene rebind returned `LIFE_UNAUTHORIZED` without changing the binding.

The generated **Local activity** preset was loaded on the same isolated Desktop with a separate test database. First load created one Persona, one private Scene, and one active Session binding; restart left the counts and binding revision at 1. The test profile patch was restored byte for byte, and the original development DB remained schema v9 with integrity `ok`. Production SnowLuma, QQ Bridge, NapCat, and production DSH were not touched.

The five reported issues meet the Life v1 **development functional acceptance** boundary. Remaining limits: no formal release, plugin license decision, production deployment, actual payment, real QQ/Browser/device Provider, or external platform send. The setup helper generates safe config but does not install or edit an existing DSH profile. PR #2 in both repositories remains open and unmerged.
