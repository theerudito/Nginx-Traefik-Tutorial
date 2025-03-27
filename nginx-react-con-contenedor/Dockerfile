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
