# Dundumon AI — Free-first Vercel starter

A responsive AI chat site with animated gradients and **no image generation**. The browser never receives the Gemini API key; all AI calls go through `/api/chat` on Vercel.

## What you need

- A GitHub account
- A Vercel account
- A Gemini API key from Google AI Studio

Hosting may fit Vercel's free tier. Gemini may offer a free quota depending on account, region, model, and current policy. Free quotas/rate limits can change; unlimited free usage is not guaranteed.

## Deploy without localhost

1. Extract this ZIP.
2. Create a new GitHub repository and upload the contents of this folder. `api/` and `public/` must be at the repository root.
3. Sign in at https://vercel.com and choose **Add New → Project**.
4. Import your GitHub repository. Vercel should detect the API function automatically. Leave the build command empty if prompted; the frontend is static and the backend is a Vercel function.
5. In **Project Settings → Environment Variables**, add:
   - `GEMINI_API_KEY` = your secret key from https://aistudio.google.com/app/apikey
   - `GEMINI_MODEL` = `gemini-2.5-flash` (optional; this is the default in code)
6. Deploy. If you add or change environment variables later, redeploy the project.
7. Open the public `*.vercel.app` URL. No localhost is needed for normal use.

## Optional local development

Install Node.js, then run:

```bash
npm install
npx vercel dev
```

Create a `.env` file based on `.env.example` for local testing. Never commit `.env` or expose your API key in `public/index.html`.

## Notes

- This project includes AI chat only; there is no image generation or live web-search integration.
- AI provider quotas and availability can change. When a free quota is exhausted, wait for it to reset or use a provider/model that is available to your account.
- Add rate limiting or authentication before sharing the site widely to reduce abuse of your API quota.
- Do not commit API keys to GitHub. Keep secrets in Vercel Environment Variables.
