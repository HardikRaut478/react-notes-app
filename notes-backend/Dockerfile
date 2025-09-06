# backend/Dockerfile
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
# install production deps
RUN npm ci --only=production

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
# copy node_modules from deps stage
COPY --from=deps /app/node_modules ./node_modules
COPY . .
# ensure server binds 0.0.0.0 in your code (see notes)
EXPOSE 3000
# drop root privileges (optional)
USER node
CMD ["node", "index.js"]

