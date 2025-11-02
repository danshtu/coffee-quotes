# syntax=docker/dockerfile:1

# Base stage with Node.js
FROM node:20-alpine AS base
WORKDIR /app

# Development stage
FROM base AS development

# Copy package files
COPY package.json package-lock.json ./

# Install all dependencies (including devDependencies for Vite)
RUN npm ci

# Copy application code
COPY . .

# Expose Vite dev server port
EXPOSE 5173

# Run development server
CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]

# Production dependencies stage
FROM base AS production-dependencies

# Copy package files
COPY package.json package-lock.json ./

# Install production dependencies only
RUN npm ci --omit=dev

# Build stage
FROM base AS build

# Copy package files
COPY package.json package-lock.json ./

# Install all dependencies for building
RUN npm ci

# Copy application code
COPY . .

# Build the application
RUN npm run build

# Production stage
FROM base AS production

# Copy package files and production dependencies
COPY package.json package-lock.json ./
COPY --from=production-dependencies /app/node_modules ./node_modules

# Copy built application
COPY --from=build /app/build ./build

# Expose production server port
EXPOSE 3000

# Run production server
CMD ["npm", "run", "start"]
