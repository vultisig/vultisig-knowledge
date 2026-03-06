# website-prod

**URL:** github.com/vultisig/website-prod
**Language:** TypeScript
**Purpose:** Vultisig marketing website

---

## Overview

Public-facing marketing site. Next.js 16 (App Router) + Tailwind CSS + Radix UI (shadcn/ui pattern). React 19.

---

## Architecture

```
app/                → Next.js app directory
  api/
    articles/
      route.ts      → Articles CRUD
      auth/route.ts → Article auth
      image/[id]/route.ts → Image serving
      upload/route.ts → Image upload
    reviews/
      route.ts      → Reviews API
  articles/         → Blog pages
  docs/             → Documentation pages
  downloads/        → Download links
  how-it-works/     → Product explainer
  privacy/          → Privacy policy
  support/          → Support page
  vult/             → VULT token page
  layout.tsx        → Root layout
  page.tsx          → Homepage
  sitemap.ts        → SEO sitemap
components/         → Reusable UI (shadcn/ui)
content/            → Static content
hooks/              → Custom React hooks
lib/                → Utilities
public/             → Static assets
```

---

## Tech Stack

- Next.js 16.1.6 (App Router)
- React 19.2.4
- Tailwind CSS
- Radix UI + shadcn/ui
- React Hook Form + Zod
- Spline for 3D graphics

---

## Quick Commands

```bash
yarn dev            # Dev server
yarn build          # Production build
yarn lint           # ESLint
yarn start          # Production server
```

---

## API Routes

| Route | Purpose |
|-------|---------|
| `/api/articles` | Article CRUD (list, create) |
| `/api/articles/auth` | Article authentication |
| `/api/articles/image/[id]` | Image serving by ID |
| `/api/articles/upload` | Image upload |
| `/api/reviews` | Reviews endpoint |

---

## Notes

- No crypto/wallet logic — pure marketing site
- API routes run server-side — validate inputs
- LOW security tier
- No CI/CD workflows in `.github/` (uses Vercel or similar)

---

_Updated: 2026-03-05_
