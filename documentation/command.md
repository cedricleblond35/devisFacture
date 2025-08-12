# 1. Structure générale
On va séparer le code en trois dossiers :
/devisFacture
  /backend-symfony  → API Symfony 7 (PHP 8.2+, MySQL, JWT)
  /frontend-angular → SPA Angular + TypeScript
  /service-go       → Microservice Go (PDF, ZIP, temps réel)



# Doctrine
```php bin/console doctrine:database:create```

# Entity:
```
php bin/console make:user
php bin/console make:entity Client
php bin/console make:entity Devis
php bin/console make:entity Facture
php bin/console make:migration
php bin/console doctrine:migrations:migrate
```


# JWT
Générer les clés pour JWT utilisateur

```
mkdir -p config/jwt
openssl genrsa -out config/jwt/private.pem -aes256 4096
openssl rsa -pubout -in config/jwt/private.pem -out config/jwt/public.pem
```




# Angular 20
## Créer projet Angular
### Lancer conteneur Node.js temporaire
````
docker run --rm -it -v "$PWD/frontend-angular":/app -w /app node:20-alpine npm install
````
### Créer projet
````
sudo docker run --rm -it -v "$PWD/frontend-angular":/app -w /app node:20-alpine sh -c "npm install -g @angular/cli && ng new devis-facture --skip-git --skip-install --directory ."
````
### Installer dépendances
```
sudo docker run --rm -it -v "$PWD/frontend-angular":/app -w /app node:20-alpine npm install
```
### Lancer stack Docker
docker compose up -d frontend-angular
### Ouvrir navigateur
http://localhost:4200

## Structure
core/ pour tout ce qui touche aux appels API vers Symfony, Auth, Guards.
shared/ pour tes composants UI réutilisables (boutons, tables, etc.).
models/ pour typer toutes tes réponses API.
features/ pour séparer les modules métiers (par ex. facturation, clients, devis).