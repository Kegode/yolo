# Explanation

## 1️⃣ Base Images
- **Frontend:** Used `node:18-alpine` because it is lightweight, secure, and based on the latest LTS.
- **Backend:** Used `node:18-alpine` because it is lightweight, secure, and based on the latest LTS.
- **MongoDB:** Used official `mongo:6.0` image to ensure stability and community support.

## 2️⃣ Dockerfile Directives

- `WORKDIR` sets the working directory to `/app`.
- `COPY` copies dependencies to ./app first for better caching.
- `RUN npm install` installs packages.
- `EXPOSE 5000` exposes backend port.
- `CMD ["npm", "start"]` runs the app.

## 3️⃣ Docker Compose Networking

- Used a custom bridge network (`ecommerce-network`) for service-to-service communication.
- Assigned ports for each service to avoid conflict and allow local testing.

## 4️⃣ Volumes

- Defined `my-products` volume for data persistence across container restarts.

## 5 Debugging

- Used `docker logs` to view logs.
- Ran `docker-compose down -v` and `docker-compose up --build` to reset environment.
- Checked `docker ps` and `docker exec` to verify service health.


## 8️⃣ DockerHub Image

![Screenshot](frontend.png)
![Screenshot](backend.png)

