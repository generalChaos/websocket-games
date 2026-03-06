# Multi-stage build for NestJS API
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./
COPY tsconfig*.json ./
COPY nest-cli.json ./

# Copy Prisma schema BEFORE installing (needed for postinstall script)
COPY prisma ./prisma

# Install dependencies (postinstall will run prisma generate)
RUN npm ci

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

# Install production dependencies + prisma and tsconfig-paths (needed for runtime)
# Skip postinstall script to avoid Prisma generation issues
RUN npm ci --only=production --ignore-scripts && \
    npm install --save prisma tsconfig-paths --ignore-scripts

# Copy Prisma files (for migrations if needed)
COPY prisma ./prisma

# Copy generated Prisma Client from builder stage (already generated there)
# This avoids regenerating and the postinstall script issues
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma
COPY --from=builder /app/node_modules/@prisma ./node_modules/@prisma

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
