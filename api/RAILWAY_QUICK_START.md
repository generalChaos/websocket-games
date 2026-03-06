# Railway Quick Start

## ✅ What's Been Set Up

Your project is now **dockerized** and ready for Railway deployment:

- ✅ `Dockerfile` - Multi-stage build for production
- ✅ `.dockerignore` - Excludes unnecessary files
- ✅ `railway.json` - Railway configuration
- ✅ CORS configuration - Supports environment-based origins
- ✅ Health check endpoint - `/health` for Railway monitoring
- ✅ Prisma scripts - `postinstall` and `migrate:deploy`

## 🚀 What to Create on Railway

### 1. **PostgreSQL Database**
   - Click "New" → "Database" → "Add PostgreSQL"
   - Railway auto-generates `DATABASE_URL`

### 2. **API Service**
   - Click "New" → "GitHub Repo"
   - Select your repository
   - **CRITICAL**: Set **Root Directory** to: `api`
     - Go to: Service → Settings → Root Directory → Enter: `api`
   - Railway will auto-detect the Dockerfile in `api/Dockerfile`

### 3. **Environment Variables**
   Set in API service:
   ```
   NODE_ENV=production
   CORS_ORIGINS=https://your-app.vercel.app
   ```
   - Replace with your actual Vercel URL (after deploying frontend)
   - `DATABASE_URL` is auto-linked from PostgreSQL

## 📋 Deployment Checklist

- [ ] Connect GitHub repo to Railway
- [ ] Add PostgreSQL service
- [ ] Add API service (root: `api/`)
- [ ] Set environment variables
- [ ] Deploy (automatic on push)
- [ ] Run migrations: `railway run npx prisma migrate deploy`
- [ ] Get API URL from Railway
- [ ] Deploy frontend to Vercel (see `../web/VERCEL_DEPLOYMENT.md`)
- [ ] Update `CORS_ORIGINS` in Railway with Vercel URL
- [ ] Set `NEXT_PUBLIC_API_URL` in Vercel environment variables

## 🐳 Docker vs Nixpacks

**Current Setup: Docker** ✅
- More control
- Smaller images (multi-stage build)
- Consistent builds
- Better for production

**Alternative: Nixpacks**
- Remove Dockerfile
- Railway auto-detects Node.js/NestJS
- Simpler but less control

## 💰 Estimated Cost

- PostgreSQL: ~$5/month
- API Service: ~$5-10/month
- **Total: ~$10-15/month**

## 📚 Full Guide

See `RAILWAY_DEPLOYMENT.md` for detailed instructions.
