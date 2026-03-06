# Multi-stage build for NestJS API
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./
COPY tsconfig*.json ./
COPY nest-cli.json ./

# Install dependencies
RUN npm ci

# Copy Prisma schema
COPY prisma ./prisma

# Generate Prisma Client
RUN npx prisma generate

# Copy source code
COPY . .

# Build the application
RUN npm run build

# Production stage
FROM node:20-alpine AS production

WORKDIR /app

# Copy package files
COPY package*.json ./
COPY tsconfig*.json ./
COPY nest-cli.json ./

# Copy Prisma files BEFORE installing (needed for postinstall script)
COPY prisma ./prisma

# Install production dependencies + prisma and tsconfig-paths (needed for runtime)
# Skip postinstall script during install, then generate Prisma manually
RUN npm ci --only=production --ignore-scripts && \
    npm install --save prisma tsconfig-paths --ignore-scripts && \
    npx prisma generate

# Copy built application from builder
COPY --from=builder /app/dist ./dist

# Expose port (Railway will set PORT env var)
EXPOSE $PORT

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s \
  CMD node -e "require('http').get('http://localhost:' + (process.env.PORT || 3004) + '/health', (r) => {process.exit(r.statusCode === 200 ? 0 : 1)})" || exit 1

# Run migrations on startup (optional - can also run manually)
# Uncomment if you want auto-migrations on deploy
# RUN npx prisma migrate deploy || true

# Start the application
CMD ["npm", "run", "start:prod"]
