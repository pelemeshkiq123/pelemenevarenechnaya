# Стадия сборки
FROM node:16-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# Сборка с ПРАВИЛЬНЫМ API_URL для production
RUN VUE_APP_API_URL=/ npm run build

# Стадия production
FROM nginx:stable-alpine
COPY --from=build-stage /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80