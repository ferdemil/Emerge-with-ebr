# EMERGE WITH EBR

Create an account, answer a few questions, get a LinkedIn headline and About section.
Stack: Vite + React + TypeScript + Firebase Authentication (email and password only).

## How it works
- **Profile guide (v6.4):** a public page at `/guide` with 18 lessons covering a complete LinkedIn profile (photo, banner, headline, About,
  experience, education, skills, Featured, projects, recommendations, URL and contact info, settings, content, networking, opportunity
  readiness, credibility, search, and AI help). It has search, tips by career stage, copyable templates, mini checklists with saved progress,
  and links to the AI coach. Edit the wording in `src/lib/guide.ts`; the page itself is `src/pages/Guide.tsx`.
- **Accounts:** Firebase Authentication, email + password. Full name is saved as the Firebase `displayName`.
  The app never stores passwords, tokens or anything in Firestore. Firebase handles all of it.
- **Writing tool (v6.4, free Gemini AI coach):** the first draft and every follow-up come from Google Gemini (free tier) through a small
  Netlify Function (`netlify/functions/coach.mts`). The first draft gives one top headline plus three alternatives, an About
  section and coaching tips. After that you can tap quick changes or talk to the coach in your own words: ask questions, give new
  facts, or tell it exactly what to change. It updates your live profile, explains what it changed, never invents employers,
  titles or numbers, and your own manual edits are always sent along. After every answer the app checks LinkedIn's limits
  (headline 220 characters, About 2,600) and quietly asks the coach to fix anything over. Type "undo" (or tap **Undo last change**) to go back.
  v6.4 adds: automatic backup models and a retry when Gemini is busy, bigger answer budget (no more cut-off drafts), a **Work on** selector
  (whole profile, headlines, About, experience bullets or keywords) so changes stay targeted, visible experience bullets, keywords and a
  profile-strength score, and a quiet check that strips empty phrases like "passionate" or "team player" the person never wrote.
- **Safety net:** if the AI is unreachable or not set up, the original built-in writer (`src/lib/writer.ts`, runs in the
  browser, no service needed) writes the draft instead and the page says so. Chat is switched off in that case.
- **Protected app:** `/app` needs a signed-in user; everyone else is sent to `/login`.

## Firebase console: do this ONCE (the code can't do it for you)
Project: `emerge-with-ebr`
1. **Authentication > Get started > Sign-in method > Email/Password > Enable > Save.**
   Without this, sign-up fails (the site shows "Sign-in isn't switched on for this site yet").
2. **Authentication > Settings > Authorized domains > Add domain:** your Netlify domain
   (e.g. `your-site.netlify.app`, plus any custom domain). `localhost` is there by default.
   Email/password sign-in does not normally check this list, but add it anyway; it is required if you
   ever add Google sign-in or email links.
3. If sign-in says it is "not allowed" from your site, check **Google Cloud Console > APIs & Services >
   Credentials > the Browser key**: if "Website restrictions" are on, add your Netlify domain
   (`https://your-site.netlify.app/*`).

## AI coach setup (one time)
1. Get a free Gemini API key at https://aistudio.google.com/apikey.
2. Netlify > Site configuration > Environment variables > add `GEMINI_API_KEY` = your key. Redeploy.
3. Optional: `GEMINI_MODEL` (default `gemini-flash-latest`).
The key stays on Netlify's server. The browser never sees it. Only signed-in users can call the function
(it checks the Firebase login), and each person is limited to a few requests per minute.

## Deploy on Netlify
Deploy from Git (Import from Git) so the AI function is included. Dragging only the `dist` folder deploys the
website WITHOUT the AI function (the built-in writer still works). `netlify.toml` already handles
page refreshes on `/login`, `/register` and `/app`.

## Run locally
```bash
npm install
npm run dev
```

## Notes
- Answers are remembered on the device, separately per account (localStorage, key includes the user id).
- Passwords are minimum 8 characters in the form; Firebase itself requires at least 6.
