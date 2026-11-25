# PostgreSQL local pour les étudiants

Chaque étudiant exécute son propre conteneur PostgreSQL sur son ordinateur et
utilise DBeaver Community pour s'y connecter. Il n'y a pas de serveur de classe
partagé ou centralisé : la base de données et les identifiants restent sur
l'ordinateur de l'étudiant.

## Prérequis selon le système

- **Windows 10/11 :** installer [Docker Desktop pour Windows](https://docs.docker.com/desktop/setup/install/windows-install/). Docker Desktop utilise généralement le backend WSL 2 ; suivre les instructions d'installation si WSL 2 doit être activé.
- **macOS :** installer [Docker Desktop pour Mac](https://docs.docker.com/desktop/setup/install/mac-install/). Choisir la version correspondant au processeur Apple Silicon ou Intel.
- **Linux :** installer [Docker Engine](https://docs.docker.com/engine/install/) et le [plugin Docker Compose](https://docs.docker.com/compose/install/linux/), selon la distribution. L'utilisateur doit pouvoir exécuter les commandes Docker conformément à la configuration de la distribution.

Sur Windows et macOS, démarrer Docker Desktop et attendre qu'il indique que Docker est en cours d'exécution. Sur Linux, vérifier que le service Docker est démarré.

Installer également [DBeaver Community](https://dbeaver.io/download/) sur le même ordinateur.

## Démarrer PostgreSQL

Chaque étudiant doit obtenir une copie de ce projet et ouvrir un terminal dans
le dossier qui contient `compose.yaml`.

Créer `.env` à partir de `.env.example` :

**Windows PowerShell**

```powershell
Copy-Item .env.example .env
```

**Windows Command Prompt, macOS ou Linux**

```sh
cp .env.example .env
```

Modifier `.env` et remplacer la valeur de `POSTGRES_PASSWORD` par un mot de
passe solide, réservé à cette base de données. Ne pas partager le fichier
`.env` ; il est exclu de Git.

Dans le même terminal, démarrer PostgreSQL :

```sh
docker compose up -d
```

La commande `docker compose` est identique dans PowerShell, Windows Command
Prompt et les terminaux macOS et Linux. Vérifier que le conteneur est démarré :

```sh
docker compose ps
docker compose logs postgres
```

## Se connecter avec DBeaver

Dans DBeaver, créer une connexion PostgreSQL avec les valeurs de `.env` :

- **Host :** `localhost`
- **Port :** valeur de `POSTGRES_PORT` (par défaut `5432`)
- **Database :** valeur de `POSTGRES_DB` (par défaut `student_db`)
- **Username :** valeur de `POSTGRES_USER` (par défaut `student`)
- **Password :** valeur de `POSTGRES_PASSWORD`

Utiliser **Test Connection** avant d'enregistrer la connexion. Le conteneur
PostgreSQL doit être démarré.

## Arrêter et redémarrer

Les données PostgreSQL sont conservées dans un Docker volume lorsque le
conteneur est arrêté ou recréé. Pour arrêter le conteneur sans supprimer les
données :

```sh
docker compose down
```

Pour le redémarrer :

```sh
docker compose up -d
```

Ne pas exécuter `docker compose down -v` sauf si la suppression de la base de
données est voulue.

## Dépannage

Le port est lié à `127.0.0.1` : PostgreSQL est accessible uniquement depuis
l'ordinateur de l'étudiant, pas depuis le LAN ou Internet.

Si Docker indique que le port `5432` est déjà utilisé, modifier `POSTGRES_PORT`
dans `.env` (par exemple `POSTGRES_PORT=5433`), puis relancer
`docker compose up -d`. Utiliser ensuite ce nouveau port dans DBeaver. Si le
conteneur est déjà démarré, appliquer la modification avec :

```sh
docker compose down
docker compose up -d
```
