# ADR 001: Offline-First Architecture & Frontend Tech Stack

**Status:** Initialized  
**Date:** 2026-10-07

## Context

Loop is designed to function as a "personal digital room" and a high-fidelity Progressive Web App (PWA). A core requirement is that the application must feel indistinguishable from a native Apple application, targeting a 120hz (ProMotion) distraction-free experience.

Traditional Single Page Applications (SPAs) rely heavily on remote network requests for state mutation and data fetching. This introduces variable latency, blocking states, and loading spinners that fundamentally break the illusion of a private, instant-access digital sanctuary. Furthermore, to support the application's Zero-Knowledge Architecture (ZKA) requirements, heavy cryptographic workloads must occur on the client side before data transmission.

We require a technology stack that guarantees local-first data availability, instant optimistic UI updates, and minimal main-thread blocking, while still synchronizing with a remote backend for multi-tenant collaboration.

## Decision

We will adopt a local-first, optimistic synchronization architecture utilizing the following stack:

1. **Application Shell & Routing:** **SvelteKit**
   - We will strictly utilize Svelte 5 (Runes) for fine-grained, compile-time reactivity.
   - Svelte's absence of a Virtual DOM minimizes memory overhead and ensures maximum frame rates during DOM updates.
2. **Local Persistence Engine:** **Dexie.JS (IndexedDB)**
   - All read and write operations will target the local Dexie.JS IndexedDB instance first.
   - This serves as the single source of truth for the SvelteKit UI state.
3. **Remote Sync & Auth Backend:** **Supabase (PostgreSQL)**
   - Acts as an eventually consistent remote storage layer and handles WebRTC signaling (via Supabase Realtime) and authentication.
4. **Schema Management:** **Drizzle ORM**
   - Used for defining the remote PostgreSQL schema and managing database migrations with strict TypeScript safety.
5. **Public / Ephemeral Routing:** **Astro**
   - SvelteKit will not be used for unauthenticated, public-facing share links (e.g., ephemeral caregiver exports). Astro will generate these as zero-JS static or edge-rendered pages to eliminate application bundle overhead for guest users.

## Consequences & Trade-offs

### Positive Impact

- **Zero-Latency UI:** Because all data operations (creating a note, sending a message, saving a map pin) are written to IndexedDB immediately, the UI reacts instantly. Network conditions no longer dictate application performance.
- **Offline Sovereignty:** The application is fully functional without an internet connection, fulfilling the "Single-Player First" product requirement.
- **Reduced Server Load:** The backend is relegated to a dumb sync layer. The heavy lifting of sorting, filtering, and decrypting data is distributed across the client devices.

### Negative Impact & Mitigations

- **Synchronization Complexity:** By writing locally first, we introduce the risk of data divergence if a user goes offline, mutates state, and reconnects later.
  - _Mitigation:_ Because Loop operates strictly in a 1-on-1 (Tri-State) isolation model rather than a global many-to-many feed, write collisions are mathematically rare. We will implement a background Service Worker with a robust retry queue to push ciphertext payloads to Supabase silently.
- **Client-Side Storage Limits:** IndexedDB is subject to browser storage quotas and eviction policies, particularly on iOS Safari.
  - _Mitigation:_ We will implement the Storage API `navigator.storage.persist()` to request persistent storage rights from the browser, treating the PWA as a primary application.
- **Cold Boot State Management:** Initializing the app requires reading the master KEK (Key Encryption Key), decrypting the Dexie vault, and hydrating the UI.
  - _Mitigation:_ We will heavily utilize Svelte 5 `$effect.root` and derived runes to manage the cryptographic hydration cycle before mounting the primary component tree, preventing layout shift or unencrypted data flashes.
