```mermaid
sequenceDiagram
    participant U as User (Angular)
    participant A as Angular Frontend
    participant S as Symfony Backend
    participant G as Google OAuth2
    participant Go as Service Go (PDF, ZIP)
    participant M as Mercure / WebSocket

    %% Auth classique
    U->>A: Saisie email + mot de passe
    A->>S: POST /api/login {email, password}
    S->>S: Vérification user + password
    S-->>A: JWT interne (LexikJWT)
    A->>U: Auth OK, stocke JWT

    %% Auth Google
    U->>A: Clique bouton "Connexion Google"
    A->>G: OAuth2 Login
    G-->>A: Google ID Token (JWT signé Google)
    A->>S: POST /api/login/google {token Google}
    S->>G: Vérifie signature Google ID Token
    S->>S: Crée user si nouveau + génère JWT interne
    S-->>A: JWT interne
    A->>U: Auth OK, stocke JWT

    %% Appels API utilisateur
    U->>A: Actions (ex: création devis)
    A->>S: API REST/GraphQL avec JWT interne

    %% Génération PDF via Go
    U->>A: Demande génération PDF devis/facture
    A->>S: POST /api/machine/generate-pdf (JWT interne)
    S->>Go: Requête API JWT machine-to-machine (signée)
    Go->>Go: Génération PDF (parallèle, cache)
    Go-->>S: Retour status + URL PDF
    S->>M: Publie notification temps réel (Mercure/WebSocket)
    M->>A: Notification génération PDF terminée
    A->>U: Affiche notification "PDF prêt"

    %% Export ZIP
    U->>A: Demande export ZIP
    A->>S: POST /api/machine/export-zip (JWT machine)
    S->>Go: Transfert requête signée
    Go->>Go: Regroupe PDF dans ZIP
    Go-->>S: Retour ZIP
    S->>A: Envoie ZIP à Angular
    A->>U: Téléchargement ZIP

    %% Suivi en temps réel des devis générés
    Go->>S: Push évènements création devis (WebSocket/Mercure)
    S->>M: Relaye évènements temps réel
    M->>A: Angular affiche compteur live devis

    S->>Go: JWT signé RSA avec expiration courte
    Go->>S: JWT vérifié
