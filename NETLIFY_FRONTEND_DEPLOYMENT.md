# 🚀 Frontend Deployment Guide - Netlify

## What We've Configured

For your **Vue 3 + Vite + Tailwind CSS** frontend to deploy on Netlify:

- ✅ `netlify.toml` - Build & deployment config
- ✅ `.env.production` - Environment variables for production
- ✅ `vite.config.js` - Updated with optimized build settings

---

## 📋 Deployment Steps

### Step 1: Update Environment Variables

In `frontend/.env.production`, update these:

```env
# Replace with your actual backend URL (e.g., Railway backend)
VITE_API_BASE_URL=https://your-tracktimi-backend.up.railway.app
VITE_SOCKET_URL=https://your-tracktimi-backend.up.railway.app
```

**Note:** If you don't have your backend URL yet, deploy backend first on Railway, then come back and update this.

### Step 2: Connect to Netlify

1. Go to [Netlify.com](https://netlify.com) and sign up/login
2. Click **"Add new site"** → **"Import an existing project"**
3. Select **GitHub** and connect your repo
4. Choose the `TrackTimi_TT` repository
5. Select branch: `dev-3` (or `main` after merging)

### Step 3: Configure Build Settings

Netlify should auto-detect, but verify:

| Setting | Value |
|---------|-------|
| **Base directory** | `frontend/` |
| **Build command** | `npm run build` |
| **Publish directory** | `dist` |
| **Node version** | 22.x |

Click **"Site settings"** if you need to change these.

### Step 4: Add Environment Variables in Netlify

1. Go to **Site settings** → **Build & deploy** → **Environment**
2. Click **"Edit variables"** (or **"Add variables"**)
3. Add these variables:

```
VITE_API_BASE_URL = https://your-backend-url.railway.app
VITE_SOCKET_URL = https://your-backend-url.railway.app
VITE_GOOGLE_MAPS_API_KEY = your-key-here (optional)
```

### Step 5: Deploy!

1. Click **"Deploy site"**
2. Netlify will:
   - Clone your repo
   - Install dependencies (`npm install`)
   - Build frontend (`vite build`)
   - Deploy to Netlify CDN

✅ Your site will be live at: `https://<your-site-name>.netlify.app`

---

## 🔗 Frontend ↔️ Backend Communication

Your frontend makes API calls to the backend:

**Local Development:**
```javascript
// Uses vite proxy from vite.config.js
http://localhost:4000/api/*
```

**Production (Netlify):**
```javascript
// Uses VITE_API_BASE_URL from .env.production
https://your-backend.railway.app/api/*
```

Make sure your **backend CORS** allows your Netlify domain:

In `backend/server.js`, update:
```javascript
app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:5173',
  credentials: true
}));
```

Set in backend's environment variables:
```
FRONTEND_URL=https://your-tracktimi-frontend.netlify.app
```

---

## 🔍 Monitoring Deploys

In Netlify Dashboard:
- **Deploys** tab shows all deployments
- **Logs** show build output
- **Analytics** shows traffic

---

## ⚡ Build Optimization

Your `vite.config.js` now includes:
- ✅ Code splitting for faster loads
- ✅ No source maps in production (smaller)
- ✅ Optimized chunk bundling

Build size will be ~200-300KB (gzipped).

---

## 🆘 Common Issues

### "Cannot find /api endpoints"
- Check `VITE_API_BASE_URL` is set correctly in Netlify
- Verify backend is running and accessible
- Check CORS settings in backend

### "Build fails - Node version"
- Netlify defaults to older Node versions
- Make sure `netlify.toml` specifies `NODE_VERSION = "22.12.0"`

### "Styles not loading"
- Vite + Tailwind should work automatically
- Clear Netlify cache: **Site settings** → **Clear cache and redeploy**

### "Socket.IO not connecting"
- Set `VITE_SOCKET_URL` to your backend URL
- Backend must allow WebSocket connections

---

## 📊 What Happens on Deploy

1. Netlify gets code from GitHub
2. Installs dependencies: `npm install` in frontend/
3. Runs build: `vite build`
4. Creates optimized `dist/` folder
5. Deploys `dist/` to Netlify edge network
6. Routes all requests to `index.html` (for Vue Router)

---

## 🔄 Automatic Deploys

Every time you push to `dev-3` branch:
1. Netlify detects the push
2. Automatically rebuilds
3. Deploys new version

No manual deployment needed! 🎉

---

## 💡 Next Steps

1. **Commit changes:**
   ```bash
   git add frontend/netlify.toml frontend/vite.config.js frontend/.env.production
   git commit -m "Configure frontend for Netlify deployment"
   git push origin dev-3
   ```

2. **Deploy backend first** on Railway (if not done)

3. **Update `.env.production`** with backend URL

4. **Connect to Netlify** and deploy

5. **Test:** Visit your Netlify domain and check console for API errors

Your frontend will be live! 🚀
