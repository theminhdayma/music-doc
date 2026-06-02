
# Frontend Architecture

## Overview

The frontend is a Next.js App Router application written in TypeScript and styled with Tailwind CSS.
It is built for a marketplace workflow, not a streaming product, so the UI focuses on discovery,
purchase, ownership, and secure playback.

## Core Principles

- Keep page-level data fetching in server components when possible.
- Keep interactive UI, media playback, and modals in client components.
- Use route query params for list filters and server-side pagination.
- Keep business decisions in feature stores or services, not in presentational components.
- Never expose raw audio URLs in the UI; playback must go through protected API flows and signed URLs.

## Frontend Stack

- Framework: Next.js App Router
- Language: TypeScript
- Styling: Tailwind CSS
- Motion: Framer Motion
- Icons: lucide-react
- State: Zustand stores for auth, cart, and music player state
- Fetching: browser `fetch` and a shared `api` client for authenticated requests
- UI primitives: local component library under `src/components/ui`

## Project Structure

- `src/app`: route entry points, layout, and route-level server components.
- `src/features`: feature slices grouped by business capability.
- `src/store`: shared Zustand stores for auth, cart, and playback.
- `src/components/ui`: reusable design-system primitives.
- `src/lib`: shared helpers such as `cn` and date formatting.
- `src/types`: shared DTOs and frontend contracts.

## Page Composition Pattern

Most pages follow this pattern:

1. Route page in `src/app/**/page.tsx` resolves `searchParams` and calls a feature API function.
2. Feature API function fetches data and maps API responses into UI-friendly DTOs.
3. Page view component renders the layout and delegates interaction to smaller subcomponents.
4. Shared state is consumed from Zustand stores where playback or auth state is needed.

This keeps routing and business orchestration at the page boundary while the visual layer stays reusable.

## Data Fetching Strategy

- Server components are used for initial page data whenever the page is primarily read-only.
- Client components fetch only when the data is interactive or browser-only auth is required.
- Public marketplace pages use query params such as `q`, `genreId`, and `page` for filtering and pagination.
- Authenticated user flows use the shared API client so tokens are attached consistently.

Examples:

- Homepage loads featured music, genres, creators, and trending sections.
- Admin list pages read paging and filters from the URL.
- Buyer library and player ownership checks refresh from `/buyer/library`.

## Routing And UI Boundaries

- `src/app/page.tsx` is the homepage entry and orchestrates featured discovery.
- `src/app/music/[slug]/page.tsx` renders public music detail.
- `src/app/library/page.tsx` renders the buyer library and download actions.
- `src/app/admin/**` contains admin dashboards and management pages.
- `src/app/seller/**` contains seller dashboards and music management.

Route pages should stay thin and pass data down into feature views.

## State Management

### Auth Store

- Holds the current user, hydration state, and login/logout session state.
- Used to gate role-specific actions like seller upload, buyer cart, and admin pages.

### Cart Store

- Keeps optimistic cart items and total counts for the buyer experience.
- Mirrors server state from `/buyer/cart` and local cache for resiliency.

### Music Player Store

- Centralizes current track, queue, playback state, volume, and preview lock state.
- Resolves ownership from seller identity and buyer-owned track ids.
- Supports preview-only playback and full playback for owned tracks.
- Powers the persistent global player dock mounted in `src/app/layout.tsx`.

## Music Playback UX

- Preview playback defaults to 90 seconds for non-owned tracks.
- Seller-owned tracks can play in full when the current user owns the music.
- Buyer-owned tracks are resolved from `/buyer/library` and treated as owned.
- The docked player includes play/pause, next/previous, stop/cancel, and ownership status.
- Playback state persists across route changes because the player shell lives in the root layout.

## Feature Modules

### Home

- Homepage balances featured music, genres, trending, and creator spotlights.
- Featured music uses server-side pagination with 6 items per page.
- Search and genre filters are encoded in the URL.

### Music Detail

- Shows track metadata, purchase action, and preview/full playback entry points.
- Buyer-owned tracks and seller-owned tracks must play in full.

### Library

- Shows purchased tracks with secure download actions.
- Download flows fetch signed URLs only after ownership is confirmed.

### Admin Management

- Admin genre, order, user, and music pages use server paging and management actions.
- Table and drawer views are used for dense admin workflows.

### Seller Management

- Seller music management uses paged catalog browsing with edit and delete actions.
- Seller dashboard pages focus on revenue and top-performing tracks.

## Auth And Permissions

- Public pages can be viewed without authentication.
- Buyer actions require buyer role and authenticated session.
- Seller actions require seller role and ownership checks.
- Admin routes require admin role.
- Client-side role checks improve UX, but the backend remains the source of truth.

## Shared UI And Design

- Dark glassmorphism surfaces are used across marketplace and dashboard views.
- Reusable tokens and utility classes keep the app visually consistent.
- Cards, sheets, dialogs, badges, and buttons are all wrapped in a local UI layer.

## Error Handling

- Pages render loading, empty, and error states explicitly.
- API failures usually surface as toast messages and fallback empty states.
- Invalid auth redirects users to login or back to the home page based on role.

## Related

- [[System-Architecture]]
- [[Backend-Architecture]]
- [[Database-Design]]
- [[SRS-Music]]
- [[API-Music]]
- [[SRS-Cart]]
- [[API-Cart]]
- [[SRS-Order]]
- [[API-Order]]
- [[SRS-Authentication]]
- [[API-Auth]]
