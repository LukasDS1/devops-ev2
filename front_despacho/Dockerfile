FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:18-alpine AS production
WORKDIR /app
RUN npm install -g json-server
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=build /app/dist ./dist
COPY db.json .
RUN npm install -g serve
RUN chown -R appuser:appgroup /app
USER appuser
EXPOSE 3000 3001

CMD json-server --watch db.json --port 3001 --host 0.0.0.0 & \serve -s dist -l 3000