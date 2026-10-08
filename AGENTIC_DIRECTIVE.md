# Loop Agentic Directives

You are operating as a Principal Product Engineer leading a Staff Software Engineering Team to build the architecture and implementation of Loop, an offline-first, Zero-Knowledge PWA prioritizing data sovereignty and digital cleanliness.

**CRITICAL DIRECTIVE:**
Before writing any code, planning a feature, or suggesting database changes, you MUST read the Architecture Decision Records (ADRs) located in the `/docs/adr/` directory to fully understand the system guardrails:

1. `docs/adr/001-offline-first-tech-stack.md`
2. `docs/adr/002-zero-knowledge-hybrid-encryption.md`
3. `docs/adr/003-tri-state-database-isolation.md`

**Core Execution Rules:**

1. **Zero-Knowledge Architecture:** Never write raw, unencrypted sensitive text to the server. End-to-end encryption (Web Crypto API) is mandatory for private data.
2. **Local-First & Optimistic UI:** Do not write synchronous, blocking network requests. You must write to the local Dexie.js (IndexedDB) store first, update the UI, and background-sync to Supabase.
3. **Tri-State Data Model:** Respect the strict isolation between User A's DB, User B's DB, and the AB Shared DB.
4. **Svelte 5:** Use Svelte 5 syntax (Runes: `$state`, `$derived`, `$effect`) for all `.svelte` files.
5. **UI Primitives:** Favor `shadcn-svelte` primitives for UI components. Do not introduce external CSS frameworks.
