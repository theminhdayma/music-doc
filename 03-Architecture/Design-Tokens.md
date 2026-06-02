---
title: Neon Pulse Music — Design Tokens
created: 2026-05-30
project: ecommerce-music
---

# Design Tokens — Neon Pulse Music

Principles

- Dark, luxury palette with purple/pink neon accents.
- Glassmorphism surfaces with soft blur and glow.
- Scales designed for mobile-first but desktop-first layouts.

Color Palette

- `color-bg-900`: #0B0B10 (deep near-black background)
- `color-surface`: rgba(255,255,255,0.04) (glass card base)
- `color-primary`: #7C3AED (Neon Purple)
- `color-accent`: #FF6EC7 (Neon Pink)
- `color-muted`: #9AA0A6 (muted text)
- `color-success`: #4ADE80
- `color-danger`: #FF6868
- `color-glow-primary`: rgba(124,58,237,0.24)
- `color-glow-accent`: rgba(255,110,199,0.20)

Typography

- `font-family-base`: Inter, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial
- `font-family-headline`: Epilogue, Inter
- Size scale (tailwind-like):
  - `text-xs`: 12px
  - `text-sm`: 14px
  - `text-md`: 16px
  - `text-lg`: 18px
  - `text-xl`: 20px
  - `text-2xl`: 24px
  - `text-3xl`: 32px

Spacing

- `space-1`: 4px
- `space-2`: 8px
- `space-3`: 12px
- `space-4`: 16px
- `space-5`: 24px
- `space-6`: 32px

Radii

- `radius-sm`: 6px
- `radius-md`: 12px
- `radius-lg`: 18px

Shadows & Glow

- `shadow-glass`: 0 8px 30px rgba(3,6,23,0.6)
- `glow-primary`: 0 8px 30px color-glow-primary
- `glow-accent`: 0 8px 30px color-glow-accent

Motion

- `motion-short`: 160ms
- `motion-base`: 300ms
- `motion-long`: 600ms

Component-level tokens

- `card-blur`: background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); backdrop-filter: blur(8px);
- `neon-border`: 1px solid rgba(124,58,237,0.12)
- `focus-ring`: 0 0 0 4px rgba(124,58,237,0.12)

Tailwind mapping (example)

Add these to `tailwind.config.js` under `theme.extend`:

- colors: {
  primary: '#7C3AED',
  accent: '#FF6EC7',
  'bg-900': '#0B0B10',
  muted: '#9AA0A6'
}

- boxShadow: {
  'glass': '0 8px 30px rgba(3,6,23,0.6)',
  'glow-primary': '0 8px 30px rgba(124,58,237,0.24)'
}

Usage notes

- Use `card-blur` for auth and profile containers.
- Use `glow-primary` on active buttons and `glow-accent` on CTA hover.
- Keep text contrast high against `color-bg-900` — body copy should use `#E6E7EA` or similar.
