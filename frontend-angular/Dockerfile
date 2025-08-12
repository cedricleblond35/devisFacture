FROM node:20-alpine

WORKDIR /app

# Installation Angular CLI globalement
RUN npm install -g @angular/cli

# Copie package.json et package-lock.json
COPY package*.json ./

# Installe les dépendances du projet
RUN npm install

EXPOSE 4200

CMD ["ng", "serve", "--host", "0.0.0.0"]