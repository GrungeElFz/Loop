# ADR 002: Zero-Knowledge Architecture and Hybrid Encryption Strategy

**Status:** Initialized  
**Date:** 2026-10-07

## Context

Loop's core value proposition is data sovereignty and absolute privacy. Centralized databases represent a single point of failure; database administrators, cloud providers, and potential threat actors must not have access to a user's private thoughts, emergency protocols, or 1-on-1 chat streams.

However, implementing blanket client-side encryption across the entire database severely degrades performance for relational queries, spatial mapping, and chronologically merged timelines. We require an architecture that guarantees strict mathematical privacy for sensitive data without sacrificing the performance of a modern social application.

## Decision

We will adopt a Hybrid Security Model utilizing the browser's native Web Crypto API and PostgreSQL Row-Level Security:

1. **Strict E2EE (Zero-Knowledge):**
   - **Scope:** Chat streams, "Safe with me" vault data, daily private notes, and sensitive protocol lists.
   - **Implementation:** Encrypted client-side via AES-256-GCM.
   - **Key Exchange:** Connection shared keys will be established via Elliptic Curve Diffie-Hellman (ECDH), secured by a Key Encryption Key (KEK) derived from the user's master password.
   - **Supabase Role:** Functions strictly as a blind storage locker for ciphertext and encrypted initialization vectors (IVs).

2. **Row-Level Security (RLS):**
   - **Scope:** Non-sensitive spatial and relational data (e.g., map coordinates, timestamps, public avatars, and shared media bucket references).
   - **Implementation:** Native PostgreSQL RLS policies ensuring isolation at the database tier.

3. **Hardware-Gated Vault:**
   - The "Safe with me" module will leverage device-level WebAuthn (Face ID / Touch ID / PIN) to authorize the release of the local decryption keys for the highest-tier sensitive data.

## Consequences & Trade-offs

### Positive Impact

- **Trustless Infrastructure:** The system's privacy is enforced by cryptography, not application logic or privacy policies.
- **Secure Sharing:** Generating public guest links (e.g., Caregiver exports) passes the decryption key strictly in the URL hash fragment (`#key=...`), which is never transmitted to the server.

### Negative Impact & Mitigations

- **Loss of Server-Side Operations:** Server-side search, filtering, and indexing of chat messages or notes are mathematically impossible.
  - _Mitigation:_ All search and indexing features must be executed locally against the decrypted Dexie.JS store.
- **Key Loss:** If a user loses their master password, their E2EE data is permanently unrecoverable.
  - _Mitigation:_ We will implement a strict onboarding flow that clearly communicates this risk and offers offline key-recovery phrase generation.
