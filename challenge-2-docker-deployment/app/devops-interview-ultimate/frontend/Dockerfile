FROM node:18-alpine

# Use /app as the working directory inside the container.
WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY package.json package-lock.json ./

# Install dependencies exactly as locked in package-lock.json.
RUN npm ci

# Copy the rest of the frontend source code.
COPY . ./

# React dev server listens on 3000.
EXPOSE 3000

# Start React in development mode.
CMD ["npm", "start"]
