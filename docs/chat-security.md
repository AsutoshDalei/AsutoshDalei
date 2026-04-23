# Chat Security Notes

This portfolio is hosted on GitHub Pages (static hosting).  
Static frontend code is public, so API keys must never be embedded in JavaScript.

## Current Safe Design

- `assets/js/chat.js` calls a backend endpoint via `CHAT_API_ENDPOINT`.
- If no endpoint is configured, chat is automatically disabled.
- GitHub Actions only injects the endpoint URL (from repo variable), not any secret.

## Required Backend Controls

Your backend proxy (Cloudflare Worker / Vercel Function / FastAPI / etc.) should enforce:

1. **Server-side API key usage only**
   - Keep OpenRouter key in backend environment variable.
   - Never return the key to clients.

2. **Strict origin allowlist**
   - Only accept requests from `https://asutoshdalei.github.io`.
   - Return `403` for other origins.

3. **Per-IP rate limiting**
   - Example: 10 requests / 5 min, with temporary blocks on bursts.

4. **Bot protection**
   - Add Cloudflare Turnstile or hCaptcha verification before proxying to model API.

5. **Input validation**
   - Max input size.
   - Block malformed payloads and non-JSON requests.

6. **Cost safeguards**
   - Enforce max tokens and model allowlist on backend regardless of client payload.

7. **Monitoring**
   - Log request metadata (no sensitive user content if you want privacy-first).
   - Alert on traffic spikes and repeated abuse patterns.

## GitHub Setup

- Set repository variable `CHAT_API_ENDPOINT` to your backend URL.
- Do not add `OPENROUTER_API_KEY` to frontend build steps.
