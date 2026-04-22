---
name: github-pages-publisher
description: >
  Utilise ce skill dès que l'utilisateur veut créer, publier, mettre à jour ou pousser une page HTML
  sur GitHub Pages via le repo webpages (matthieulequimener-ux/webpages). Déclenche sur toute demande
  du type : "crée-moi une page", "publie cette page", "ajoute une landing page", "push une page HTML",
  "mets en ligne une page", "génère une page pour X". Ce skill gère automatiquement la création du HTML,
  le choix du sous-dossier, le commit et le push via l'outil github:create_or_update_file.
  Ne jamais ignorer ce skill dès qu'une page web doit être hébergée ou mise à jour sur GitHub Pages.
---

# GitHub Pages Publisher

Skill pour créer et publier des pages HTML sur GitHub Pages via le repo `webpages`.

## Contexte du repo

| Paramètre | Valeur |
|-----------|--------|
| owner | `matthieulequimener-ux` |
| repo | `webpages` |
| branch | `main` |
| URL de base | `https://matthieulequimener-ux.github.io/webpages/` |
| Déploiement | GitHub Actions (`.github/workflows/static.yml`) — push → live en ~1 min |

---

## Workflow complet

### Étape 1 — Identifier le type de page

Avant de coder, déterminer :

- **Quel projet ?** (Jamaya, Œil Augmenté, test, perso, client…)
- **Quel type de page ?** Voir table des types ci-dessous
- **Contenu fourni ou à générer ?**

Si l'utilisateur ne précise pas, poser une seule question : *"C'est pour quel projet ou usage ?"*

---

### Étape 2 — Choisir le sous-dossier

Toujours organiser dans un sous-dossier logique. Règle de nommage : **kebab-case**, court, descriptif.

| Type de page | Sous-dossier exemple | URL finale |
|---|---|---|
| Page de test | `test/` | `/webpages/test/` |
| Landing page projet | `jamaya/` | `/webpages/jamaya/` |
| Page personnelle / portfolio | `portfolio/` | `/webpages/portfolio/` |
| Page client | `clients/nom-client/` | `/webpages/clients/nom-client/` |
| Page événement | `events/nom-evenement/` | `/webpages/events/nom-evenement/` |
| Page produit / service | `services/nom-service/` | `/webpages/services/nom-service/` |
| Page de contenu / article | `articles/slug-article/` | `/webpages/articles/slug-article/` |
| Page de lien (link-in-bio) | `links/` | `/webpages/links/` |
| Expérimentation / prototype | `lab/nom-proto/` | `/webpages/lab/nom-proto/` |

Le fichier principal est toujours nommé **`index.html`** à l'intérieur du sous-dossier.

---

### Étape 3 — Générer le HTML

Lire le skill `frontend-design` (`/mnt/skills/public/frontend-design/SKILL.md`) avant de coder.

**Règles impératives pour le HTML :**

- Fichier **self-contained** : tout le CSS et JS est inline (pas de dépendances externes non CDN)
- Balises meta obligatoires :
  ```html
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="…" />
  <title>…</title>
  ```
- Utiliser Google Fonts via CDN (toujours avec `rel="preconnect"`)
- Design distinctif — lire le skill frontend-design pour les guidelines
- **Mobile-first** : le layout doit être responsive
- Pas de framework JS lourd — vanilla JS ou CSS animations seulement
- Commenter le code en français pour les sections principales

---

### Étape 4 — Pusher via l'outil GitHub

Utiliser `github:create_or_update_file` avec ces paramètres :

```
owner   : matthieulequimener-ux
repo    : webpages
branch  : main
path    : [sous-dossier]/index.html
message : ✨ Add [nom de la page] — [sous-dossier]
content : [contenu HTML complet]
```

**Si le fichier existe déjà** (mise à jour), récupérer d'abord le SHA avec `github:get_file_contents` puis passer `sha` dans l'appel.

---

### Étape 5 — Confirmer et communiquer l'URL

Après le push réussi, toujours communiquer :

1. ✅ Confirmation du commit
2. 🔗 URL live : `https://matthieulequimener-ux.github.io/webpages/[sous-dossier]/`
3. ⏱ Délai : "La page sera live dans ~1 minute via GitHub Actions"
4. 📁 Chemin dans le repo : `webpages/[sous-dossier]/index.html`

---

## Gestion des mises à jour

Quand l'utilisateur demande à **modifier une page existante** :

1. Utiliser `github:get_file_contents` pour récupérer le SHA actuel
2. Modifier le HTML selon la demande
3. Passer le `sha` dans `github:create_or_update_file`
4. Message de commit : `♻️ Update [nom] — [changement résumé en quelques mots]`

---

## Convention de commit messages

| Action | Emoji | Format |
|--------|-------|--------|
| Nouvelle page | ✨ | `✨ Add [nom] — [dossier]` |
| Mise à jour contenu | ♻️ | `♻️ Update [nom] — [résumé]` |
| Fix bug / correction | 🐛 | `🐛 Fix [description]` |
| Ajout asset (image, font) | 🖼️ | `🖼️ Add asset [nom]` |
| Suppression | 🗑️ | `🗑️ Remove [nom]` |
| Config / workflow | 🚀 | `🚀 [description config]` |

---

## Structure type du repo webpages

```
webpages/
├── .github/
│   └── workflows/
│       └── static.yml          ← GitHub Actions (ne pas modifier)
├── README.md
├── test/
│   └── index.html              ← Page de test existante
├── [nouveau-projet]/
│   └── index.html
└── ...
```

---

## Erreurs fréquentes à éviter

| Erreur | Solution |
|--------|----------|
| Fichier nommé autre chose que `index.html` | Toujours nommer `index.html` pour que l'URL soit propre |
| Push sans SHA sur un fichier existant | Toujours récupérer le SHA avant une mise à jour |
| CSS/JS dans des fichiers séparés | Tout inline dans le HTML — repo simple, pas de build |
| Dossier mal nommé (majuscules, espaces) | Toujours kebab-case, tout en minuscules |
| Oublier les meta tags | Vérifier charset, viewport, description, title avant push |
