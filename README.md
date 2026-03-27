# elysia-anon-chat

An anonymous messaging app where users can send messages to each other without revealing their identity. Built with Next.js, Elysia.js, and Drizzle ORM.

## Features

- **Anonymous messaging** — send messages to any user without revealing who you are
- **Conversation management** — view, reply to, and delete conversations
- **Block accounts** — block users from sending you messages
- **Message accepting toggle** — turn off incoming anonymous messages at any time
- **Authentication** — email/password (Gmail only) and OAuth via Google & GitHub
- **Admin panel** — role-based admin access with impersonation support
- **OpenAPI docs** — auto-generated API documentation via Scalar

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | [Next.js 16](https://nextjs.org) (App Router) |
| Runtime | [Bun](https://bun.sh) |
| API | [Elysia.js](https://elysiajs.com) (mounted as Next.js API routes) |
| Database | [Neon](https://neon.tech) (serverless PostgreSQL) |
| ORM | [Drizzle ORM](https://orm.drizzle.team) |
| Auth | [Better Auth](https://www.better-auth.com) |
| UI | [Tailwind CSS v4](https://tailwindcss.com), [shadcn/ui](https://ui.shadcn.com), [Tabler Icons](https://tabler.io/icons), [Lucide](https://lucide.dev) |
| State | [TanStack Query](https://tanstack.com/query) |
| Forms | [React Hook Form](https://react-hook-form.com) + [Zod](https://zod.dev) |
| API Client | [Elysia Eden](https://elysiajs.com/eden/overview.html) (fully typed) |
| Deployment | [Vercel](https://vercel.com) |

## Getting Started

### Prerequisites

- [Bun](https://bun.sh) v1.x or later
- A [Neon](https://neon.tech) (or compatible) PostgreSQL database
- Google and/or GitHub OAuth app credentials (optional, for social login)

### 1. Install dependencies

```bash
bun install
```

### 2. Configure environment variables

Create a `.env.local` file in the project root:

```env
# Database
DATABASE_URL=postgresql://user:password@host/dbname

# Better Auth
BETTER_AUTH_URL=http://localhost:3000
NEXT_BETTER_AUTH_URL=http://localhost:3000

# Google OAuth (optional)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=http://localhost:3000/api/auth/callback/google

# GitHub OAuth (optional)
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
GITHUB_REDIRECT_URI=http://localhost:3000/api/auth/callback/github
```

### 3. Run database migrations

```bash
bun run db:migrate
```

### 4. Start the development server

```bash
bun run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

## Scripts

| Command | Description |
|---|---|
| `bun run dev` | Start the development server |
| `bun run build` | Build for production |
| `bun run start` | Start the production server |
| `bun run lint` | Run ESLint |
| `bun run db:generate` | Generate Drizzle migration files |
| `bun run db:migrate` | Apply pending migrations |
| `bun run db:push` | Push schema changes directly (dev only) |
| `bun run auth:generate` | Regenerate Better Auth schema |

## Project Structure

```
├── app/
│   ├── (app)/          # Authenticated app routes (dashboard, user, admin)
│   ├── (auth)/         # Auth routes (sign-in, sign-up)
│   └── api/            # Elysia API routes
├── components/         # Shared UI components
├── db/
│   ├── drizzle.ts      # Database client
│   └── schema.ts       # Drizzle table definitions & relations
├── lib/
│   ├── auth.ts         # Better Auth server config
│   ├── auth-client.ts  # Better Auth browser client
│   └── elysia-client.ts# Typed Eden API client
├── migrations/         # Drizzle migration files
├── modules/            # Elysia route controllers (messages, conversations, etc.)
└── server/             # Server actions
```

## API Documentation

The Scalar OpenAPI UI is available at `/api/reference` when the development server is running.

## Auth Notes

- Email/password sign-up requires a **Gmail address** (`@gmail.com`)
- Usernames must be between **8 and 20 characters** and cannot be `admin`
- Sessions expire after **7 days**

## Deployment

This project is configured for deployment on [Vercel](https://vercel.com) with Bun as the runtime (`bunVersion: "1.x"` in `vercel.json`).

1. Push your code to GitHub
2. Import the repository on Vercel
3. Set all environment variables from the `.env.local` template above
4. Deploy
