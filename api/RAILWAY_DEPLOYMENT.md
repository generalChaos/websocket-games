# Railway Deployment Guide

This guide will help you deploy the Party Game API to Railway.

## Prerequisites

1. Railway account (sign up at [railway.app](https://railway.app))
2. GitHub repository connected to Railway
3. PostgreSQL database (Railway provides this)

## What You Need to Create on Railway

### 1. PostgreSQL Database Service

1. In Railway dashboard, click **"New Project"**
2. Click **"New"** → **"Database"** → **"Add PostgreSQL"**
3. Railway will automatically create a PostgreSQL instance
4. Note the `DATABASE_URL` from the service variables (Railway sets this automatically)

### 2. API Service

1. In the same project, click **"New"** → **"GitHub Repo"**
2. Select your repository
3. Railway will detect the `api/` directory or you can set the root directory to `api/`
4. Railway will automatically detect the Dockerfile and build

### 3. Environment Variables

Set these in your API service:

#### Required Variables:
- `DATABASE_URL` - Automatically set by Railway when you link the PostgreSQL service
- `PORT` - Automatically set by Railway (don't override)
- `NODE_ENV=production`
- `CORS_ORIGINS` - Comma-separated list of allowed origins (e.g., `https://your-app.vercel.app,https://www.yourdomain.com`)
  - **Important**: Must include your Vercel frontend URL
  - Format: `https://your-app.vercel.app` (no trailing slash)

## Deployment Steps

### Step 1: Connect Repository

1. Go to Railway dashboard
2. Click **"New Project"**
3. Select **"Deploy from GitHub repo"**
4. Choose your repository

### Step 2: Add PostgreSQL

1. In your project, click **"New"** → **"Database"** → **"Add PostgreSQL"**
2. Wait for it to provision (takes ~1 minute)
3. The `DATABASE_URL` will be automatically available to other services

### Step 3: Add API Service

1. Click **"New"** → **"GitHub Repo"**
2. Select your repository again
3. In service settings, set:
   - **Root Directory**: `api`
   - **Build Command**: (auto-detected from Dockerfile)
   - **Start Command**: (auto-detected from Dockerfile)

### Step 4: Configure Environment Variables

1. Go to your API service → **Variables** tab
2. Add:
   ```
   NODE_ENV=production
   CORS_ORIGINS=https://your-app.vercel.app
   ```
   - Replace `your-app.vercel.app` with your actual Vercel URL
   - Add multiple origins separated by commas if needed
   - No trailing slashes
3. `DATABASE_URL` is automatically linked from PostgreSQL service

**Important**: After deploying your frontend to Vercel, come back and update `CORS_ORIGINS` with your Vercel URL.

### Step 5: Run Database Migrations

After first deployment, run Prisma migrations:

1. Go to your API service → **Deployments** tab
2. Click on the latest deployment → **View Logs**
3. Or use Railway CLI:
   ```bash
   railway run npx prisma migrate deploy
   ```

Alternatively, add to your Dockerfile or use a one-off command in Railway.

### Step 6: Get Your API URL

1. Go to your API service → **Settings** → **Networking**
2. Generate a domain (or use Railway's auto-generated one)
3. Copy the URL (e.g., `https://your-api.railway.app`)

## Docker vs Nixpacks

### Current Setup: Docker (Recommended)

The project includes a `Dockerfile` which gives you:
- ✅ Full control over build process
- ✅ Multi-stage builds (smaller images)
- ✅ Consistent builds across environments
- ✅ Better caching

### Alternative: Nixpacks (Auto-detection)

Railway can also auto-detect and build without Docker:
- Remove `Dockerfile` and `railway.json`
- Railway will use Nixpacks to detect Node.js/NestJS
- Less control but simpler setup

**We recommend Docker** for production deployments.

## Health Check

The API includes a health endpoint at `/health` that Railway uses for health checks.

## Monitoring

- View logs in Railway dashboard → Service → **Deployments** → **View Logs**
- Set up alerts in Railway → Service → **Settings** → **Alerts**

## Troubleshooting

### Build Fails

1. Check build logs in Railway dashboard
2. Ensure `package.json` has correct build scripts
3. Verify Dockerfile syntax

### Database Connection Issues

1. Ensure PostgreSQL service is running
2. Check `DATABASE_URL` is set correctly
3. Verify migrations have run: `railway run npx prisma migrate deploy`

### WebSocket Not Working

1. Ensure CORS_ORIGINS includes your frontend URL
2. Check Railway networking settings (WebSocket support is automatic)
3. Verify the API URL uses `wss://` (secure WebSocket) in production

### Port Issues

- Railway automatically sets `PORT` environment variable
- Don't hardcode port numbers
- The app already uses `process.env.PORT` in `main.ts`

## Cost Estimate

- **PostgreSQL**: ~$5/month (Hobby plan)
- **API Service**: ~$5-10/month (depending on usage)
- **Total**: ~$10-15/month for small apps

## Next Steps

1. ✅ Deploy API to Railway (this guide)
2. ✅ Get Railway API URL
3. Deploy frontend to Vercel (see `../web/VERCEL_DEPLOYMENT.md`)
4. Update `CORS_ORIGINS` in Railway with your Vercel URL
5. Test WebSocket connections
6. Set up custom domains (optional)

## Useful Commands

```bash
# Install Railway CLI
npm i -g @railway/cli

# Login
railway login

# Link to project
railway link

# View logs
railway logs

# Run migrations
railway run npx prisma migrate deploy

# Open service
railway open
```
