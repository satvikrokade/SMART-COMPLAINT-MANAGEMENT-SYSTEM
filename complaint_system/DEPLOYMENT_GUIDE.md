# Cloudflare + Render Deployment Guide

This project is best deployed as:
- **Frontend (React/Vite):** Cloudflare Pages
- **Backend (Django API):** Render (already running)

---

## 1) Deploy Frontend to Cloudflare Pages

1. Push your code to GitHub.
2. In Cloudflare dashboard, go to **Workers & Pages → Create application → Pages → Connect to Git**.
3. Select your repo and configure:
   - **Root directory:** `frontend`
   - **Build command:** `npm run build`
   - **Build output directory:** `dist`
4. Add environment variable in Pages:
   - `VITE_API_URL=https://<your-render-backend-domain>`
     - Example: `https://civic-pulse-backend.onrender.com`
5. Deploy.

After deploy, your frontend URL will look like:
- `https://<project-name>.pages.dev`

---

## 2) Configure Backend (Render) for Cloudflare domain

In Render service environment variables, set:

- `DEBUG=False`
- `ALLOWED_HOSTS=<render-domain>,<api-custom-domain-if-any>`
  - Example: `civic-pulse-backend.onrender.com,api.yourdomain.com`
- `CORS_ALLOWED_ORIGINS=<cloudflare-pages-url>,<your-custom-frontend-domain>`
  - Example: `https://civic-pulse.pages.dev,https://app.yourdomain.com`
- `CSRF_TRUSTED_ORIGINS=<cloudflare-pages-url>,<your-custom-frontend-domain>`
  - Example: `https://civic-pulse.pages.dev,https://app.yourdomain.com`

Then redeploy Render.

---

## 3) Optional: Use Custom Domains

- Cloudflare Pages custom domain for frontend: `app.yourdomain.com`
- Render custom domain for API (or proxied via Cloudflare): `api.yourdomain.com`

If you use custom domains, add them to:
- `VITE_API_URL` (frontend)
- `ALLOWED_HOSTS`, `CORS_ALLOWED_ORIGINS`, `CSRF_TRUSTED_ORIGINS` (backend)

---

## 4) Quick Verification

1. Open frontend URL (Pages):
   - `https://<project>.pages.dev`
2. Open backend docs:
   - `https://<backend-domain>/api/docs/`
3. Submit a complaint from frontend and confirm API requests succeed (browser devtools Network tab).

---

## 5) Common Issues

- **CORS error in browser**
  - Add exact frontend origin to `CORS_ALLOWED_ORIGINS`.
- **CSRF verification failed**
  - Add frontend origin to `CSRF_TRUSTED_ORIGINS`.
- **Host header disallowed**
  - Add backend host to `ALLOWED_HOSTS`.
- **Frontend calling wrong API URL**
  - Check Cloudflare Pages env var `VITE_API_URL` and redeploy.
