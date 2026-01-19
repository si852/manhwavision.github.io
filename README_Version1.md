# Manhwa Reader Starter (Next.js + TypeScript + Prisma + Tailwind)

A starter project for a manhwa/manhua reader site (English UI). Features:
- Home (popular/new)
- Series page with chapters
- Reader page (page-by-page; long-scroll placeholder)
- Search + tag filter
- Auth scaffold (NextAuth)
- Bookmarks & reading progress (DB-backed)
- Admin UI skeleton
- Prisma schema + seed data (SQLite by default)

IMPORTANT: Do NOT upload or host copyrighted content you don't own or have rights to. This starter is for building a legal reader/aggregator or for hosting content you own.

Quickstart (local)
1. Clone repository
2. Copy env example:
   cp .env.example .env
   - Fill NEXTAUTH_SECRET and optional provider keys
3. Install:
   npm install
4. Run Prisma migrate + seed:
   npx prisma migrate dev --name init
   node prisma/seed.js
5. Start dev:
   npm run dev
6. Open http://localhost:3000

Switching to PostgreSQL:
- Update DATABASE_URL in .env
- Run `npx prisma migrate dev`

Files of interest:
- app/ - Next.js App Router pages and layout
- components/ - UI components
- prisma/schema.prisma - DB schema
- prisma/seed.ts - seed script
- lib/prisma.ts - Prisma client
- .env.example - environment variables

Deployment notes:
- Use a database like Postgres in production.
- Store images in S3 / Cloudflare R2 and serve through a CDN.
- Configure NEXTAUTH_URL and NEXTAUTH_SECRET in production.

License: MIT