# Design — Fonctionnalités pratiques : École Ibn Rachik

**Date :** 2026-06-26  
**Statut :** Approuvé  

---

## Contexte

Le site actuel est une page HTML unique et statique (`index.html`). Il contient les sections : hero, à propos, niveaux scolaires, inscriptions, pourquoi Ibn Rachik, contact, et réseaux sociaux. Aucun formulaire n'est fonctionnel, aucune actualité n'est dynamique.

**Objectif :** Ajouter deux fonctionnalités pratiques sans backend ni serveur :
1. Un formulaire de contact fonctionnel (envoi d'email)
2. Une section actualités (annonces texte + publications Facebook avec photos)

**Contrainte principale :** La personne qui gérera le site est non-informaticienne. Elle ne doit jamais toucher au code après la mise en place initiale.

---

## Fonctionnalité 1 — Formulaire de contact fonctionnel

### Solution
**Formspree** (https://formspree.io) — service gratuit, 50 soumissions/mois.

### Comment ça marche
- Le formulaire HTML existant reçoit un attribut `action="https://formspree.io/f/[ID]"` et `method="POST"`
- Quand un parent clique "Envoyer", Formspree reçoit les données et envoie un email à l'adresse de l'école
- Aucun JavaScript requis, aucun serveur

### Champs du formulaire
| Champ | Type | Requis |
|-------|------|--------|
| Nom complet | text | oui |
| Téléphone | tel | oui |
| Sujet | text | oui |
| Message | textarea | oui |

### Confirmation
Après envoi, Formspree redirige vers une page de confirmation. On peut personnaliser cette page ou afficher un message inline avec un peu de JavaScript.

### Configuration (une seule fois)
1. Créer un compte sur formspree.io avec l'email de l'école
2. Créer un nouveau "form" → copier l'ID généré
3. Remplacer l'attribut `action` du formulaire HTML
4. Vérifier l'email de confirmation que Formspree envoie

---

## Fonctionnalité 2 — Section Actualités

La section se divise en deux parties visuellement distinctes, dans le style rouge/or du site.

### Partie A — Annonces rapides (texte)

**Solution :** Google Sheets publié en CSV + fetch JavaScript

#### Comment ça marche
- Un Google Sheets est créé avec 3 colonnes : `date`, `titre`, `texte`
- Le Sheets est publié publiquement en CSV (Fichier → Partager → Publier sur le web → CSV)
- JavaScript dans la page fetch ce CSV, parse les lignes, et affiche les 4 dernières annonces sous forme de cartes
- La personne en charge ajoute une ligne dans le tableau → l'annonce apparaît sur le site dans la minute

#### Structure du Google Sheets
| date | titre | texte |
|------|-------|-------|
| 15/09/2025 | Rentrée scolaire | La rentrée aura lieu le lundi 15 septembre à 8h00. |
| 28/06/2025 | Pas de cours vendredi | En raison d'une réunion pédagogique, il n'y a pas cours vendredi. |

#### Affichage
- Cartes avec fond blanc, bordure gauche rouge, date en or, titre en gras
- Maximum 4 annonces affichées (les plus récentes en premier)
- Si aucune annonce : message neutre "Aucune annonce en ce moment"

### Partie B — Publications avec photos (Facebook)

**Solution :** Facebook Page Plugin embed

#### Comment ça marche
- On insère le code d'embed officiel de Facebook Page Plugin dans la page HTML
- Il affiche les dernières publications de la page Facebook de l'école, avec photos et texte
- La personne poste sur Facebook comme elle le fait déjà → les posts apparaissent automatiquement sur le site

#### Configuration (une seule fois)
1. Aller sur developers.facebook.com/docs/plugins/page-plugin
2. Renseigner l'URL de la page Facebook de l'école
3. Copier le code généré → coller dans le HTML

#### Paramètres recommandés
- Largeur : adaptée au conteneur (responsive)
- Onglets : `timeline` (fil d'actualité)
- Hauteur : 500px

---

## Hébergement — Netlify (gratuit)

### Déploiement initial
1. Créer un compte sur netlify.com
2. Glisser-déposer le dossier du projet sur le dashboard Netlify
3. Le site est en ligne à `[nom-choisi].netlify.app`

### Mises à jour
- Re-glisser-déposer le dossier → le site se met à jour

### Domaine personnalisé (optionnel, futur)
- Acheter `ecole-ibnrachik.tn` ou similaire (~10€/an)
- Brancher sur Netlify via les DNS settings

---

## Architecture des fichiers après implémentation

```
Ecole_Ibn_Rachik/
├── index.html          ← fichier principal modifié
└── docs/
    └── superpowers/
        └── specs/
            └── 2026-06-26-fonctionnalites-pratiques-design.md
```

Tout tient dans un seul fichier HTML. Pas de dépendances externes à installer.

---

## Ce qui ne change PAS

- Le design visuel du site (couleurs, typographie, sections existantes)
- La structure HTML globale
- Les informations affichées (niveaux, inscription, à propos)

---

## Résumé des services tiers utilisés

| Service | Usage | Coût | Limite gratuite |
|---------|-------|------|-----------------|
| Formspree | Formulaire de contact | Gratuit | 50 soumissions/mois |
| Google Sheets | Annonces texte | Gratuit | Illimité |
| Facebook Page Plugin | Publications avec photos | Gratuit | Aucune |
| Netlify | Hébergement | Gratuit | 100GB bande passante/mois |
