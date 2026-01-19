# manhwavision.github.io
ManhwaVision 👁️ – Bangla Manhwa &amp; Manga Reading Platform
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,46 @@
# ManhwaVision 👁️ — Manhwa Reader Starter (Next.js + TypeScript + Prisma + Tailwind)
A starter project for a manhwa/manhua reader site (English UI) branded as ManhwaVision 👁️. Features:
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
‎app_admin_page_Version1.tsx.txt‎
+15
Lines changed: 15 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,15 @@
import React from 'react'
export default function AdminPage() {
  return (
    <div>
      <h1 className="text-2xl font-bold">Admin</h1>
      <p className="mt-4">Admin UI skeleton — implement authentication-protected forms here to create/update series, chapters, and upload images (connect to S3/R2).</p>
      <ul className="mt-4 list-disc ml-6">
        <li>Create Series</li>
        <li>Upload cover (S3 or R2)</li>
        <li>Create Chapter & upload pages</li>
      </ul>
    </div>
  )
}
‎app_layout_Version1.tsx.txt‎
+19
Lines changed: 19 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,19 @@
import './globals.css'
import React from 'react'
import Header from '../components/Header'
export const metadata = {
  title: 'Manhwa Reader',
  description: 'A lightweight manhwa reader starter'
}
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Header />
        <main className="max-w-5xl mx-auto p-4">{children}</main>
      </body>
    </html>
  )
}
‎app_layout_Version2.tsx.txt‎
+19
Lines changed: 19 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,19 @@
import './globals.css'
import React from 'react'
import Header from '../components/Header'
export const metadata = {
  title: 'ManhwaVision 👁️',
  description: 'ManhwaVision — a lightweight manhwa reader starter'
}
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <Header />
        <main className="max-w-5xl mx-auto p-4">{children}</main>
      </body>
    </html>
  )
}
‎app_page_Version1.tsx.txt‎
+23
Lines changed: 23 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,23 @@
import React from 'react'
import prisma from '../lib/prisma'
import SeriesCard from '../components/SeriesCard'
export default async function Home() {
  // Fetch recent/popular series (simple example)
  const series = await prisma.series.findMany({
    orderBy: { updatedAt: 'desc' },
    take: 12,
    include: { tags: { include: { tag: true } } }
  })
  return (
    <section>
      <h1 className="text-3xl font-bold mb-4">Latest & Popular</h1>
      <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
        {series.map(s => (
          <SeriesCard key={s.id} series={s} />
        ))}
      </div>
    </section>
  )
}
‎app_series_[slug]_page_Version1.tsx.txt‎
+35
Lines changed: 35 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,35 @@
import prisma from '../../../lib/prisma'
import React from 'react'
import ChapterList from '../../../components/ChapterList'
import SeriesCard from '../../../components/SeriesCard'
type Props = { params: { slug: string } }
export default async function SeriesPage({ params }: Props) {
  const s = await prisma.series.findUnique({
    where: { slug: params.slug },
    include: { chapters: { orderBy: { number: 'desc' } }, tags: { include: { tag: true } } }
  })
  if (!s) return <div>Series not found</div>
  return (
    <div className="space-y-6">
      <div className="flex gap-6">
        <img src={s.coverUrl ?? '/cover-placeholder.png'} alt={s.title} className="w-40" />
        <div>
          <h1 className="text-2xl font-bold">{s.title}</h1>
          <p className="text-sm text-gray-600">{s.status}</p>
          <p className="mt-2">{s.description}</p>
          <div className="mt-3 flex gap-2">
            {s.tags.map(t => (
              <span key={t.id} className="px-2 py-1 bg-gray-200 rounded text-sm">{t.tag.name}</span>
            ))}
          </div>
        </div>
      </div>
      <ChapterList chapters={s.chapters} seriesSlug={s.slug} />
    </div>
  )
}
‎components_ChapterList_Version1.tsx.txt‎
+23
Lines changed: 23 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,23 @@
import Link from 'next/link'
import React from 'react'
export default function ChapterList({ chapters, seriesSlug }: any) {
  return (
    <div>
      <h3 className="text-lg font-semibold mb-2">Chapters</h3>
      <ul className="space-y-1">
        {chapters.map((c: any) => (
          <li key={c.id} className="flex justify-between items-center p-2 bg-white rounded">
            <div>
              <Link href={`/series/${seriesSlug}/chapter/${c.slug}`} className="font-medium">Chapter {c.number} {c.title ? `— ${c.title}` : ''}</Link>
              <div className="text-xs text-gray-500">{c.publishedAt ? new Date(c.publishedAt).toLocaleDateString() : ''}</div>
            </div>
            <div>
              <Link href={`/series/${seriesSlug}/chapter/${c.slug}`} className="px-3 py-1 bg-blue-600 text-white rounded text-sm">Read</Link>
            </div>
          </li>
        ))}
      </ul>
    </div>
  )
}
‎components_Header_Version1.tsx.txt‎
+18
Lines changed: 18 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,18 @@
'use client'
import Link from 'next/link'
import React from 'react'
export default function Header() {
  return (
    <header className="bg-white shadow">
      <div className="max-w-5xl mx-auto p-4 flex items-center justify-between">
        <Link href="/" className="font-bold text-xl">Manhwa Reader</Link>
        <nav className="flex gap-4 items-center">
          <Link href="/search">Search</Link>
          <Link href="/admin" className="text-sm px-3 py-1 bg-gray-100 rounded">Admin</Link>
          <Link href="/api/auth/signin" className="text-sm">Sign in</Link>
        </nav>
      </div>
    </header>
  )
}
‎components_Header_Version2.tsx.txt‎
+18
Lines changed: 18 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,18 @@
'use client'
import Link from 'next/link'
import React from 'react'
export default function Header() {
  return (
    <header className="bg-white shadow">
      <div className="max-w-5xl mx-auto p-4 flex items-center justify-between">
        <Link href="/" className="font-bold text-xl">ManhwaVision 👁️</Link>
        <nav className="flex gap-4 items-center">
          <Link href="/search">Search</Link>
          <Link href="/admin" className="text-sm px-3 py-1 bg-gray-100 rounded">Admin</Link>
          <Link href="/api/auth/signin" className="text-sm">Sign in</Link>
        </nav>
      </div>
    </header>
  )
}
‎components_Reader_Version1.tsx.txt‎
+37
Lines changed: 37 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,37 @@
'use client'
import React, { useState } from 'react'
import Link from 'next/link'
export default function Reader({ pages, seriesSlug, chapterSlug }: any) {
  const [index, setIndex] = useState(0)
  const total = pages.length
  if (total === 0) return <div>No pages</div>
  const goNext = () => setIndex(i => Math.min(total - 1, i + 1))
  const goPrev = () => setIndex(i => Math.max(0, i - 1))
  return (
    <div className="space-y-4">
      <div className="flex justify-between items-center">
        <div>
          <Link href={`/series/${seriesSlug}`}>← Back</Link>
        </div>
        <div className="text-sm text-gray-600">Page {index + 1} / {total}</div>
      </div>
      <div className="bg-white p-4 rounded">
        <img src={pages[index].imageUrl} alt={`Page ${index + 1}`} className="mx-auto" />
      </div>
      <div className="flex justify-between">
        <button onClick={goPrev} className="px-4 py-2 bg-gray-200 rounded">Previous</button>
        <div className="flex gap-2">
          <button onClick={goNext} className="px-4 py-2 bg-blue-600 text-white rounded">Next</button>
        </div>
      </div>
      <div className="text-xs text-gray-500">Long-scroll mode and reading progress sync to be implemented (client + API).</div>
    </div>
  )
}
‎components_SeriesCard_Version1.tsx.txt‎
+13
Lines changed: 13 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,13 @@
import Link from 'next/link'
import React from 'react'
export default function SeriesCard({ series }: any) {
  return (
    <Link href={`/series/${series.slug}`} className="block bg-white rounded shadow-sm overflow-hidden">
      <img src={series.coverUrl ?? 'https://placehold.co/300x450?text=Cover'} alt={series.title} className="w-full h-48 object-cover" />
      <div className="p-2">
        <h3 className="text-sm font-semibold">{series.title}</h3>
      </div>
    </Link>
  )
}
‎env_Version1.example.txt‎
+10
Lines changed: 10 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,10 @@
# Environment variables - copy to .env and fill
DATABASE_URL="file:./dev.db"
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=change_this_to_a_random_string
# Optional OAuth providers for NextAuth
GITHUB_ID=
GITHUB_SECRET=
EMAIL_SERVER=
EMAIL_FROM=
‎prisma_schema_Version1.prisma.txt‎
+78
Lines changed: 78 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,78 @@
generator client {
  provider = "prisma-client-js"
}
datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}
model Series {
  id        Int       @id @default(autoincrement())
  title     String
  slug      String    @unique
  description String?
  coverUrl  String?
  status    String    @default("ongoing")
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
  chapters  Chapter[]
  tags      SeriesTag[]
}
model Chapter {
  id        Int      @id @default(autoincrement())
  series    Series   @relation(fields: [seriesId], references: [id])
  seriesId  Int
  number    Int
  title     String?
  slug      String
  publishedAt DateTime?
  pages     Page[]
  createdAt DateTime @default(now())
}
model Page {
  id        Int     @id @default(autoincrement())
  chapter   Chapter @relation(fields: [chapterId], references: [id])
  chapterId Int
  index     Int
  imageUrl  String
}
model User {
  id        Int      @id @default(autoincrement())
  name      String?
  email     String   @unique
  image     String?
  bookmarks Bookmark[]
  createdAt DateTime @default(now())
}
model Bookmark {
  id        Int     @id @default(autoincrement())
  user      User    @relation(fields: [userId], references: [id])
  userId    Int
  series    Series  @relation(fields: [seriesId], references: [id])
  seriesId  Int
  chapter   Chapter? @relation(fields: [chapterId], references: [id])
  chapterId Int?
  pageIndex Int?     // reading progress
  createdAt DateTime @default(now())
}
model Tag {
  id    Int    @id @default(autoincrement())
  name  String @unique
  series SeriesTag[]
}
model SeriesTag {
  id       Int    @id @default(autoincrement())
  series   Series @relation(fields: [seriesId], references: [id])
  seriesId Int
  tag      Tag    @relation(fields: [tagId], references: [id])
  tagId    Int
  @@unique([seriesId, tagId])
}
‎styles_globals_Version1.css‎
+11
Lines changed: 11 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,11 @@
@tailwind base;
@tailwind components;
@tailwind utilities;
html, body, #__next {
  height: 100%;
}
body {
  @apply bg-gray-50 text-gray-900;
}
