# Vercel Deployment Guide

This guide covers deploying the Next.js frontend to Vercel.

## Architecture

```
┌─────────────────┐
│  Vercel (Web)   │  ← Next.js Frontend (this service)
│  (Free tier)    │
└────────┬────────┘
         │
         │ HTTPS/WSS
         │
┌────────▼────────┐
│ Railway (API)   │  ← NestJS + WebSocket Server
│  PostgreSQL     │  ← Database
└─────────────────┘
```

## Prerequisites

1. Vercel account (sign up at [vercel.com](https://vercel.com))
2. GitHub repository
3. Railway API deployed and URL available

## Deployment Steps

### Step 1: Connect Repository to Vercel

1. Go to [vercel.com](https://vercel.com)
2. Click **"Add New"** → **"Project"**
3. Import your GitHub repository
4. Vercel will auto-detect Next.js

### Step 2: Configure Project Settings

1. **Root Directory**: Set to `web` (if deploying from monorepo)
2. **Framework Preset**: Next.js (auto-detected)
3. **Build Command**: `npm run build` (auto-detected)
4. **Output Directory**: `.next` (auto-detected)
5. **Install Command**: `npm install` (auto-detected)

### Step 3: Set Environment Variables

Go to **Settings** → **Environment Variables** and add:

#### Required Variables:

```
NEXT_PUBLIC_API_URL=https://your-api.railway.app
```

**Important**: 
- Replace `your-api.railway.app` with your actual Railway API URL
- Use `https://` for HTTP and it will automatically use `wss://` for WebSocket
- The `NEXT_PUBLIC_` prefix makes it available in the browser

#### Optional Variables (if using Supabase):

```
NEXT_PUBLIC_SUPABASE_URL=your-supabase-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-supabase-key
```

### Step 4: Deploy

1. Click **"Deploy"**
2. Vercel will build and deploy automatically
3. You'll get a URL like: `https://your-app.vercel.app`

### Step 5: Update Railway CORS

After getting your Vercel URL, update Railway API CORS:

1. Go to Railway → API Service → **Variables**
2. Update `CORS_ORIGINS`:
   ```
   CORS_ORIGINS=https://your-app.vercel.app,https://www.yourdomain.com
   ```
3. Redeploy the API service

## Environment Variables Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `NEXT_PUBLIC_API_URL` | Railway API URL (required) | `https://api.railway.app` |
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase URL (optional) | `https://xxx.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase key (optional) | `eyJhbGc...` |

## WebSocket Configuration

The frontend automatically converts HTTP to WebSocket:
- `https://api.railway.app` → `wss://api.railway.app` (for WebSocket)

This is handled in `src/shared/config/index.ts` via the `getApiUrl()` function.

## Custom Domain (Optional)

1. Go to **Settings** → **Domains**
2. Add your custom domain
3. Follow DNS configuration instructions
4. Update Railway `CORS_ORIGINS` to include your custom domain

## Troubleshooting

### API Connection Fails

1. **Check `NEXT_PUBLIC_API_URL`**:
   - Must start with `https://`
   - No trailing slash
   - Example: `https://api.railway.app`

2. **Check Railway CORS**:
   - Ensure Vercel URL is in `CORS_ORIGINS`
   - Format: `https://your-app.vercel.app` (no trailing slash)

3. **Check Railway API**:
   - Verify API is running: `https://your-api.railway.app/health`
   - Check Railway logs for errors

### WebSocket Connection Fails

1. **Verify WebSocket URL**:
   - Should be `wss://` (secure WebSocket)
   - Check browser console for connection errors

2. **Check Railway Networking**:
   - Railway supports WebSocket automatically
   - No additional configuration needed

3. **Check CORS**:
   - WebSocket connections also respect CORS
   - Ensure Vercel URL is in `CORS_ORIGINS`

### Build Fails

1. **Check Node.js version**:
   - Vercel auto-detects from `package.json`
   - Next.js 16 requires Node 18+

2. **Check dependencies**:
   - Ensure `package.json` has all dependencies
   - Check build logs for missing packages

## Cost

- **Vercel Free Tier**: 
  - 100GB bandwidth/month
  - Unlimited deployments
  - Perfect for small/medium apps

- **Vercel Pro**: $20/month (if you need more)

## Continuous Deployment

Vercel automatically deploys on every push to:
- `main` branch → Production
- Other branches → Preview deployments

## Useful Commands

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy to preview
vercel

# Deploy to production
vercel --prod

# View logs
vercel logs
```

## Next Steps

1. ✅ Deploy to Vercel
2. ✅ Set `NEXT_PUBLIC_API_URL` environment variable
3. ✅ Update Railway `CORS_ORIGINS` with Vercel URL
4. ✅ Test WebSocket connections
5. ✅ Set up custom domain (optional)

## Quick Checklist

- [ ] Repository connected to Vercel
- [ ] Root directory set to `web` (if monorepo)
- [ ] `NEXT_PUBLIC_API_URL` environment variable set
- [ ] Railway API deployed and URL available
- [ ] Railway `CORS_ORIGINS` includes Vercel URL
- [ ] Test API connection from Vercel app
- [ ] Test WebSocket connection
- [ ] Custom domain configured (optional)
