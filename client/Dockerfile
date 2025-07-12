FROM node:18-alpine
WORKDIR /app
COPY . /app
RUN npm install
EXPOSE 3000
ENV NODE_OPTIONS=--openssl-legacy-provider
CMD ["npm","start"]