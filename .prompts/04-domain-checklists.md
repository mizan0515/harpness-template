# Domain Audit Checklists — Evaluator Packet Reference

This file is a reference document, not an execution prompt.
Codex extracts relevant domain sections when writing Evaluator packets.

---

{{DOMAIN_CHECKLISTS}}
<!-- Define checklists for each domain in your project.
     Below are EXAMPLES for common project types.
     Delete the ones that don't apply and add your own. -->

<!-- ==================== WEB PROJECT EXAMPLE ==================== -->
<!--
## UI / Frontend Domain

- [ ] Implemented pages vs design document comparison
- [ ] Skeleton/placeholder pages not marked as "done"
- [ ] All pages reachable via navigation (no dead routes)
- [ ] Forms validate input before submission
- [ ] Error states displayed for failed API calls
- [ ] Loading states shown during async operations
- [ ] Responsive layout works at mobile/tablet/desktop breakpoints
- [ ] No console errors or warnings in browser dev tools

## API Domain

- [ ] All documented endpoints exist and respond
- [ ] Stub endpoints not marked as "complete"
- [ ] Authentication required on protected routes
- [ ] Request validation matches schema
- [ ] Error responses follow consistent format
- [ ] No business logic in route handlers (delegated to services)

## Data / Database Domain

- [ ] Prisma schema matches documented models
- [ ] Seed data covers all required test scenarios
- [ ] Migrations are sequential and reversible
- [ ] No raw SQL queries outside repository layer
- [ ] Indexes exist for frequent query patterns

## Auth / Permissions Domain

- [ ] All defined roles exist in code
- [ ] Role-based access enforced on both client and server
- [ ] Session handling follows documented flow
- [ ] Token refresh/expiry logic works
- [ ] No privilege escalation paths

## State Management Domain

- [ ] Client state boundaries match architecture doc
- [ ] Server state not duplicated in client without sync
- [ ] Optimistic updates have rollback on failure
- [ ] Global state minimal — prefer local/component state

## Testing Domain

- [ ] Test count matches expectations
- [ ] Critical paths have at least unit tests
- [ ] Mocked dependencies documented
- [ ] No tests that always pass regardless of implementation
- [ ] CI runs tests on every PR
-->

<!-- ==================== GAME PROJECT EXAMPLE ==================== -->
<!--
## Combat / Gameplay Domain

- [ ] Effect resolver maintains dispatcher role
- [ ] Per-item scripts not used when shared pipeline suffices
- [ ] Game object lifecycle explicit (original/copy/temporary)
- [ ] Character resources match spec names
- [ ] Documented calculation order preserved

## Network / Authoritative Domain

- [ ] Server RPCs do not compute gameplay logic inline
- [ ] Network state is mirrored, not the sole source
- [ ] Transport types do not leak into gameplay code
- [ ] Local helper paths follow same rules as RPC paths

## Map / Navigation Domain

- [ ] Generation and traversal algorithms match architecture
- [ ] Deleted legacy paths not revived
- [ ] Sector/zone/node types match spec
-->

<!-- ==================== MOBILE APP EXAMPLE ==================== -->
<!--
## Offline / Sync Domain

- [ ] All reads work from local storage
- [ ] Writes queue when offline and sync when online
- [ ] Conflict resolution strategy documented and implemented
- [ ] Sync status visible to user

## Navigation Domain

- [ ] All screens reachable from expected entry points
- [ ] Deep links resolve correctly
- [ ] Back navigation works on every screen
- [ ] Tab/drawer state preserved across navigation

## Push Notifications Domain

- [ ] Token registration on app launch
- [ ] Token refresh on expiry
- [ ] Notification tap opens correct screen
- [ ] Background notifications handled
-->
