# TERMINAL-7 — Guide de configuration Firebase
## Mise en place complète pas à pas

---

## ÉTAPE 1 — Créer un projet Firebase

1. Allez sur **https://console.firebase.google.com**
2. Cliquez sur **"Ajouter un projet"**
3. Nom du projet : `terminal7` (ou ce que vous voulez)
4. Désactivez Google Analytics (inutile pour ce projet) → **Créer le projet**

---

## ÉTAPE 2 — Activer la Realtime Database

1. Dans le menu gauche → **Build → Realtime Database**
2. Cliquez **"Créer une base de données"**
3. Choisissez la région : **europe-west1 (Belgique)** (recommandé pour la France)
4. Choisissez le mode de démarrage : **"Mode test"** → Activer
   > ⚠️ Le mode test autorise tout accès pendant 30 jours.
   > On sécurisera ça à l'étape 5.

---

## ÉTAPE 3 — Récupérer votre config Firebase

1. Cliquez sur l'icône ⚙️ (Paramètres) → **Paramètres du projet**
2. Faites défiler jusqu'à **"Vos applications"**
3. Cliquez **"</>  Ajouter une application web"**
4. Nom : `terminal7-web` → **Enregistrer**
5. Firebase vous affiche un bloc de code comme celui-ci :

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "terminal7-abc.firebaseapp.com",
  databaseURL: "https://terminal7-abc-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "terminal7-abc",
  storageBucket: "terminal7-abc.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

6. **Copiez ce bloc entier** et remplacez la section `firebaseConfig` dans :
   - `terminal7-joueur.html` (vers la ligne 12)
   - `terminal7-admin.html` (vers la ligne 12)
   Les deux fichiers doivent avoir **exactement la même config**.

---

## ÉTAPE 4 — Initialiser les données dans Firebase

1. Dans la console Firebase → **Realtime Database**
2. Cliquez sur le bouton **"⋮"** (trois points) → **"Importer JSON"**
3. Copiez-collez le JSON suivant (structure de départ) :

```json
{
  "config": {
    "motDePasse": "club ados",
    "codeVictoire": "OMEGA-7-LIBRE",
    "msgVictoire": "Félicitations, agent !\nVous avez déverrouillé le système TERMINAL-7.\nTransmettez le code suivant au maître du jeu :",
    "sessionId": "session_init"
  },
  "enigmes": [
    {
      "titre": "ÉNIGME 01 — IDENTIFICATION",
      "intro": "Données chiffrées détectées dans le secteur 7.\nUn scientifique a laissé un message codé avant de disparaître.\nSon message :\n\n« Je suis l'inverse du jour, je vis dans la nuit.\n  On me craint, mais je guide les perdus.\n  Les astronomes me connaissent bien. »\n\nIdentifiez l'entité décrite pour déverrouiller le niveau suivant.",
      "indice1": "L'entité se trouve dans le ciel. Elle n'est visible que la nuit.",
      "indice2": "Elle émet sa propre lumière. Elle n'a pas besoin du Soleil.",
      "indice3": "Les navigateurs l'utilisaient pour s'orienter. La Polaire en est une.",
      "echec": "Réponse incorrecte. L'entité n'est pas identifiée. Relisez le message.",
      "victoire": ">>> RÉPONSE VALIDÉE <<<\nL'entité est une ÉTOILE.\nVerrou 01 désactivé. Accès au niveau suivant... accordé.",
      "reponses": ["etoile", "etoiles", "une etoile", "les etoiles"]
    },
    {
      "titre": "ÉNIGME 02 — DÉCRYPTAGE",
      "intro": "Nouveau secteur chargé. Niveau de sécurité élevé.\nUn code a été retrouvé gravé sur le mur :\n\n  A=1  B=2  C=3  D=4  E=5  F=6  G=7  H=8  I=9\n  J=10 K=11 L=12 M=13 N=14 O=15 P=16 Q=17 R=18\n  S=19 T=20 U=21 V=22 W=23 X=24 Y=25 Z=26\n\nMessage chiffré : 6  5  21\n\nDécodez ce message.",
      "indice1": "Chaque nombre correspond à une lettre. A=1, B=2, C=3...",
      "indice2": "Le nombre 6 donne F. Le nombre 5 donne E. Continuez...",
      "indice3": "F-E-U. Quelque chose de chaud, lumineux et dangereux.",
      "echec": "Séquence incorrecte. Vérifiez votre décodage. Chaque nombre = une lettre.",
      "victoire": ">>> RÉPONSE VALIDÉE <<<\nMessage décodé : FEU\nVerrou 02 désactivé. Accès au niveau suivant... accordé.",
      "reponses": ["feu", "le feu"]
    },
    {
      "titre": "ÉNIGME 03 — ACCÈS FINAL",
      "intro": "Dernier verrou de sécurité activé.\nLe Dr. VOSS a laissé une ultime énigme :\n\n  « J'ai des villes, mais pas de maisons.\n    J'ai des montagnes, mais pas d'arbres.\n    J'ai de l'eau, mais pas de poissons.\n    J'ai des routes, mais pas de voitures.\n    Qu'est-ce que je suis ? »\n\nRépondez correctement pour libérer le système.",
      "indice1": "L'entité représente des choses réelles mais n'est pas réelle elle-même.",
      "indice2": "On la déplie. On la consulte. On y cherche son chemin.",
      "indice3": "Elle est en papier. Les explorateurs ne peuvent pas s'en passer.",
      "echec": "Accès refusé. La réponse n'est pas reconnue.",
      "victoire": ">>> RÉPONSE FINALE VALIDÉE <<<\nEntité identifiée : CARTE\nTous les verrous sont désactivés. Le système est libéré.",
      "reponses": ["carte", "une carte", "plan", "un plan", "la carte"]
    }
  ]
}
```

