# Back Market – Simulateur de Formation au Live Chat

Un outil de formation interactif, accessible depuis le navigateur, destiné aux agents du service client Back Market. Deux utilisateurs se connectent en temps réel : l'un joue le rôle de l'**Agent**, l'autre celui du **Client**, afin de s'entraîner à la gestion du chat en direct à travers des scénarios de support réalistes.

> **Démo en ligne :** [https://aitormaa.github.io/FR-Back-Market-Live-Chat-Simulator/](https://aitormaa.github.io/FR-Back-Market-Live-Chat-Simulator/)

---

## Fonctionnalités

### Multijoueur à deux utilisateurs

- Deux participants se connectent via un **Code de Session** partagé
- Un utilisateur choisit le niveau de difficulté et devient l'**Agent**
- L'autre rejoint via le code et devient le **Client**
- Synchronisation en temps réel via **PeerJS (WebRTC)** avec fallback localStorage + BroadcastChannel
- Fonctionne dans deux onglets du même navigateur ou sur deux appareils différents

### 52 Scénarios de Formation – 3 Niveaux de Difficulté

| Niveau | Plage de codes | Scénarios | Points clés |
|---|---|---|---|
| Débutant | B01–B11 | 11 | Suivi de commande, factures, annulations, garanties, politique de retour |
| Intermédiaire | I01–I30 | 30 | Défauts de l'appareil, demandes de garantie, litiges de remboursement, dépannage technique |
| Expert | A01–A11 | 11 | Accusations de fraude, violations RGPD, clients hostiles, menaces juridiques, utilisateurs vulnérables |

Chaque scénario comprend :

- **Persona client** (nom, appareil, détails de commande, humeur)
- **Message d'ouverture** du client
- **Guide de résolution** (visible uniquement par l'Agent)
- **Conseils** sur le comportement du client
- **Défis clés** à travailler

### Minuteries de Performance

- **FRT** – Temps de Première Réponse (objectif : moins de 60 s), décompte automatique depuis le début de la session
- **NRT** – Temps de Réponse Suivant (objectif : moins de 90 s), réinitialisé à chaque message du client
- **Minuterie de Session** – temps total écoulé

### Comportement Client Automatisé

- Aucune réponse de l'agent après **60 secondes** : message de file d'attente automatique envoyé
- Toujours aucune réponse après **120 secondes** : chat automatiquement transféré en mode asynchrone

### Outils de l'Agent

- Bascule **RÉPONSE / NOTE** (les notes internes ne sont pas visibles par le client)
- **Bibliothèque de macros** (8 réponses prédéfinies : salutation, mise en attente, escalade, clôture, note récapitulative, etc.)
- **Téléchargement de fichiers et d'images** avec compression côté client (max. 600px, JPEG 65 %)
- Champs du ticket : **Statut**, **Priorité**, **Catégorie**

### Score QA et Analyse de l'Empathie

- **Score d'Empathie** automatique calculé à partir des messages de l'agent (détection de mots-clés, utilisation du prénom du client, fréquence)
- **Grille d'évaluation QA** manuelle avec score pondéré
- **Rapport de débriefing** complet à la clôture du chat

---

## Démarrage

### Option 1 – Utiliser la version en ligne

Aucune installation requise. Ouvrir [https://aitormaa.github.io/FR-Back-Market-Live-Chat-Simulator/](https://aitormaa.github.io/FR-Back-Market-Live-Chat-Simulator/) dans deux fenêtres de navigateur ou sur deux appareils.

### Option 2 – Exécuter en local

Il s'agit d'un seul fichier HTML sans étape de build :

```bash
git clone https://github.com/aitormaa/backmarket-chat-simulator.git
cd backmarket-chat-simulator
open index.html
```

> **Remarque :** Certains navigateurs limitent localStorage et BroadcastChannel sur les URL `file://`. Pour de meilleurs résultats, utiliser un serveur local :

```bash
npx serve .
# ou
python3 -m http.server 8080
```

---

## Comment Lancer une Session

### Agent (Formateur ou Stagiaire)

1. Ouvrir le simulateur
2. Choisir un niveau de difficulté : **Débutant**, **Intermédiaire** ou **Expert**
3. Saisir son prénom
4. Un **Code de Session à 6 caractères** est généré – le partager avec son partenaire
5. Attendre que le client rejoigne, puis le chat commence

### Client (Joueur de Rôle)

1. Ouvrir le simulateur dans un autre onglet ou sur un autre appareil
2. Cliquer sur **« Rejoindre en tant que Client »**
3. Saisir le Code de Session partagé par l'Agent
4. Le scénario se charge automatiquement – le chat peut commencer

---

## Structure des Fichiers

```
backmarket-chat-simulator/
└── index.html        # Application complète – autonome, aucune dépendance à installer
```

L'application utilise des bibliothèques chargées via CDN (npm non requis) :

- [React 18](https://react.dev/) – framework UI
- [Tailwind CSS](https://tailwindcss.com/) – mise en forme
- [Babel Standalone](https://babeljs.io/) – transpilation JSX dans le navigateur
- [PeerJS 1.5](https://peerjs.com/) – connexion WebRTC pair-à-pair

---

## Architecture

```
AGENT (Onglet / Appareil A)              CLIENT (Onglet / Appareil B)

Crée le code de session                  Rejoint via le code de session
Reçoit le scénario + les conseils        Reçoit le scénario (sans conseils)
Voit le score QA                         Voit uniquement le chat côté client

          |                                       |
          +------------ PeerJS WebRTC ------------+
               BroadcastChannel + localStorage (fallback)
```

**Flux de données de session :**

1. L'Agent crée la session – le scénario est stocké dans `localStorage` sous le code de session
2. PeerJS envoie le payload `INIT` au Client lors de la connexion
3. Les messages sont diffusés via WebRTC DataChannel + écriture dans `localStorage`
4. `BroadcastChannel` maintient la synchronisation entre les onglets du même appareil (polling 400 ms en fallback)

---

## Configuration

Tous les scénarios, macros et messages automatiques sont définis sous forme de tableaux JavaScript simples en haut de `index.html` – aucun backend ni base de données requis.

| Constante | Description |
|---|---|
| `SC` | Tableau des 52 scénarios de formation |
| `MACROS` | Modèles de macros pour l'Agent (supporte `{CUSTOMER_FNAME}`, `{CURRENT_USER_FNAME}`) |
| `AUTO` | Messages automatiques envoyés lors de l'inactivité de l'Agent à 60 s et 120 s |
| `PCFG` | Configuration PeerJS (serveurs STUN) |

---

## Détails du Score

### Score d'Empathie (calculé automatiquement, max. 25 pts)

Analyse tous les messages de l'Agent pour :

- Les mots-clés d'empathie (`comprends`, `désolé`, `excuse`, `avec plaisir`, etc.)
- L'utilisation du **prénom du client**
- La fréquence et la cohérence tout au long de la conversation

### Score QA (grille manuelle)

Les évaluateurs vérifient des éléments dans les catégories suivantes :

- Qualité de la salutation
- Identification du problème
- Pertinence de la solution
- Respect des politiques
- Clarté de la communication
- Vitesse de réponse (objectifs FRT et NRT)

---

## Feuille de Route

- [ ] Backend Firebase / Supabase pour l'historique persistant des sessions
- [ ] Tableau de bord administrateur avec analyses à l'échelle de l'équipe
- [ ] Version en espagnol (`index-es.html`)
- [ ] Injection de scénario par le formateur en cours de session
- [ ] Rapport de débriefing exportable en PDF

---

## Contributeurs

Développé par l'équipe Learning & Development / Service Client de Back Market.

---

## Licence

Usage interne uniquement – outil propriétaire Back Market. Non destiné à la distribution publique.
