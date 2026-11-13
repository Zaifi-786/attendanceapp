# Attendance Management System: Web App

React and Tailwind frontend for the Attendance Management System. Admin users only.
It talks to the ASP.NET Core Web API. The calls it needs are listed in [docs/API.md](docs/API.md).

## What is in it

| Screen | Requirement |
|---|---|
| Sign in, page guard, sign out clears the session | AUTH-1, AUTH-3 |
| Dashboard (counts, by department, 30 day trend, open alerts) | RPT-4 |
| Daily report and day detail | RPT-1, RPT-2 |
| Employee period report with totals | RPT-3 |
| Excel and PDF export buttons | RPT-5 |
| Unmapped and unscheduled punches | RPT-6, DEV-8 |
| Employees (search, filters, add, edit, deactivate) | EMP-1 to EMP-3 |
| Departments | DEP-1, DEP-2 |
| Shifts and shift assignment (one, department, selection) | SHF-1 to SHF-4 |
| Settings (grace period, flexi switch) | SET-1, SET-2 |
| Run analysis by hand | ANL-3, ANL-5 |
| Device status | DEV-10 |
| Audit log | AUTH-5 |

## Tech

React 19, React Router, Tailwind CSS 4, Vite. No UI or chart library.

## Getting started

You need Node 20 or newer and git.

```bash
git clone https://github.com/<your-username>/attendance-web.git
cd attendance-web
npm install
cp .env.example .env.local
npm run dev
```

Open http://localhost:5173.

### Two ways to run

**Demo mode (no backend needed).** In `.env.local` set `VITE_USE_MOCK=true`. The screens use fake data and
sign in with the static login from the same file:

```
VITE_STATIC_LOGIN_USER=your-email@example.com
VITE_STATIC_LOGIN_PASSWORD=your-password
```

**With the real API.** Set `VITE_USE_MOCK=false` and point `VITE_DEV_API_TARGET` at the running .NET API
(for example `https://localhost:7001`). The dev server forwards every `/api` call there, so CORS is not needed locally.

Restart `npm run dev` after changing any `.env` value.

## Environment variables

| Name | Meaning |
|---|---|
| `VITE_API_BASE_URL` | Where API calls go. `/api` if the site and API share a domain, or the full API address. |
| `VITE_DEV_API_TARGET` | Only for `npm run dev`. Address of the .NET API on your machine. |
| `VITE_USE_MOCK` | `true` for demo data and the static login, `false` for the real API. |
| `VITE_STATIC_LOGIN_USER` / `VITE_STATIC_LOGIN_PASSWORD` | Demo login. Only used when `VITE_USE_MOCK=true`. |

Anything starting with `VITE_` is copied into the built JavaScript and can be read by anyone who opens the site.
Do not use the static login in production, and never put real secrets in these variables.

## Build

```bash
npm run build
```

The finished site is in `dist/`. Steps for putting it on a server are in [DEPLOYMENT.md](DEPLOYMENT.md).

## Folder layout

```
src/
  api/          all API calls, one file per area (mock.js holds the demo data)
  components/   layout, route guard, small shared pieces
  context/      sign-in state
  hooks/        useFetch
  pages/        one file per screen
  utils/        date and minute formatting
docs/API.md     the API the frontend expects
```

## How it behaves

- The token is kept in `sessionStorage`. Closing the tab signs you out, and signing out clears it.
- Any 401 from the API sends the user to the sign-in page.
- Times are shown as the API sends them (local office time). There is no time zone conversion.
- Report totals come from the API. The app never adds up rows itself, so the figures match the stored results (RPT-7).

## Working on the code

1. Do not commit to `main` directly. Make a branch: `git checkout -b feature/short-name`.
2. Commit small changes with a clear message.
3. Push the branch and open a pull request on GitHub.

Repository setup and adding people is in [GITHUB_SETUP.md](GITHUB_SETUP.md).

## Related parts of the system

- **Web API** (ASP.NET Core, EF Core, SQL Server): separate repository or folder.
- **Device Sync Service** (.NET Worker Service with Hangfire): separate service for the K50 machines.
- **Attendance engine** (library): the analysis algorithm.

Each deploys on its own.