> ✅ Cette structure sera visible dans Firebase. Vous pourrez la modifier
> directement depuis le panneau admin (terminal7-admin.html).

---

## ÉTAPE 5 — Règles de sécurité Firebase

Dans Firebase → Realtime Database → **Règles** :

Remplacez le contenu par :

```json
{
  "rules": {
    "config": {
      ".read": true,
      ".write": "auth == null"
    },
    "enigmes": {
      ".read": true,
      ".write": "auth == null"
    },
    "joueurs": {
      ".read": "auth == null",
      ".write": "auth == null"
    },
    "classement": {
      ".read": true,
      ".write": "auth == null"
    }
  }
}
```

> ⚠️ Ces règles permettent l'accès sans authentification (adapté à une salle
> fermée / réseau local). Si vous voulez plus de sécurité, ajoutez Firebase
> Authentication.

Cliquez **Publier**.

---

## ÉTAPE 6 — Structure des données expliquée

```
Firebase Realtime Database
│
├── config/
│   ├── motDePasse       → "club ados"
│   ├── codeVictoire     → "OMEGA-7-LIBRE"
│   ├── msgVictoire      → texte affiché à la victoire
│   └── sessionId        → "session_1234567890"  ← change à chaque reset
│
├── enigmes/
│   ├── 0/  { titre, intro, indice1/2/3, echec, victoire, reponses[] }
│   ├── 1/  { ... }
│   └── 2/  { ... }
│
├── joueurs/
│   └── session_xxx/             ← session en cours
│       └── prenom_du_joueur/
│           ├── prenom
│           ├── joueurId
│           ├── enigmeIdx        ← étape actuelle
│           ├── indiceIdx        ← indices utilisés
│           ├── tempsSecondes    ← chrono sauvegardé
│           ├── fini             ← true si terminé
│           └── connecteAt
│
└── classement/
    └── session_xxx/
        └── prenom_du_joueur/
            ├── prenom
            ├── tempsSecondes    ← temps final avec pénalités
            ├── indicesUtilises
            └── finAt
```

---

## ÉTAPE 7 — Déploiement

### Option A — Fichiers locaux (réseau local, sans internet)
Ouvrez simplement `terminal7-joueur.html` et `terminal7-admin.html`
dans un navigateur. Firebase fonctionne via internet.

### Option B — Hébergement Firebase (recommandé)

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# Choisissez votre projet, répertoire public = "."
firebase deploy
```

Vous obtenez une URL type `https://terminal7-abc.web.app`.

### Option C — GitHub Pages ou Netlify
Glissez-déposez vos fichiers sur **https://app.netlify.com/drop**
→ URL publique instantanée.

---

## ÉTAPE 8 — Utilisation en escape game

### Avant la partie (admin)
1. Ouvrez `terminal7-admin.html` sur votre PC
2. Connectez-vous avec le mot de passe `admin`
3. Onglet **CONFIG** : définissez le mot de passe joueur, le code victoire, les messages
4. Onglet **ÉNIGMES** : créez/modifiez vos énigmes
5. Cliquez **✓ SAUVEGARDER DANS FIREBASE**
6. Onglet **EN DIRECT** : vous voyez les joueurs apparaître en temps réel

### Pendant la partie (joueurs)
1. Chaque joueur ouvre `terminal7-joueur.html` sur son téléphone/tablette
2. Entre son prénom + le mot de passe de session
3. Joue les énigmes — progression sauvegardée automatiquement
4. En cas de déconnexion : il se reconnecte avec le même prénom + mot de passe
   → il reprend exactement là où il en était

### Réinitialiser entre deux groupes
1. Panneau admin → **⚠ RÉINITIALISER LE JEU**
2. Confirmer → tous les joueurs sont déconnectés instantanément
3. Nouvelle session créée → les joueurs doivent se réinscrire

---

## Récapitulatif des pénalités

| Action               | Pénalité         |
|----------------------|------------------|
| Mauvaise réponse     | +2 minutes       |
| Utiliser un indice   | +3 minutes/indice|
| Pas de chrono max    | Chrono infini     |

---

## Dépannage

**"ERREUR : Config introuvable dans Firebase"**
→ Vérifiez que vous avez bien importé le JSON à l'étape 4,
  et que votre `firebaseConfig` est correcte.

**Le chrono ne se met pas à jour en direct dans l'admin**
→ Normal : le chrono tourne côté joueur et se synchronise toutes les 10s.
  L'admin affiche le temps interpolé localement.

**Un joueur ne peut pas se reconnecter**
→ Vérifiez qu'il tape le même prénom (insensible à la casse)
  ET le même mot de passe.

**Le reset ne fonctionne pas**
→ Vérifiez les règles de sécurité Firebase (étape 5).
