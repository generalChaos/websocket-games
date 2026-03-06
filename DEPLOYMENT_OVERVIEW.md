# Deployment Overview

Complete deployment guide for the Party Game Platform.

## Architecture

```
┌─────────────────┐
│  Vercel (Web)   │  ← Next.js Frontend
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

## Deployment Order

### 1. Deploy API to Railway (First)

**Why first?** You need the API URL to configure the frontend.

**Steps:**
1. Deploy PostgreSQL database on Railway
2. Deploy NestJS API service on Railway
3. Get your Railway API URL
4. Run database migrations

**Guide:** See [`api/RAILWAY_DEPLOYMENT.md`](./api/RAILWAY_DEPLOYMENT.md)

**Quick Start:** See [`api/RAILWAY_QUICK_START.md`](./api/RAILWAY_QUICK_START.md)

### 2. Deploy Frontend to Vercel (Second)

**Why second?** You need the Railway API URL to set `NEXT_PUBLIC_API_URL`.

**Steps:**
1. Connect GitHub repo to Vercel
2. Set root directory to `web`
3. Set `NEXT_PUBLIC_API_URL` environment variable (Railway API URL)
4. Deploy

**Guide:** See [`web/VERCEL_DEPLOYMENT.md`](./web/VERCEL_DEPLOYMENT.md)

### 3. Update CORS (After Frontend Deploy)

**Why?** Railway API needs to allow requests from your Vercel frontend.

**Steps:**
1. Get your Vercel URL (e.g., `https://your-app.vercel.app`)
2. Go to Railway → API Service → Variables
3. Update `CORS_ORIGINS` to include your Vercel URL
4. Redeploy API service

## Environment Variables Summary

### Railway API Service

```
NODE_ENV=production
CORS_ORIGINS=https://your-app.vercel.app
DATABASE_URL=<auto-set by Railway>
PORT=<auto-set by Railway>
```

### Vercel Frontend

```
NEXT_PUBLIC_API_URL=https://your-api.railway.app
NEXT_PUBLIC_SUPABASE_URL=<optional>
NEXT_PUBLIC_SUPABASE_ANON_KEY=<optional>
```

## Cost Estimate

- **Vercel**: Free tier (100GB bandwidth/month)
- **Railway PostgreSQL**: ~$5/month
- **Railway API Service**: ~$5-10/month
- **Total**: ~$10-15/month

## Quick Checklist

### Railway (API)
- [ ] PostgreSQL service created
- [ ] API service created (root: `api/`)
- [ ] Environment variables set
- [ ] Migrations run: `railway run npx prisma migrate deploy`
- [ ] API URL obtained
- [ ] Health check working: `https://your-api.railway.app/health`

### Vercel (Frontend)
- [ ] Repository connected
- [ ] Root directory set to `web`
- [ ] `NEXT_PUBLIC_API_URL` environment variable set
- [ ] Deployed successfully
- [ ] Vercel URL obtained

### Post-Deployment
- [ ] Railway `CORS_ORIGINS` updated with Vercel URL
- [ ] API connection tested from Vercel app
- [ ] WebSocket connection tested
- [ ] Custom domains configured (optional)

## Troubleshooting

### API Connection Issues

1. **Check `NEXT_PUBLIC_API_URL`**:
   - Must be `https://` (not `http://`)
   - No trailing slash
   - Example: `https://api.railway.app`

2. **Check Railway CORS**:
   - Vercel URL must be in `CORS_ORIGINS`
   - Format: `https://your-app.vercel.app` (no trailing slash)

3. **Test API directly**:
   - Visit: `https://your-api.railway.app/health`
   - Should return JSON with status

### WebSocket Issues

1. **Check WebSocket URL**:
   - Should automatically use `wss://` (secure)
   - Check browser console for connection errors

2. **Check Railway**:
   - Railway supports WebSocket automatically
   - No additional configuration needed

## Support

- Railway: [docs.railway.app](https://docs.railway.app)
- Vercel: [vercel.com/docs](https://vercel.com/docs)
- Project Issues: Check individual deployment guides

## Next Steps

1. ✅ Read [`api/RAILWAY_DEPLOYMENT.md`](./api/RAILWAY_DEPLOYMENT.md)
2. ✅ Deploy API to Railway
3. ✅ Read [`web/VERCEL_DEPLOYMENT.md`](./web/VERCEL_DEPLOYMENT.md)
4. ✅ Deploy frontend to Vercel
5. ✅ Update CORS in Railway
6. ✅ Test everything!
