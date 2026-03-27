This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

## About Codebase

### Overview

This is a **Next.js anonymous chat application** where users can send messages to others by username without the recipient knowing who sent them (anonymous sender). It uses **Bun** as the runtime and package manager.

---

### Key Technologies

| Layer | Technology |
|---|---|
| Runtime | **Bun** |
| Frontend framework | **Next.js 16** (App Router) |
| Backend API framework | **Elysia** (Bun-native HTTP framework) |
| Authentication | **BetterAuth** with Google, GitHub OAuth + email/password |
| Database | **PostgreSQL** via **Neon** (serverless) |
| ORM | **Drizzle ORM** + `drizzle-zod` for schema validation |
| State management | **TanStack Query (React Query)** |
| UI components | **shadcn/ui** + Tailwind CSS v4 |
| Form handling | **React Hook Form** + **Zod** |
| API client | **Eden** (Elysia's type-safe client, similar to tRPC) |
| Deployment | **Vercel** |

---

### Directory Structure

```
├── app/
│   ├── (auth)/             # Auth route group: sign-in, sign-up pages
│   ├── api/
│   │   ├── [[...slug]]/route.ts   # Catch-all route — mounts the Elysia app into Next.js
│   │   └── plugins/
│   │       └── better-auth-plugin.ts   # Elysia macro for session-based auth guard
│   ├── layout.tsx          # Root layout: wraps app with providers (TanStack, Auth, Theme)
│   └── page.tsx            # Home page: lists active conversations
│
├── modules/                # Feature modules (Elysia controllers)
│   ├── messages/           # POST /api/user/send/message — initiate a new anonymous message
│   ├── get-messages/       # GET /api/messages/:conversationId — fetch messages in a thread
│   ├── get-conversation/   # GET /api/conversations — list all conversations for the user
│   ├── reply-back/         # POST /api/reply-message — reply within an existing conversation
│   ├── delete-conversation/# POST /api/deleteconvo/:id — delete a conversation (owner only)
│   └── block-account/      # POST /api/block/:userId — block a user
│
├── db/
│   ├── drizzle.ts          # Drizzle/Neon client setup
│   └── schema.ts           # All DB table definitions + Zod schemas
│
├── lib/
│   ├── auth.ts             # BetterAuth config (plugins, social providers, hooks)
│   ├── auth-client.ts      # BetterAuth client-side helper
│   ├── elysia-client.ts    # Eden treaty client (typed API calls from the frontend)
│   └── utils.ts            # Utility helpers (cn, etc.)
│
├── components/
│   ├── custom/             # App-specific components (Navbar, MessageForm, ConversationTile, etc.)
│   └── ui/                 # shadcn/ui auto-generated components
│
├── server/
│   └── action.ts           # Next.js Server Actions (e.g. getUserSession)
│
└── migrations/             # Drizzle migration SQL files
```

---

### How It Works

#### API Architecture
Elysia runs **inside Next.js** via the `app/api/[[...slug]]/route.ts` catch-all. All HTTP methods (`GET`, `POST`, etc.) are forwarded to `app.fetch`, so Elysia handles routing, validation, and responses. This is then exported as a type (`App`) so Eden can generate a fully type-safe client.

#### Authentication (`better-auth-plugin.ts`)
A custom Elysia **macro** called `auth: true` is available on any route. When set, it calls `auth.api.getSession()` and injects `user` + `session` directly into the Elysia context. If no valid session exists, it returns a 401.

#### Database Schema (key app tables)
- **`user`** — extended with `username`, `isPremiumUser`, `isAcceptingMessages`
- **`conversation`** — links a `senderId` → `receiverId`; each sender gets their own unique conversation thread even if two users message each other
- **`message`** — belongs to a conversation, authored by `authorId`; has `isRead` flag
- **`blockedAccounts`** — records who blocked whom

#### Anonymous Messaging Flow
1. User A sends a message to User B **by username** (`POST /api/user/send/message`)
2. A new conversation is created with A as `senderId`, B as `receiverId` (or reuses an existing one if A already messaged B)
3. User B sees the conversation but **does not see who sent the initial message** (the `authorId` is stored but the UI concept is anonymous)
4. B can reply within the same conversation thread (`POST /api/reply-message`)
5. Either party can delete the conversation (only the original sender can delete)
6. Users can block others (`POST /api/block/:userId`)

#### Frontend Data Fetching
The frontend uses **Eden** (`lib/elysia-client.ts`) to call the Elysia API with full TypeScript type inference — similar to tRPC but for Elysia. Data fetching is managed by **TanStack Query**.

---

### Notable Design Choices
- **BetterAuth** enforces that only `@gmail.com` emails can sign up (enforced via a `before` hook)
- Usernames are auto-derived from the email prefix on signup
- Sessions are cached for 5 minutes client-side for performance
- The app supports admin impersonation and organization/team structures (via BetterAuth plugins), though these appear to be infrastructure set up for future use
- OpenAPI docs are auto-generated from both Elysia routes and BetterAuth routes and merged together

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts) to automatically optimize and load [Geist](https://vercel.com/font), a new font family for Vercel.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/app/building-your-application/deploying) for more details.
