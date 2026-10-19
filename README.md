Back Market - Simulateur de formation Live Chat
Outil de formation dans le navigateur pour les agents du service client Back Market. Deux personnes se connectent à la même session de chat. Une personne joue le rôle de l'Agent et l'autre celui du Client.
Démo en ligne
https://aitormaa.github.io/FR-Back-Market-Live-Chat-Simulator/
Fonctionnalités
Sessions en direct à deux utilisateurs

    L'Agent crée un code de session de six caractères.
    Le Client rejoint la session avec ce code.
    Firebase Realtime Database synchronise les sessions et les messages entre les appareils.
    Firebase Anonymous Authentication protège l'accès à la base de données sans demander de compte utilisateur.
    PeerJS WebRTC peut établir une connexion directe lorsque le réseau l'autorise.
    Si WebRTC est bloqué par le réseau de l'entreprise, Firebase continue de synchroniser la session.

52 scénarios de formation
Niveau	Plage de codes	Scénarios	Thèmes
Débutant	B01-B11	11	Suivi de commande, factures, annulations, garanties et retours
Intermédiaire	I01-I30	30	Défauts des appareils, garanties, remboursements et dépannage technique
Avancé	A01-A11	11	Accusations de fraude, sujets RGPD, clients difficiles, menaces juridiques et clients vulnérables
Chaque scénario contient un profil client, les informations de l'appareil et de la commande, un message initial, un guide de résolution, des indications sur le comportement du client et des objectifs d'entraînement.
Minuteurs de performance

    FRT: délai jusqu'à la première réponse, avec un objectif de 60 secondes.
    NRT: délai jusqu'à la réponse suivante, avec un objectif de 90 secondes.
    Minuteur de session: durée totale de la session.

Outils de l'Agent

    Modes réponse et note interne.
    Bibliothèque de macros pour les réponses fréquentes.
    Ajout de fichiers et d'images avec compression dans le navigateur.
    Champs de ticket pour le statut, la priorité et la catégorie.
    Évaluation QA et analyse de l'empathie.

Démarrage
Utiliser la version en ligne
Ouvre la démo dans deux fenêtres de navigateur ou sur deux appareils:
https://aitormaa.github.io/FR-Back-Market-Live-Chat-Simulator/
La version française utilise le chemin Firebase sessions/fr.
Exécuter le projet en local
Le projet ne nécessite pas d'étape de compilation:

git clone https://github.com/aitormaa/FR-Back-Market-Live-Chat-Simulator.git
cd FR-Back-Market-Live-Chat-Simulator
python3 -m http.server 8080

Ouvre ensuite:
http://localhost:8080
Un serveur local est plus fiable que l'ouverture directe du fichier avec file://.
Lancer une session
Agent

    Ouvre le simulateur.
    Sélectionne le rôle Agent.
    Sélectionne le niveau de difficulté.
    Saisis ton nom.
    Partage le code de session à six caractères.
    Attends l'arrivée du Client.

Client

    Ouvre le simulateur sur un autre appareil ou navigateur.
    Sélectionne le rôle Client.
    Saisis le code transmis par l'Agent.
    Commence la conversation.

Structure du dépôt

FR-Back-Market-Live-Chat-Simulator/
├── index.html
└── README.md

La configuration Firebase est incluse dans index.html. Cette version indépendante ne nécessite pas de fichier firebase-config.js séparé.
Configuration Firebase
Le simulateur utilise Firebase Anonymous Authentication et Firebase Realtime Database. La configuration Web Firebase est incluse dans le HTML, comme prévu pour une application Web publique.
La protection des données repose sur l'authentification et les règles de Realtime Database.
Paramètres requis

    Anonymous Authentication activée.
    Realtime Database activée.
    aitormaa.github.io ajouté aux domaines autorisés de Firebase Authentication.
    Règles Realtime Database publiées.

Utilise ces règles:

Rules
Sessions
$locale
$code
.read
auth != null
.write
auth != null
.validate
newData.hasChildren(['scenario', 'closed'])

Ne rends pas messages obligatoire dans la validation. Une nouvelle session commence avec une liste de messages vide.
Les sessions françaises sont stockées ici:

sessions/fr/SESSION_CODE

Architecture
Firebase est la source principale pour les sessions et les messages. Le navigateur conserve un cache local. PeerJS WebRTC peut établir une connexion directe lorsque le réseau l'autorise.
Si la console affiche WebRTC: ICE failed, Firebase doit continuer à synchroniser la session et les messages.
Évaluation
Le simulateur calcule un Empathy Score et propose une checklist QA avec des critères tels que:

    Qualité de l'accueil.
    Identification du problème.
    Exactitude de la solution.
    Respect des politiques.
    Clarté de la communication.
    Rapidité de réponse.

Dépannage
Si la console affiche:

Firebase is not configured. Local-only mode is active.

Vérifie que tu utilises la version Firebase de index.html.
Si elle affiche:

PERMISSION_DENIED

Vérifie qu'Anonymous Authentication est activée et que les règles Realtime Database sont publiées.
Roadmap

    Réunir les quatre dépôts de langue dans un seul dépôt.
    Créer un tableau de bord administrateur avec les indicateurs de l'équipe.
    Exporter les comptes rendus de session en PDF.
    Stocker les fichiers volumineux dans Firebase Storage.

Licence
Usage interne uniquement. Outil propriétaire de Back Market. Aucune diffusion publique.
