# Put EMERGE WITH EBR on Netlify (v6.4, free Gemini AI coach)

The AI coach is a Netlify Function, so **dragging only the `dist` folder is not enough** (that uploads the website but
not the function; the app then falls back to the built-in writer and says so).

## Easiest way, no commands (GitHub in the browser + Netlify)
Netlify's drag-and-drop box cannot build this site or include the AI function, so use this 5-minute route instead:
1. Unzip this file on your computer.
2. On github.com create a new empty repository, click **uploading an existing file**, and drag in everything inside the unzipped folder (the files, `src`, `netlify` and `public` folders, not the zip itself). Click **Commit changes**.
3. On Netlify click **Add new site > Import an existing project > GitHub**, pick the repository and press **Deploy**. Nothing else to set up.
4. Add `GEMINI_API_KEY` under Site configuration > Environment variables, then **Deploys > Trigger deploy**.
Your new public guide is at `your-site.netlify.app/guide`.

## Option 1: deploy from Git (same thing, if you use git)
1. Put the unzipped project in a GitHub repository.
2. Netlify > Add new site > Import an existing project > pick the repo. `netlify.toml` does the rest.
3. Netlify > Site configuration > Environment variables > add `GEMINI_API_KEY` (a free key from https://aistudio.google.com/apikey). Trigger a new deploy.

## Option 2: Netlify CLI (needs Node 18+ on your computer)
```
npm install
npm install -g netlify-cli
netlify login
netlify link          # pick your existing site
netlify env:set GEMINI_API_KEY your-key-here
netlify deploy --build --prod
```
This builds the site and uploads the function together.

## Before you go live
- The Gemini free tier has usage limits. If it is ever busy, the app tells people to try again in a minute.
- Test the key: open `https://YOUR-SITE.netlify.app/.netlify/functions/coach?test=1`. You want `"keyWorks":true` and at least one model showing `"ok"` under `modelTests`. Google keys come in more than one format, so the key does not have to start with any particular letters. If every model says `400 INVALID_ARGUMENT` or `403`, the key is wrong or was pasted with extra characters.
- Never paste the key into the website code, chat, or a public GitHub repo. It belongs only in Netlify's environment variables (or `.env.local` on your computer, which is already in `.gitignore`).
- Check it works: sign in, write a profile, then tap "Make it longer". If you see "The AI coach isn't switched on",
  the key is missing; if you see "isn't deployed", the function wasn't uploaded (use Option 1 or 2).

## Already done earlier (do not repeat)
Firebase Email/Password sign-in is on, and your Netlify domain is in Authorized domains.
If you use a NEW Netlify address, add it under Firebase > Authentication > Settings > Authorized domains.
