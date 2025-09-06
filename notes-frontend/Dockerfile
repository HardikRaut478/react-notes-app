# frontend/Dockerfile
FROM node:18-alpine AS build
WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
COPY package*.json ./
RUN npm ci
COPY . .
# If your app needs an API base, use an env arg or keep API calls relative (recommended)
ARG REACT_APP_API_BASE=/api
ENV REACT_APP_API_BASE $REACT_APP_API_BASE
RUN npm run build

FROM nginx:stable-alpine AS production
# copy build output to nginx html folder
COPY --from=build /app/build /usr/share/nginx/html
# use a small SPA-friendly nginx config (optional, provided below)
COPY nginx/frontend-spa.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

