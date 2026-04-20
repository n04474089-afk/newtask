# 🚀 Railway Deployment Guide for TrackTimi

## The Problem You Had
Railway's ephemeral storage deletes files on each deploy. Your SQLite database was being lost because it wasn't persisted properly.

## The Solution
We've configured your app to use Railway's volume system to persist the SQLite database.

---

## 📋 Deployment Steps

### Step 1: Verify Files Created
The following files have been added/updated:
- ✅ `railway.json` - Build config
- ✅ `Procfile` - Process definition
- ✅ `backend/config/db.js` - Updated for Railway volumes
- ✅ `.env.example` - Added Railway variables

### Step 2: Push to GitHub
```bash
git add .
git commit -m "Configure for Railway deployment with SQLite persistence"
git push origin dev-3
```

### Step 3: Connect to Railway
1. Go to [Railway.app](https://railway.app)
2. Create new project
3. Select "Deploy from GitHub repo"
4. Choose your `TrackTimi_TT` repository
5. Select `dev-3` branch

### Step 4: Configure Environment Variables in Railway Dashboard
In the Railway project dashboard, set these variables:

```
PORT=4000
JWT_SECRET=your-super-secure-secret-here-change-this
FRONTEND_URL=https://your-domain.up.railway.app
MAIN_DOMAIN=your-domain.up.railway.app
NODE_ENV=production
RAILWAY_DATA_DIR=/var/data
```

**Important:** 
- Change `JWT_SECRET` to a unique, long random string
- Replace `your-domain.up.railway.app` with your actual Railway domain

### Step 5: Add Volume for Database Persistence
In Railway Dashboard:
1. Go to your service settings
2. Find "Volumes" section
3. Add volume:
   - **Mount path:** `/var/data`
   - **Size:** 1GB (or more)

### Step 6: Configure Build & Deploy
In Railway settings:
- **Build command:** `npm install && cd backend && npm install`
- **Start command:** `npm start` (runs from backend/)
- **Nixpacks configuration:** Leave default

### Step 7: Deploy!
Click "Deploy" - Railway will:
1. Clone your repo
2. Install dependencies
3. Run your server
4. Persist database in volume

---

## 🔍 Troubleshooting

### Database file not persisting?
Check Railway logs for:
```
✅ TrackTimi DB connected: /var/data/tracktimi.db
```

If you see a different path, the `RAILWAY_DATA_DIR` variable isn't set.

### "Cannot find module 'sqlite3'"?
Railway might need build tools. Add to your backend/package.json:
```json
"optionalDependencies": {
  "sqlite3": "^5.0.2"
}
```

### Port issues?
Railway automatically sets `PORT` env var. Make sure your code uses `process.env.PORT`.
✅ Your `server.js` already does this correctly.

### CORS errors from frontend?
Update `FRONTEND_URL` in Railway env vars to match your frontend domain.

---

## 📊 Monitoring

Access logs in Railway Dashboard:
- Look for "✅ TrackTimi DB connected" on startup
- Check for any "Error" messages
- Database queries should appear in logs

---

## 🔄 Updates & Redeployment

After code changes:
```bash
git add .
git commit -m "Your changes"
git push origin dev-3
```

Railway automatically redeploys. Your database **persists** in the volume.

---

## 🆘 Still Having Issues?

Common Railway + SQLite issues:

1. **DB file disappearing** → Make sure volume is mounted at `/var/data`
2. **"Database locked" errors** → SQLite doesn't handle concurrent access well. Consider upgrading to PostgreSQL later if needed
3. **Large database slow** → SQLite is fine for small-medium apps. If >100MB, consider migration
4. **Permission denied** → Railway user needs write access to `/var/data`

---

## 💡 Next Steps (Optional)

Once your app works on Railway with SQLite:
- Monitor database performance
- If issues arise, migrate to PostgreSQL (Railway provides free tier)
- Set up automatic backups of your `/var/data` volume

**Your current setup will work great for TrackTimi without any code changes!**
