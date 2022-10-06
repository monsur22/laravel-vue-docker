FROM node:14.15-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install -g @vue/cli
RUN npm install
COPY . .

CMD ["npm", "run", "serve"]