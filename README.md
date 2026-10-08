# fireship

Two full-stack Next.js 13 apps I built while working through Fireship's Next.js course. Both use the App Router, React Server Components, and TypeScript.

| Project | What it is | Stack |
| --- | --- | --- |
| [`myspace/`](./myspace) | A small social network with GitHub login, editable profiles, and following | Next.js 13, NextAuth.js, Prisma, PostgreSQL |
| [`crudapp/`](./crudapp) | A notes app with create, list, and detail views | Next.js 13, PocketBase |

---

## myspace

A MySpace-style social app.

**Features**
- **GitHub OAuth sign-in** with NextAuth.js; sessions and accounts are stored in Postgres through the Prisma adapter
- **User directory** (`/users`) and **profile pages** (`/users/[id]`), with loading and error states
- **Dashboard** (`/dashboard`) where signed-in users edit their name, bio, age, and avatar
- **Follow and unfollow** other users: a `Follows` join table, a REST endpoint (`POST`/`DELETE /api/follow`), and a client follow button
- **Blog routes** (`/blog/[slug]`) with a custom 404 page
- **Protected API** (`/api/content`) that only returns data to authenticated sessions

**Data model** (`prisma/schema.prisma`): the standard NextAuth models (`User`, `Account`, `Session`, `VerificationToken`), plus profile fields on `User` and a self-referencing many-to-many `Follows` relation.

### Running it locally

You need Node 18+, a PostgreSQL database, and a [GitHub OAuth app](https://github.com/settings/developers) with the callback URL set to `http://localhost:3000/api/auth/callback/github`.

```bash
cd myspace
npm install
```

Create `myspace/.env`:

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:5432/myspace"
SHADOW_DATABASE_URL="postgresql://USER:PASSWORD@HOST:5432/myspace_shadow"
GITHUB_ID="your-github-oauth-client-id"
GITHUB_SECRET="your-github-oauth-client-secret"
NEXTAUTH_SECRET="any-long-random-string"
NEXTAUTH_URL="http://localhost:3000"
```

Then apply the migrations and start the dev server:

```bash
npx prisma migrate dev
npm run dev
```

Open http://localhost:3000.

---

## crudapp

A minimal notes app, built to practice server-side data fetching and caching in the App Router.

**Features**
- `/notes` lists notes, fetched on the server with `cache: 'no-store'` so the list is always fresh
- `/notes/[id]` shows one note, using incremental revalidation (`revalidate: 10`) and a loading skeleton
- A client component creates notes and refreshes the route afterwards

The backend is [PocketBase](https://pocketbase.io/), a single-file backend with its own database. The schema lives in `pb_migrations/`.

### Running it locally

```bash
cd crudapp
npm install

# In one terminal, start PocketBase on port 8090.
# The repo includes the Windows binary; on macOS or Linux, download PocketBase and run it the same way.
./pocketbase serve

# In another terminal:
npm run dev
```

The app expects PocketBase at `http://127.0.0.1:8090`, with a `notes` collection that has `title` and `content` fields.

---

## Acknowledgements

Course material by [Fireship](https://fireship.io). The code, and the extra features beyond the lessons, are mine.
