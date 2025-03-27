Estructura del proyecto

```bash
my-app/
├── app/
│ ├── src/
│ ├── public/
│ ├── vite.config.js
│ └── package.json
├── nginx/
│ └── default.conf
├── Dockerfile
├── docker-compose.yml
└── .dockerignore
```

1.- Crear el archivo de docker file

```bash
# Etapa de construcción
FROM node:18-alpine as build

WORKDIR /app

# Copiar los archivos de la aplicación
COPY ./app/package.json ./app/package-lock.json ./
RUN npm install
COPY ./app ./
RUN npm run build

# Etapa de producción con Nginx
FROM nginx:alpine

# Copiar la configuración personalizada de Nginx
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf

# Copiar los archivos construidos de la app al contenedor de Nginx
COPY --from=build /app/dist /usr/share/nginx/html

# Exponer el puerto 80
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

2.- Crear el archivo de docker compose

```bash
version: '3.8'

services:
  app:
    build: .
    container_name: react-app
    ports:
      - "80:80"
    networks:
      - app-network
    volumes:
      - ./app:/app
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf

networks:
  app-network:
    driver: bridge
```

3.- Crear el archivo de nginx

```bash
server {
  listen 80;

  server_name localhost;

  root /usr/share/nginx/html;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

4.- Crear el archivo .dockerignore

```bash
node_modules
dist
*.log
```

5.- Configurar el archivo vite.config.js

```bash
node_modules
dist
*.log
```

6.- Construir y Ejecutar el Contenedor con Docker Composer

```bash
docker-compose up --build
```

7.- Verificar la aplicación en el navegador

```bash
http://localhost
```

8.- Detener el contenedor

```bash
docker-compose down
```
