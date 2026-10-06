# ( Loop )

### A personal, offline-first platform designed for digital cleanliness, self-serenity, and intentional human connection.

### Preserve our cognitive loads from the noise of modern social media. There are no algorithmic feeds, no influencers, and no ads.

### Loop serves as your personal digital room — a beautifully organized space for your thoughts, lists, and media. A space that encourages self-reflection, self-curation, while offering a seamless bridge to share parts of your life with the people who matter most.

<br>

### 1. Your Space: A Room of Your Own

Loop works perfectly even if you never invite a single person. It’s a tool built to give you absolute control over your digital life, free from clutter and tracking.

- **The Daily Canvas**: A quiet, beautifully clean space for your private thoughts, daily to-dos, and quick notes.

- **Your Media, Your Rules**: Save your favorite YouTube videos, Spotify playlists, and other media links. Unlike major platforms that make it impossible to clean up your history, Loop gives you absolute power to easily organize or bulk-delete anything you no longer want.

- **Solo Planner**: A private map and planner for places you want to explore and things you want to achieve, just for you.

- **The Care Board**: A secure spot to log your personal vitals, clothing sizes, and an Emergency Protocol (allergies, medical steps) that you can instantly save to your phone's native contacts app.

<br>

### 2. The Loop: Bridging the Distance

When you are ready to connect, the circle is tucked quietly in a sidebar. You only engage when you choose to.

- **Seamless Sharing**: You never have to type things out twice. If you want to share your food cravings or emergency info with a close friend or partner, you just link them to that specific part of your space.

- **No Labels Required**: You don’t have to categorize people as _"Partner"_, _"Best Friend"_, or _"Family"._ You just connect with the people who matter, naturally.

<br>

### 3. Shared Spaces: Experiencing Together

When you interact with someone in your Loop, you enter a private, shared sandbox built to bridge the distance until you can meet up.

- **Dialogue**: An end-to-end messaging space where you can effortlessly drop songs, map pins, and calendar plans right into the conversation.

- **The Shared Spaces**: Shared spaces to actively plan what you want to do: _**Go Together** (destinations)_, _**Watch Together** (media)_, _**Dress Together** (style)_, and other shared spaces.

- **The Shared Timeline**: A beautiful chronological feed where your personal memories seamlessly merge with the photos and moments you log together.

<br>

### 4. Real Life First: The Delayed Check-In

Technology should get out of the way when you are actually together.

- **Giving Space**: If you schedule a shared trip or dinner date, Loop goes completely silent. It will not buzz, notify, or interrupt your physical time together.

- **Gently Ask**: Hours or days after you are finally rested, the app gently checks in. Once you confirm you went, you can both contribute photos and notes to the _Shared Timeline_ to relive the memory.

<br>

### 5. Your Secrets Stay Yours

Privacy isn't a setting; it's the foundation of the app.

- **Glance Mode**: If you're using the app in public, one tap instantly blurs or hides sensitive text _(like phone number or personal notes)_ so no one can read over your shoulder.

- **Safe with me**: A locked folder for your most sensitive items — like surprise gift planning or private journal entries — that requires Face ID, Touch ID, or a PIN to open.

- **Secure Guest Links**: Need to send your _Emergency Protocol_ to a caregiver or babysitter? You can generate a temporary, secure link that self-destructs when you choose.

<br>

## For Developer

Loop is a high-performance, offline-first Progressive Web App (PWA) prioritizing data sovereignty and strict cryptographic privacy. Built on a Zero-Knowledge Architecture (ZKA), it provides a single-player utility dashboard that seamlessly scales into a multi-tenant, end-to-end encrypted messaging and close-collaboration platform.

<br>

### 1. The Technology Stack

- **Frontend Shell**: [`SvelteKit`](https://github.com/sveltejs/svelte) _(SPA / Hybrid routing)_ for persistent cryptographic state, service-worker lifecycle management, and WebSocket connections.

- **UI Primitives**: [`Shadcn-Svelte`](https://github.com/huntabyte/shadcn-svelte) for minimal bundle sizes and accessible, unstyled components.

- **Public Routing**: [`Astro`](https://github.com/withastro/astro), utilized strictly for zero-JS marketing pages and ephemeral decryption endpoints.

- **Backend as a Service**: [`Supabase`](https://github.com/supabase/supabase) providing [`PostgreSQL`](https://github.com/postgres/postgres), Authentication, and Realtime WebSockets.

- **Database ORM**: [`Drizzle ORM`](https://github.com/drizzle-team/drizzle-orm) for lightweight, highly performant, and type-safe schema management.

- **Offline Engine**: [`Dexie.JS`](https://github.com/dexie/Dexie.js) for local-first data caching and optimistic UI updates.

<br>

### 2. Security & Cryptography Architecture

- **Zero-Knowledge E2EE**: Powered by the browser's native Web Crypto API. Chat messages, daily notes, and vault items are encrypted client-side (AES-256-GCM) before transmission.

- **ECDH Key Exchange**: Connections establish shared symmetric keys via _Elliptic Curve Diffie-Hellman_, secured by a Key Encryption Key (KEK) derived from the user's master password.

- **URL Hash Decryption**: Public sharing links _(e.g., Caregiver exports)_ pass the decryption key strictly in the URL hash fragment `[domain.com/share#key=123](https://domain.com/share#key=123)`. Browsers do not send fragments to the server, ensuring the server delivers the encrypted payload blindly.

- **WebAuthn Integration**: The _Safe with me_ vault utilizes device-level biometrics (Face ID/Touch ID) to authorize the decryption of highly sensitive local data.

<br>

### 3. Data Flow & State Management

- **Hybrid Storage Model**: To maintain _ProMotion_ UI performance, heavy spatial/relational data _(e.g., map coordinates, timestamps, media buckets)_ are secured via PostgreSQL Row-Level Security (RLS), while sensitive textual/personal data uses strict E2EE.

- **The Tri-State Database Model**: To avoid sync conflicts, relationship data is siloed into three distinct PostgreSQL spaces
  - `[User A]` isolated database
  - `[User B]` isolated database
  - `[AB]` shared database.

- **Optimistic UI & Background Sync**: Read/write operations execute against the local `Dexie.JS` IndexedDB instance first. A Service Worker queues network requests and silently syncs ciphertext to Supabase in the background, ensuring zero UI blocking even on poor connections.

<br>

### 4. Technical Feature Highlights

- **Polymorphic Components**: A unified Svelte's `<CollectionBoard/>` dynamically renders food, music, and clothing lists, preventing codebase bloat and ensuring high reusability.

- **Smart Chat Action Drawer**: Replaces complex slash commands with a mobile-optimized bottom sheet. Links dropped into chat are unfurled via stateless, privacy-preserving Edge Functions that strip identifying client headers.

- **WebRTC Ready**: The architecture is prepped for peer-to-peer video calls for future phases, utilizing Supabase Realtime purely as a signaling server to introduce devices via STUN/TURN, bypassing central media servers entirely.
