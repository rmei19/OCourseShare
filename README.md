# 🛒 OCourseShare

Scanner un ticket de caisse avec le téléphone, en extraire automatiquement les articles, les classer par catégorie, et répartir la note entre plusieurs personnes — le tout dans **un seul fichier HTML autonome**, sans compte, sans base de données, sans backend obligatoire.

> Projet personnel, pensé pour un usage quotidien (courses, restaurant, notes de frais partagées) sur téléphone Android.

---

## ✨ Fonctionnalités

**Scan & OCR**
- Reconnaissance de texte 100% locale via [Tesseract.js](https://github.com/naptha/tesseract.js) (français + espagnol)
- Recadrage automatique du ticket (ignore le fond de la photo)
- Jusqu'à 4 passes d'amélioration d'image (contraste, seuillage noir/blanc, mise en page alternative)
- Détection et regroupement des formats de ticket à une ou deux lignes par article
- Gestion des notations de quantité variées (`1.000x`, `2 x`, vente au poids `0.882kg x 8.20€/kg`...)

**Analyse & organisation**
- Trois modes : **Courses**, **Restaurant**, **Générique** — chacun avec son propre jeu de catégories
- Catégorisation automatique par mots-clés, catégories personnalisées ajoutables à la volée
- Édition complète en ligne : nom, quantité, prix, catégorie
- Recherche instantanée dans la liste
- Fusion automatique des doublons mal lus par l'OCR (similarité floue tolérante au bruit)
- **Mémoire de corrections** : les renommages et changements de catégorie sont retenus et réappliqués automatiquement sur les tickets suivants
- Historique et moyennes de prix par article dans le temps

**Partage entre personnes**
- Attribution d'articles à une ou plusieurs personnes (partage au prorata)
- Calcul automatique de qui doit combien à qui a payé

**IA en secours (optionnelle)**
- Bouton "Analyse de secours" qui envoie le texte OCR à un modèle de langage (via un [Cloudflare Worker](https://developers.cloudflare.com/workers-ai/) gratuit) pour un découpage plus robuste sur les formats inhabituels
- Choix du modèle (rapide / plus fiable) directement dans l'app
- 100% optionnel : l'app fonctionne entièrement sans, le parsing local reste prioritaire

**Divers**
- Export / import JSON (sauvegarde, transfert entre appareils)
- Interface pensée mobile : sections repliables, colonnes optimisées, tactile confortable

---

## 🚀 Démarrage rapide

1. Télécharge `index.html` (ou clone le dépôt) et ouvre-le dans un navigateur mobile (Chrome/Safari — évite les navigateurs avec bac à sable renforcé type Brave en `file://` local, préfère un hébergement `https://`).
2. Prends une photo de ticket ou choisis-en une depuis la galerie.
3. Vérifie/corrige le texte reconnu si besoin, puis laisse l'app analyser automatiquement.
4. Ajuste, catégorise, assigne aux personnes concernées.

Aucune installation, aucune dépendance à builder : c'est un fichier HTML qui charge Tesseract.js depuis un CDN au premier lancement.

---

## 🌐 Déploiement sur GitHub Pages

1. Renomme le fichier principal en `index.html` à la racine du dépôt (ou dans `/docs` si tu préfères cette convention).
2. Dans les paramètres du dépôt GitHub → **Pages** → source : branche `main`, dossier `/ (root)` (ou `/docs`).
3. L'app sera servie sur `https://<ton-pseudo>.github.io/<nom-du-repo>/`.

⚠️ Le navigateur doit accéder au site en `https://`, pas en fichier local — certaines fonctions (lecture de fichier, appel réseau vers le Worker IA) sont bloquées par les navigateurs sous `file://`.

---

## 🤖 Worker IA (optionnel)

Le fichier `worker.js` est un [Cloudflare Worker](https://workers.cloudflare.com/) qui sert de relais gratuit vers un modèle de langage (Workers AI), sans exposer de clé API.

**Déploiement :**
1. Crée un Worker sur le [dashboard Cloudflare](https://dash.cloudflare.com/).
2. Colle le contenu de `worker.js`.
3. Ajoute le binding **Workers AI** nommé `AI` (Réglages → Bindings → Add → Workers AI).
4. Déploie, récupère l'URL (`https://<nom>.<compte>.workers.dev`).
5. Colle cette URL dans le champ prévu dans l'app (section "🤖 Analyse de secours").

L'URL est stockée uniquement dans le `localStorage` de ton navigateur — jamais dans le code source.

---

## 🛠️ Stack technique

| Composant | Rôle |
|---|---|
| [Tesseract.js](https://github.com/naptha/tesseract.js) | OCR, 100% côté client |
| JavaScript vanilla | Toute la logique (aucun framework, aucun build) |
| `localStorage` | Persistance (articles, personnes, catégories, mémoire de corrections) |
| Cloudflare Workers AI *(optionnel)* | Modèle de langage de secours pour le découpage des tickets |

---

## 🔒 Confidentialité

- Les photos ne quittent **jamais** ton appareil : l'OCR tourne entièrement en local.
- Aucune donnée n'est envoyée à un serveur, **sauf** si tu utilises volontairement le bouton "Analyse de secours" (le texte OCR est alors envoyé à ton propre Worker Cloudflare).
- Rien n'est stocké ailleurs que dans le `localStorage` de ton navigateur.

---

## ⚠️ Limites connues

- La qualité de la photo reste le facteur le plus déterminant pour la précision de l'OCR (lumière, netteté, ticket à plat).
- Les modèles gratuits utilisés par le Worker IA (Llama 3.x, quelques milliards de paramètres) sont moins fiables qu'une API IA payante — ils peuvent oublier des lignes ou mal catégoriser sur des tickets longs ou très abîmés.
- Testé principalement sur tickets espagnols et français ; d'autres formats peuvent nécessiter des ajustements.

---

## 📄 Licence

À définir selon ton usage prévu — voir [choosealicense.com](https://choosealicense.com/) si tu comptes rendre le dépôt public. [MIT](https://choosealicense.com/licenses/mit/) est un choix simple et permissif si tu n'as pas de préférence.
