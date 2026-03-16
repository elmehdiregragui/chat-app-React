# chat-app-React

Application de chat temps reel avec:
- `client`: React + Socket.IO client
- `server`: Express + Socket.IO server


## Structure
- `client/public/index.html` est deja present (fichier de depart front-end)
- `client/src` contient l'application React
- `server/server.js` contient le serveur HTTP + WebSocket

## Installation locale
Dans deux terminaux separes:

```bash
# Terminal 1
cd server
npm install
```

```bash
# Terminal 2
cd client
npm install
```

## Lancer en local
Terminal 1 (serveur):

```bash
cd server
npm start
```

Terminal 2 (client React):

```bash
cd client
npm start
```

Le client est sur `http://localhost:3000`.
Le serveur ecoute sur `http://localhost:5000`.

## Variables d'environnement

### Frontend (Vercel / React)
- `REACT_APP_SERVER_URL`: URL publique du backend (Render)

Le client utilise:
1. `REACT_APP_SERVER_URL`
2. sinon `http://localhost:5000`

### Backend (Render)
- `PORT`: fourni automatiquement par Render
- `CLIENT_URL`: URL du frontend Vercel

## Deploiement Backend sur Render

### Option A

1. Aller sur Render -> `New +` -> `Web Service`.
2. Connecter le repo `MouradIntellij/chat-app-React`.

3. Configurer:

- `Root Directory`: `server`
- `Build Command`: `npm install`
- `Start Command`: `npm start`
- `Plan`: Free

4. Dans `Environment Variables`, ajouter:

```
CLIENT_URL=https://<ton-frontend-vercel>.vercel.app
```

5. Deploy et copie l'URL publique Render.

## Deploiement Frontend sur Vercel

1. Va sur Vercel -> `Add New...` -> `Project`.
2. Importe `MouradIntellij/chat-app-React`.

3. Configure:

- `Root Directory`: `client`
- Build command: `npm run build`
- Output directory: `build`

4. Ajoute la variable Vercel:

```
REACT_APP_SERVER_URL=https://<ton-backend-render>.onrender.com
```

5. Deploy.


## Fichiers utiles

- `client/.env.example` 
- `render.yaml` 

## Q1 - Role des fichiers

### App.js
Composant principal de l'application React.
Il gere les etats globaux comme le pseudo, la room et l'etat de connexion.
Il affiche `Join.js` quand l'utilisateur n'est pas connecte puis `Chat.js` une fois la connexion etablie.

### Chat.js
Composant principal du chat.
Il gere l'affichage des messages, l'envoi des messages, la reception en temps reel et la liste des utilisateurs connectes.

### Message.js
Composant qui affiche un message individuel.
Il distingue les messages de l'utilisateur courant, les messages des autres et les messages systeme.

### Sidebar.js
Composant qui affiche le panneau lateral.
Il montre les participants connectes dans la room.

### Join.js
Composant d'entree dans l'application.
Il permet de saisir le pseudo, voir les rooms disponibles, creer une room et rejoindre une salle.

### server.js
Serveur Node.js avec Express et Socket.IO.
Il gere les connexions, les rooms, les messages, la liste des utilisateurs et les evenements temps reel.

### SocketContext.js
Fichier qui cree et partage la connexion socket dans toute l'application React avec Context API.

## Q2 - Communication frontend / backend

### Creation et partage du socket
Le socket est cree dans `SocketContext.js` avec `socket.io-client`.
Il est ensuite partage dans toute l'application grace a `SocketContext.Provider`.
Les composants React utilisent le hook `useSocket()` pour acceder a la meme connexion.

### Evenement emis quand un utilisateur rejoint une room
Quand un utilisateur choisit une room dans `Join.js`, le client emet l'evenement `join_room` avec son pseudo et le nom de la room.

Le serveur recoit cet evenement, ajoute le socket dans la room avec:

```
socket.join(room)
```

Puis il met a jour la liste des utilisateurs et la liste des rooms.

### Diffusion des messages
Quand un utilisateur envoie un message, `Chat.js` emet l'evenement:

```
send_message
```

Le serveur recoit cet evenement et utilise:

```
io.to(room).emit("receive_message", data)
```

pour envoyer le message a tous les membres de la room.

### Difference entre emit et broadcast
- `emit` envoie a tous les clients cibles
- `broadcast` envoie a tous sauf l'expediteur

## Modifications apportees

### Q3 - Indicateur Lu
Ajout d'un indicateur visuel `✓✓ Lu` uniquement sous les messages de l'utilisateur courant dans `Message.js`, avec le style CSS dans `App.css`.

### Q4 - Bouton Quitter la salle
Ajout d'un bouton `Quitter la salle` dans `Chat.js`.
Ce bouton emet un evenement Socket.IO vers le serveur et remet l'application sur l'ecran `Join.js`.

### Q5 - Historique des connexions
Ajout d'un evenement `activity_log` cote serveur a chaque connexion et deconnexion.
Dans `Sidebar.js`, une section `Activite recente` affiche les 5 derniers evenements.


## Liens de deploiement

Frontend Vercel:

```
https://................vercel.app
```

Backend Render:

```
https://................onrender.com
```

## Question finale - Amelioration libre

Une amelioration possible serait d'ajouter une base de donnees pour sauvegarder l'historique des messages.
Cela permettrait de conserver les conversations meme apres la fermeture de l'application.

## Remise du projet

Le projet doit etre remis sous la forme:

```bash
chat-app-examen-elmehdiregragui.zip
```

Contenant:

- `client/`
- `server/`
- `README.md`

## GitHub
Depot distant:

`https://github.com/MouradIntellij/chat-app-React.git`
