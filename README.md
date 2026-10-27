# ⚔ WAR-ROOM LOCAL

Outil d'analyse de listes d'armée **Warhammer 40,000 (11e)** et **Age of Sigmar (4e)**, 100 % côté navigateur : un seul fichier `index.html`, aucune API d'IA, aucun serveur à maintenir.

Collez une liste d'armée, lancez l'analyse : l'outil produit un rapport (scores, règles clés, gameplan, plan contre, matchups, vulnérabilités) exportable en PDF.

## Fonctionnalités

- **Analyse de liste** 40k et AoS (moteur heuristique local).
- **Import de données** Wahapedia (40k) et CSV Age of Sigmar pour une analyse sur les vraies fiches, stockées dans le navigateur (IndexedDB).
- **Historique** des analyses, synchronisé en temps réel avec **Firestore** (multi-appareils).
- **Export PDF** compressé automatiquement pour rester sous 10 Mo.
- **Réglages protégés par mot de passe** (imports de données).

## Utilisation en local

Ouvrez simplement `index.html` dans un navigateur récent (connexion internet nécessaire pour charger Tailwind, Alpine, jsPDF, html2canvas et Firebase depuis leurs CDN).

## Déploiement sur GitHub Pages

1. Créez un dépôt GitHub et envoyez-y `index.html` et `README.md` à la racine.
2. Dans le dépôt : **Settings → Pages**.
3. **Source** : « Deploy from a branch », branche `main`, dossier `/ (root)` → **Save**.
4. Après une minute environ, l'outil est disponible sur `https://<votre-compte>.github.io/<nom-du-depot>/`.

## Configuration Firestore

La configuration Firebase se trouve dans l'objet `CLOUD.config` du fichier `index.html`. Les analyses sont enregistrées dans la collection `warroom_analyses` (un document par analyse).

1. Console Firebase → **Firestore Database** → créer la base si ce n'est pas fait.
2. Onglet **Règles** : autorisez la collection utilisée, par exemple :

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /warroom_analyses/{id} {
      allow read, write: if true;
    }
  }
}
```

> ⚠️ Ces règles ouvrent la collection à toute personne connaissant l'adresse du site, et toutes les analyses sont partagées (pas de comptes utilisateurs). Pour restreindre l'accès, ajoutez Firebase Auth et des règles basées sur `request.auth`.

**Bonnes pratiques :** la clé d'API Firebase est publique par conception (la sécurité repose sur les règles Firestore). Vous pouvez en plus la restreindre aux référents HTTP de votre site (`https://<votre-compte>.github.io/*`) dans la console Google Cloud → **API et services → Identifiants**.

## Réglages et import de données

Le bouton **⚙ Réglages** (en-tête) ouvre un panneau protégé par mot de passe, qui contient :

- le pack de données personnalisé (JSON) ;
- l'import **Wahapedia** 40k (CSV « Export Data », séparateur `|`) ;
- l'import **Age of Sigmar** (CSV) et le pack de règles AoS (JSON).

Le mot de passe est défini en dur dans `index.html` (constante `SETTINGS_PASSWORD`) et n'est jamais envoyé à Firestore. Il est lisible dans le code source de la page : c'est un garde-fou, pas une sécurité forte. Pensez à le modifier avant de publier le dépôt.

Les données importées restent dans le navigateur de chaque utilisateur ; seules les analyses de l'historique sont synchronisées.

## Structure du dépôt

```
├── index.html   # l'application complète (HTML + CSS + JS)
└── README.md
```

## Technologies

Tailwind CSS · Alpine.js · jsPDF · html2canvas · Firebase (Firestore, SDK compat) — tous chargés via CDN.

## Avertissement

Analyse heuristique locale : vérifiez toujours les règles officielles (Wahapedia, publications Games Workshop) avant utilisation.
