# superapp-web

Frontend for the **CGSEM Club Management System** — built with Next.js 15 (App Router).

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 15 (App Router) |
| Language | TypeScript |
| Auth | NextAuth.js + Azure Entra ID |
| Styling | Tailwind CSS |
| UI Components | shadcn/ui |
| State | TanStack Query |
| HTTP | Axios |
| Forms | React Hook Form + Zod |

## Architecture

Single dashboard, role-aware rendering:

```
/app
  /(public)     → Guest views (events, posts, media)
  /(auth)       → Member + Admin (protected by NextAuth)
    /dashboard  → Role-based dashboard
    /events     → Event management
    /tasks      → Task board
    /comms      → Post tracking (Facebook status)
    /media      → OneDrive media references
    /members    → Admin only
```

## Branch Strategy (Trunk-Based Development)

| Branch | Purpose |
|---|---|
| `main` | Trunk — always deployable, this is develop |
| `feat/*` | Short-lived feature branches → PR into `main` |
| `fix/*` | Bug fixes → PR into `main` |
| `stable/vx.x` | Staging promotion from `main` |
| `vx.x.x` (tag) | Production release |

> PRs **must** reference an issue: `closes #N` — this drives the Kanban board automatically.

## Local Development

### Prerequisites

- Node.js 20+
- pnpm 9+

### Run

```bash
pnpm install
cp .env.local.example .env.local  # fill in Azure + API vars
pnpm dev
```

App runs at `http://localhost:3000`.

## Environment Variables

```env
NEXTAUTH_SECRET=
NEXTAUTH_URL=http://localhost:3000

AZURE_AD_CLIENT_ID=
AZURE_AD_CLIENT_SECRET=
AZURE_AD_TENANT_ID=

NEXT_PUBLIC_API_URL=http://localhost:8080
```

## Roles

| Role | Access |
|---|---|
| Guest | Public events, posts, public media — no login |
| Member | Assigned events, own task status updates, personal KPI |
| Admin | Full dashboard, all tasks, KPI reports, Excel export |
