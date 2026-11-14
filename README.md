# annur-maj — flux de mise à jour d'An-Nur

Ce dépôt public ne contient **aucun code source**. Il sert uniquement le
système de mise à jour intégré à l'application An-Nur (à partir de la
version 3.17.67) :

- **`version.json`** (branche `main`, racine) : lu par l'application à
  l'ouverture (au plus une fois par jour) pour savoir si une version
  plus récente existe.
- **Les APK** : un fichier par version, attaché comme *asset* à la
  Release GitHub de même nom (ex. Release `v3.17.67`, asset
  `An-Nur-v3.17.67.apk`).

Format de `version.json` :

```json
{
  "versionCode": 88,
  "versionName": "3.17.67",
  "apkUrl": "https://github.com/Mouc878/annur-maj/releases/download/v3.17.67/An-Nur-v3.17.67.apk",
  "sha256": "<empreinte SHA-256 de l'APK signé>",
  "sizeBytes": 145459964,
  "notes": "<2-3 lignes de nouveautés, français simple>",
  "publishedAt": "2026-11-14"
}
```

## État au 2026-11-14 (build .67)

- Le connecteur GitHub de l'assistant **ne peut pas créer de dépôt**
  (réponse de l'API : « Resource not accessible by integration ») et
  **ne peut ni créer de Release ni téléverser d'asset binaire**.
- **Actions manuelles du propriétaire, une seule fois** :
  1. Créer le dépôt **public** `annur-maj` (vide, sans README) sur
     github.com — 30 secondes.
  2. Si GitHub le demande, ajouter `annur-maj` à la sélection de
     dépôts de l'application GitHub « Muse » (réglages de
     l'installation) pour que l'assistant puisse y pousser.
  3. Dire « le dépôt est créé » à l'assistant : il pousse alors
     `version.json` + ce README et vérifie l'URL publique.
- **Action manuelle à chaque version** : créer la Release et y joindre
  l'APK (voir procédure ci-dessous, étape 1). Tout le reste
  (génération de version.json, poussée, vérifications) est fait par
  l'assistant dans la livraison du build.

## Procédure de publication d'une version (à chaque nouveau build)

1. **Créer la Release** sur GitHub (page du dépôt → *Releases* →
   *Draft a new release*) : tag = `v<versionName>` (ex. `v3.17.67`),
   titre libre, et **joindre l'APK signé** renommé
   `An-Nur-v<versionName>.apk` comme asset.
   ⚠️ Toujours créer la Release AVANT de mettre à jour
   `version.json` : sinon les téléphones annonceraient une version
   dont le fichier n'est pas encore téléchargeable (l'app gère
   l'échec proprement, mais l'annonce serait prématurée).
2. **Mettre à jour `version.json`** avec les valeurs du nouvel APK
   (le script `publier-version.sh` du dossier `flux/` du build
   génère le fichier complet à partir de l'APK signé : SHA-256 et
   taille calculés, URL construite). Ce fichier est poussé par
   l'assistant via le connecteur GitHub dans la livraison du build.
3. **Vérifier** : ouvrir
   `https://raw.githubusercontent.com/Mouc878/annur-maj/main/version.json`
   — le fichier servi doit être le nouveau. Les téléphones à jour le
   liront sous 24 h (ou tout de suite via Paramètres → Mise à jour).

Sécurité : l'application ne propose l'installation qu'après trois
contrôles sur le fichier téléchargé — SHA-256 égal à `version.json`,
certificat de signature égal à celui de l'éditeur, même paquet et
même versionCode que le flux. Un fichier altéré est supprimé, jamais
installé.
