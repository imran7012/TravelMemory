# --- Frontend: React (dev server for simplicity) ---
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci || npm install

# Copy source
COPY . .

# CRA / Vite dev servers typically run on 3000
ENV PORT=3000
ENV REACT_APP_BACKEND_URL=""
EXPOSE 3000

# Uses start script from package.json
CMD ["npm", "start"]