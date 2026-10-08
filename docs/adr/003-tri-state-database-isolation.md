# ADR 003: Tri-State Database Model for Data Sovereignty

**Status:** Initialized  
**Date:** 2026-10-07

## Context

Standard social network schemas often mingle multi-tenant user data within complex many-to-many relationship tables. This creates significant friction regarding data ownership, privacy, and cleanup (e.g., if two users sever a connection, whose data is deleted, and how do we ensure no orphaned artifacts remain?).

Loop must respect the "Single-Player First" philosophy, ensuring users maintain total sovereignty over their digital footprint regardless of their network connections. We need a data model that allows for deep collaboration without permanently tangling user data.

## Decision

Relationship and user data will be strictly isolated into three logical PostgreSQL spaces, defined as the "Tri-State Model":

1. **User A's Isolated DB ("My Space"):**
   - Exclusively writable by User A.
   - Holds private timeline events, unshared wishlists, personal vitals, and solo planners.
2. **User B's Isolated DB ("Their Space"):**
   - Functions as a read-only mirror for User A, exposing only the specific modules User B has explicitly toggled to "shared" with User A.
3. **The AB Shared DB ("The Intersection" / The Trunk):**
   - A dedicated, collaborative relational space generated only when a connection is formed.
   - Holds the E2EE chat stream, shared timeline events, and collaborative map markers.

## Consequences & Trade-offs

### Positive Impact

- **Clean Severance:** If a connection is archived or severed, the AB Shared DB is frozen or dropped. User A's and User B's personal data remain entirely untouched and private.
- **Data Portability:** This enforces the architectural rule that the application functions perfectly as a personal organizer even if the user never forms a connection.

### Negative Impact & Mitigations

- **Client-Side Merging Overhead:** Rendering a unified UI (e.g., a timeline mixing private gym logs with shared dinner dates) requires fetching from multiple distinct tables/spaces.
  - _Mitigation:_ The frontend (SvelteKit) will fetch from both the Isolated DB and the Shared DB, relying on the Dexie.JS local store to efficiently merge and sort these streams chronologically on the client device before rendering.
