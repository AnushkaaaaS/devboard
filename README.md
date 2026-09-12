Devboard

Devboard is a Next.js application demonstrating different rendering strategies, GitHub OAuth authentication, protected routes, and ISR with a local JSON API.

Project Structure
devboard/
├── api/
│   ├── db.json
│   ├── package.json
│   └── package-lock.json
│
├── web/
│   ├── components/
│   │   └── Header.js
│   ├── data/
│   │   └── technologies.js
│   ├── pages/
│   │   ├── api/
│   │   │   ├── auth/
│   │   │   │   └── [...nextauth].js
│   │   │   ├── revalidate.js
│   │   │   └── todos.js
│   │   ├── stack/
│   │   │   └── [slug].js
│   │   ├── _app.js
│   │   ├── index.js
│   │   ├── profile.js
│   │   ├── search.js
│   │   └── todos.js
│   ├── middleware.js
│   ├── .env.example
│   └── package.json
│
└── README.md

Requirements
Node.js
npm
A GitHub account
GitHub OAuth application credentials
Installation

Clone the repository and enter the project:

git clone YOUR_REPOSITORY_URL
cd devboard

Install API dependencies
cd api
npm install

Install web dependencies
cd ../web
npm install

Environment Variables

Create:

web/.env.local


using .env.example as a template.

Required variables:

API_BASE_URL=http://localhost:4000
REVALIDATE_SECRET=your_revalidation_secret

GITHUB_ID=your_github_client_id
GITHUB_SECRET=your_github_client_secret

NEXTAUTH_SECRET=your_nextauth_secret
NEXTAUTH_URL=http://localhost:3000

NEXT_PUBLIC_APP_NAME=Devboard


Do not commit .env.local.

GitHub OAuth Setup

Create a GitHub OAuth application with:

Homepage URL:
http://localhost:3000

Authorization callback URL:
http://localhost:3000/api/auth/callback/github


Copy the generated Client ID and Client Secret into .env.local.

Running the Application

The API and web application run as separate processes.

Start json-server

From api/:

npm run dev


The API runs on:

http://localhost:4000

Start Next.js

From web/:

npm run dev


The application runs on:

http://localhost:3000

Production Testing

For testing ISR and production behavior:

cd web
npm run build
npm start


The production application runs on:

http://localhost:3000

Routes and Rendering Strategies
/

Uses Static Site Generation (SSG).

The page is generated at build time.

/stack/[slug]

Uses dynamic SSG with:

getStaticPaths()
getStaticProps()


Known technology slugs are generated at build time.

Unknown slugs return a 404.

/search

Uses Client-Side Rendering (CSR).

GitHub user searches happen in the browser and use a 400ms debounce.

/profile

Uses Server-Side Rendering (SSR) with:

getServerSideProps()


GitHub profile and repository information is fetched on every request.

The page also displays a server-generated timestamp.

/todos

Uses Incremental Static Regeneration (ISR).

The page uses:

getStaticProps()


with:

revalidate: 60


Todo data is retrieved from json-server.

/api/todos

Accepts POST requests and creates todos through json-server.

/api/revalidate

Provides protected on-demand revalidation for /todos.

Requests must provide the correct:

x-revalidate-secret


header.

Authentication

GitHub OAuth is implemented using NextAuth.

Authenticated users can access:

/profile
/todos


Middleware protects these routes from unauthenticated access.

The GitHub username is stored in the NextAuth JWT/session and is used by the server-rendered profile page.

Security
OAuth credentials are stored in .env.local.
.env.local is excluded from Git.
GitHub Client Secret is never exposed through a NEXT_PUBLIC_ variable.
The revalidation endpoint requires a secret header.
Protected routes are enforced using Next.js middleware.
Rendering Summary
Route	Rendering
/	SSG
/stack/[slug]	Dynamic SSG
/search	CSR
/profile	SSR
/todos	ISR
/api/todos	API Route
/api/revalidate	API Route
Testing Checklist
 Home page renders correctly.
 Technology pages render correctly.
 Unknown technology slug returns 404.
 GitHub search works with debounce.
 GitHub OAuth login works.
 Header displays authenticated user information.
 /profile displays GitHub profile data.
 /profile renders server-side on each request.
 Middleware protects /profile.
 Middleware protects /todos.
 /todos loads data from json-server.
 /todos uses 60-second ISR.
 New todos are created through /api/todos.
 On-demand revalidation works.
 Invalid revalidation secrets are rejected.
 .env.local is not committed.
 Production build succeeds.