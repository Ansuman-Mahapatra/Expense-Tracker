## FinSync – Deployment & Configuration Help

This file explains **what you must change** when you move from local development to a deployed / production setup.

---

### 1. OpenAI API key – where to set it

**Local development (already done):**
- File: `expenses_tracker_backend/src/main/resources/application.properties`
- Property:
  - `openai.api.key=YOUR_OPENAI_KEY_HERE`

**For production / when publishing:**
- **Do NOT hardcode** your real key in the file that gets committed to git.
- Instead, use an **environment variable** and read it from there.

Recommended change in `application.properties`:
- Replace the hardcoded value with:
  - `openai.api.key=${OPENAI_API_KEY:}`

Then, in your production environment (Railway, Docker, VPS, etc.), set:
- Environment variable: `OPENAI_API_KEY`
- Value: your real OpenAI project key

This way:
- Local dev can still use a local `.env` or local override.
- Production will read from the environment and you won’t leak the key in git.

---

### 2. Backend base URL – what the frontend calls

File: `expenses_tracker_frontend/src/services/api.js`

Current setting:
- `baseURL: "http://localhost:8081/api",`

**For production:**
- Change `baseURL` to your deployed backend URL, for example:
  - `baseURL: "https://your-domain-or-backend-url.com/api",`

Typical scenarios:
- **Railway / similar PaaS**: use the public URL they give you, e.g.  
  `baseURL: "https://your-railway-app-url.up.railway.app/api",`
- **Docker + Nginx on a server**: use your domain, e.g.  
  `baseURL: "https://fin-sync.yourdomain.com/api",`

After you deploy, **update only this one line** so the React app talks to the correct backend.

---

### 3. Ports and CORS

Backend runs on port `8081` locally, frontend on `3000`.

When deploying:
- If both frontend and backend are behind the **same domain** (e.g. Nginx reverse proxy):
  - Keep API under `/api` and adjust Nginx, not the Java code.
- If you serve frontend and backend from **different origins**, ensure CORS is configured to allow the frontend origin in `CorsConfig` / security config (backend).

You normally **do not need to change ChatController** when publishing; just make sure:
- The backend is reachable at the `baseURL` you configured in `api.js`.
- CORS allows your frontend origin.

---

### 4. Chatbot specific configuration

The chatbot depends on:
1. **OpenAI key** → `openai.api.key` (see section 1).
2. **API base URL** → `baseURL` in `src/services/api.js` (see section 2).

If after deployment the chatbot fails:
- Open browser DevTools → Network → click a `/api/chat` request.
- Check:
  - **Status**:
    - `200` → OK, backend responded.
    - `401/403` → auth / CORS issue.
    - `404` → base URL or path wrong.
    - `429` → OpenAI rate limit.
  - **Request URL**: must match your deployed backend URL.

---

### 5. Docker / docker-compose notes

You have a `docker-compose.yml` and Dockerfiles in the project.

When running in Docker:
- Pass env vars in `docker-compose.yml`, for example:

```yaml
services:
  backend:
    environment:
      - OPENAI_API_KEY=your_real_key_here
```

- Ensure backend container exposes the right port and that the frontend uses the correct **public URL** of the backend in `api.js`.

---

### 6. Quick checklist before publishing

1. **OpenAI key**
   - [ ] Remove hardcoded key from `application.properties` or replace with `${OPENAI_API_KEY:}`.
   - [ ] Set `OPENAI_API_KEY` in your deployment environment.
2. **Frontend API base URL**
   - [ ] Change `baseURL` in `src/services/api.js` from `http://localhost:8081/api` to your real backend URL.
3. **Build frontend**
   - [ ] Run `npm run build` in `expenses_tracker_frontend` for production assets.
4. **Test chatbot**
   - [ ] After deploy, open the app, send a finance-related question, and a quick action (e.g. **Show my budgets**) to confirm everything works.

Keep this file updated as you change hosting or add new external services.


Commit at 2025-10-16T01:02:47
Commit at 2025-09-04T23:39:43
Commit at 2025-10-04T18:47:13
Commit at 2025-10-22T14:06:43
Commit at 2025-09-07T14:53:08
Commit at 2025-09-13T18:45:20
Commit at 2025-10-16T20:14:25
Commit at 2025-09-12T07:33:05
Commit at 2025-09-17T06:55:27
Commit at 2025-10-18T06:41:50
Commit at 2025-10-25T03:16:46
Commit at 2025-09-06T20:50:25
Commit at 2025-10-11T00:37:22
Commit at 2025-11-27T05:37:04
Commit at 2025-09-05T00:22:01
Commit at 2025-08-11T04:03:20
Commit at 2025-11-05T18:18:30
Commit at 2025-09-15T13:58:33
Commit at 2025-09-29T22:33:42
Commit at 2025-10-12T04:52:01
Commit at 2025-08-24T16:48:59
Commit at 2025-08-27T21:54:09
Commit at 2025-11-08T02:36:55
Commit at 2025-11-09T23:05:15
Commit at 2025-08-12T15:51:14
Commit at 2025-12-03T18:28:25
Commit at 2025-10-09T18:39:45
Commit at 2025-11-09T21:32:09
Commit at 2025-10-08T06:30:20
Commit at 2025-10-10T15:10:27
