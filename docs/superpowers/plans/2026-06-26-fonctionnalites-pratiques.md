# Fonctionnalités Pratiques — Plan d'implémentation

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ajouter un formulaire de contact fonctionnel (email via Formspree) et une section Actualités (annonces texte via Google Sheets + publications Facebook) à la page `index.html` de l'École Ibn Rachik.

**Architecture:** Tout tient dans un seul fichier `index.html`. Pas de build, pas de framework, pas de serveur. Les services tiers (Formspree, Google Sheets CSV, Facebook Page Plugin) sont appelés directement depuis le navigateur du visiteur.

**Tech Stack:** HTML5, CSS3, JavaScript vanilla (fetch async/await), Formspree (formulaire), Google Sheets CSV public, Facebook Page Plugin SDK, Netlify (hébergement)

---

## Fichiers modifiés

| Fichier | Action | Description |
|---------|--------|-------------|
| `index.html` | Modifier | Formulaire de contact, section Actualités, nav |

---

## Task 1 : Créer le compte Formspree et obtenir l'ID de formulaire

> Étape manuelle — aucun code. À faire AVANT de modifier index.html.

**Files:**
- Modify: `index.html` (la valeur d'ID sera utilisée à la Task 2)

- [ ] **Step 1 : Créer un compte Formspree**

  Aller sur https://formspree.io → cliquer "Get Started" → s'inscrire avec l'adresse email de l'école (ex: ibnrachik.ouardanine@gmail.com).

- [ ] **Step 2 : Créer un nouveau formulaire**

  Dans le dashboard Formspree :
  1. Cliquer "+ New Form"
  2. Nommer le formulaire : `Contact École Ibn Rachik`
  3. Cliquer "Create Form"

- [ ] **Step 3 : Copier l'ID du formulaire**

  Formspree affiche une URL du type :
  ```
  https://formspree.io/f/xpwzqkrb
  ```
  Copier la partie après `/f/` — ici `xpwzqkrb`. C'est votre **FORM_ID**.
  Le noter quelque part, il sera utilisé à la Task 2.

- [ ] **Step 4 : Vérifier l'email de confirmation**

  Formspree envoie un email à l'adresse de l'école pour confirmer. Cliquer le lien de confirmation dans l'email.

---

## Task 2 : Brancher le formulaire de contact sur Formspree

**Files:**
- Modify: `index.html` lignes 1212–1254

- [ ] **Step 1 : Remplacer le bloc `<div class="contact-form">` entier**

  Dans `index.html`, trouver le bloc qui commence à la ligne 1212 :
  ```html
  <div class="contact-form">
  ```
  Remplacer tout le contenu de `<div class="contact-form">` jusqu'à son `</div>` fermant (ligne ~1254) par le code suivant.

  **⚠️ Remplacer `VOTRE_FORM_ID` par l'ID copié à la Task 1 (ex: `xpwzqkrb`).**

  ```html
  <div class="contact-form">
    <h3>Demande d'information</h3>

    <div id="form-success" style="display:none; background:#d4edda; border:1px solid #c3e6cb; border-radius:8px; padding:20px; text-align:center; margin-bottom:20px;">
      <p style="color:#155724; font-weight:700; font-size:16px; margin:0;">✅ Message envoyé avec succès !</p>
      <p style="color:#155724; font-size:14px; margin:8px 0 0;">Nous vous contacterons très bientôt.</p>
    </div>

    <form id="contact-form"
          action="https://formspree.io/f/VOTRE_FORM_ID"
          method="POST">

      <input type="hidden" name="_subject" value="Nouvelle demande — École Ibn Rachik">
      <input type="hidden" name="_language" value="fr">

      <div class="form-row">
        <div class="form-group">
          <label>Prénom et Nom *</label>
          <input type="text" name="nom" placeholder="Votre nom complet" required/>
        </div>
        <div class="form-group">
          <label>Téléphone *</label>
          <input type="tel" name="telephone" placeholder="Votre numéro" required/>
        </div>
      </div>

      <div class="form-group">
        <label>Objet de la demande</label>
        <select name="objet">
          <option value="">Sélectionnez...</option>
          <option>Inscription 2026-2027</option>
          <option>Renseignements généraux</option>
          <option>Visite de l'école</option>
          <option>Programme pédagogique</option>
          <option>Autre</option>
        </select>
      </div>

      <div class="form-group">
        <label>Niveau souhaité</label>
        <select name="niveau">
          <option value="">Sélectionnez le niveau...</option>
          <option>Classe Préparatoire</option>
          <option>1ère année primaire</option>
          <option>2ème année primaire</option>
          <option>3ème année primaire</option>
          <option>4ème année primaire</option>
          <option>5ème année primaire</option>
          <option>6ème année primaire</option>
        </select>
      </div>

      <div class="form-group">
        <label>Message (optionnel)</label>
        <textarea name="message" placeholder="Votre message..."></textarea>
      </div>

      <button type="submit" class="btn-submit" id="btn-submit">
        Envoyer la demande →
      </button>
    </form>
  </div>
  ```

- [ ] **Step 2 : Ajouter le JavaScript de gestion du formulaire**

  Juste avant la balise `</body>` (dernière ligne du fichier), ajouter :

  ```html
  <script>
    // Formulaire de contact Formspree
    const form = document.getElementById('contact-form');
    if (form) {
      form.addEventListener('submit', async function(e) {
        e.preventDefault();
        const btn = document.getElementById('btn-submit');
        btn.textContent = 'Envoi en cours...';
        btn.disabled = true;

        const data = new FormData(form);
        const response = await fetch(form.action, {
          method: 'POST',
          body: data,
          headers: { 'Accept': 'application/json' }
        });

        if (response.ok) {
          form.style.display = 'none';
          document.getElementById('form-success').style.display = 'block';
        } else {
          btn.textContent = 'Erreur — réessayez';
          btn.disabled = false;
        }
      });
    }
  </script>
  ```

- [ ] **Step 3 : Vérifier dans le navigateur**

  1. Ouvrir `index.html` dans le navigateur (double-clic sur le fichier)
  2. Scroller jusqu'à la section "Contact"
  3. Remplir le formulaire avec un vrai nom, téléphone, et message
  4. Cliquer "Envoyer la demande →"

  **Résultat attendu :** Le message vert "✅ Message envoyé avec succès !" apparaît. Un email arrive dans la boîte de l'école.

  > Note : Le formulaire ne fonctionne PAS en ouvrant le fichier local (file://) si le navigateur bloque les requêtes cross-origin. Il fonctionnera correctement une fois déployé sur Netlify (Task 7). Pour tester localement, utiliser un serveur local (voir note à la fin de ce plan).

---

## Task 3 : Créer le Google Sheets des annonces

> Étape manuelle — aucun code. À faire AVANT la Task 4.

**Files:**
- Modify: `index.html` (l'URL du sheet sera utilisée à la Task 4)

- [ ] **Step 1 : Créer le fichier Google Sheets**

  Aller sur https://sheets.google.com → cliquer "+ Nouveau" → créer un nouveau fichier Google Sheets.
  Nommer le fichier : `Annonces — École Ibn Rachik`

- [ ] **Step 2 : Créer les colonnes**

  Dans la première ligne (ligne 1, qui est l'en-tête), écrire exactement :
  - Cellule A1 : `date`
  - Cellule B1 : `titre`
  - Cellule C1 : `texte`

- [ ] **Step 3 : Ajouter des annonces de test**

  Ajouter 2 lignes d'exemple (lignes 2 et 3) :

  | date | titre | texte |
  |------|-------|-------|
  | 15/09/2025 | Rentrée scolaire 2025-2026 | La rentrée aura lieu le lundi 15 septembre à 8h00. Bienvenue à toutes les familles ! |
  | 28/06/2025 | Résultats de fin d'année | Félicitations à tous nos élèves pour leurs excellents résultats ! |

- [ ] **Step 4 : Publier le sheet en CSV**

  1. Dans Google Sheets, cliquer **Fichier** → **Partager** → **Publier sur le Web**
  2. Dans le premier menu déroulant : sélectionner **"Feuille 1"**
  3. Dans le deuxième menu déroulant : sélectionner **"Valeurs séparées par des virgules (.csv)"**
  4. Cliquer **"Publier"** → confirmer avec "OK"
  5. Copier l'URL affichée — elle ressemble à :
     ```
     https://docs.google.com/spreadsheets/d/ABCDEF123456/pub?gid=0&single=true&output=csv
     ```
  6. Noter cette URL — elle sera utilisée à la Task 4.

---

## Task 4 : Ajouter la section Actualités (annonces texte)

**Files:**
- Modify: `index.html` — ajouter CSS dans `<style>`, ajouter section HTML + JavaScript

- [ ] **Step 1 : Ajouter les styles CSS des annonces**

  Dans `index.html`, trouver la ligne qui contient `/* ── FOOTER ── */` (ligne ~753).
  **Avant** cette ligne, insérer le bloc CSS suivant :

  ```css
  /* ── ACTUALITÉS ── */
  #actualites {
    background: var(--off-white);
    padding: 80px 5%;
  }

  .actualites-inner {
    max-width: 1100px;
    margin: 0 auto;
  }

  .actualites-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 48px;
    margin-top: 48px;
    align-items: start;
  }

  .actualites-col-title {
    font-size: 17px;
    font-weight: 700;
    margin-bottom: 20px;
    color: #222;
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .actualites-col-title::before {
    content: '';
    display: inline-block;
    width: 4px;
    height: 20px;
    background: var(--red);
    border-radius: 2px;
    flex-shrink: 0;
  }

  .annonces-list {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  .annonce-card {
    background: var(--white);
    border-left: 4px solid var(--red);
    border-radius: 8px;
    padding: 20px 24px;
    box-shadow: 0 2px 12px rgba(0,0,0,0.06);
    transition: transform 0.2s;
  }

  .annonce-card:hover { transform: translateX(4px); }

  .annonce-date {
    font-size: 11px;
    font-weight: 700;
    color: var(--white);
    text-transform: uppercase;
    letter-spacing: 1px;
    background: var(--gold);
    color: #111;
    display: inline-block;
    padding: 2px 10px;
    border-radius: 4px;
    margin-bottom: 10px;
  }

  .annonce-titre {
    font-size: 15px;
    font-weight: 700;
    margin-bottom: 6px;
    color: #222;
  }

  .annonce-texte {
    font-size: 13px;
    color: var(--gray);
    line-height: 1.6;
    margin: 0;
  }

  .annonces-vides {
    color: var(--gray);
    font-size: 14px;
    font-style: italic;
    padding: 20px;
    text-align: center;
  }

  .fb-page-wrapper {
    display: flex;
    justify-content: center;
    overflow: hidden;
    border-radius: 12px;
    box-shadow: 0 4px 24px rgba(0,0,0,0.1);
    background: #fff;
  }
  ```

- [ ] **Step 2 : Ajouter la section HTML Actualités**

  Dans `index.html`, trouver la ligne qui commence par :
  ```html
  <!-- SOCIAL MEDIA SECTION -->
  ```
  (ligne ~1258). **Avant** cette ligne, insérer la section suivante.

  **⚠️ Remplacer `VOTRE_URL_GOOGLE_SHEETS` par l'URL copiée à la Task 3 Step 4.**

  ```html
  <!-- ACTUALITÉS SECTION -->
  <section id="actualites">
    <div class="actualites-inner">
      <div class="section-label">Vie de l'école</div>
      <h2 class="section-title">Actualités & Annonces</h2>
      <div class="section-line"></div>

      <div class="actualites-grid">

        <!-- Colonne gauche : annonces texte -->
        <div>
          <p class="actualites-col-title">📢 Annonces récentes</p>
          <div class="annonces-list" id="annonces-container">
            <p class="annonces-vides">Chargement des annonces...</p>
          </div>
        </div>

        <!-- Colonne droite : publications Facebook -->
        <div>
          <p class="actualites-col-title">📸 Publications Facebook</p>
          <div class="fb-page-wrapper">
            <div class="fb-page"
              data-href="https://www.facebook.com/profile.php?id=100063631150397"
              data-tabs="timeline"
              data-width="500"
              data-height="600"
              data-small-header="true"
              data-adapt-container-width="true"
              data-hide-cover="false"
              data-show-facepile="false">
            </div>
          </div>
        </div>

      </div>
    </div>
  </section>
  ```

- [ ] **Step 3 : Ajouter le JavaScript de chargement des annonces**

  Juste **après** le `<script>` ajouté à la Task 2 (toujours avant `</body>`), ajouter :

  ```html
  <script>
    // Annonces depuis Google Sheets
    async function chargerAnnonces() {
      const SHEET_URL = 'VOTRE_URL_GOOGLE_SHEETS';
      const conteneur = document.getElementById('annonces-container');
      if (!conteneur) return;

      try {
        const response = await fetch(SHEET_URL);
        const texte = await response.text();
        const lignes = texte.trim().split('\n').slice(1);

        const annonces = lignes
          .map(ligne => {
            const cols = ligne.split(',').map(c => c.trim().replace(/^"|"$/g, ''));
            return { date: cols[0], titre: cols[1], texte: cols[2] };
          })
          .filter(a => a.titre && a.titre.length > 0)
          .slice(0, 4);

        if (annonces.length === 0) {
          conteneur.innerHTML = '<p class="annonces-vides">Aucune annonce pour le moment.</p>';
          return;
        }

        conteneur.innerHTML = annonces.map(a => `
          <div class="annonce-card">
            <span class="annonce-date">${a.date}</span>
            <h4 class="annonce-titre">${a.titre}</h4>
            <p class="annonce-texte">${a.texte}</p>
          </div>
        `).join('');
      } catch (err) {
        conteneur.innerHTML = '<p class="annonces-vides">Annonces temporairement indisponibles.</p>';
      }
    }

    chargerAnnonces();
  </script>
  ```

- [ ] **Step 4 : Vérifier dans le navigateur**

  Ouvrir `index.html` et scroller jusqu'à la section "Actualités & Annonces".

  **Résultat attendu :**
  - La colonne gauche affiche les 2 annonces de test créées à la Task 3
  - La colonne droite affiche "Chargement..." ou un cadre vide (le Facebook Plugin ne charge pas en local — c'est normal)

  > Si les annonces affichent "Annonces temporairement indisponibles", c'est une restriction CORS du navigateur en mode local. Elles fonctionneront correctement sur Netlify. Pour tester en local, voir la note en bas du plan.

---

## Task 5 : Intégrer le SDK Facebook Page Plugin

**Files:**
- Modify: `index.html` — ajouter le SDK Facebook dans `<head>` + `fb-root` dans `<body>`

- [ ] **Step 1 : Ajouter le div `fb-root` en début de body**

  Dans `index.html`, trouver la balise `<body>` (ou la première ligne après `</style></head>`).
  Juste après l'ouverture de `<body>`, ajouter :

  ```html
  <div id="fb-root"></div>
  ```

- [ ] **Step 2 : Ajouter le script SDK Facebook dans `<head>`**

  Dans `index.html`, dans la balise `<head>`, après les balises `<link>` existantes (polices Google), ajouter :

  ```html
  <script async defer crossorigin="anonymous"
    src="https://connect.facebook.net/fr_FR/sdk.js#xfbml=1&version=v18.0">
  </script>
  ```

- [ ] **Step 3 : Vérifier dans le navigateur (après déploiement Netlify)**

  Après déploiement sur Netlify (Task 7), ouvrir la page et scroller jusqu'à "Publications Facebook".

  **Résultat attendu :** Le widget Facebook affiche les publications de la page Ibn Rachik Ouardanine avec photos et textes.

---

## Task 6 : Mettre à jour la navigation

**Files:**
- Modify: `index.html` lignes 888–894 (nav) et 1347–1352 (footer)

- [ ] **Step 1 : Ajouter le lien "Actualités" dans la navigation principale**

  Trouver dans `index.html` (ligne ~889) :
  ```html
  <li><a href="#about">À propos</a></li>
  <li><a href="#social">Réseaux</a></li>
  ```

  Remplacer par :
  ```html
  <li><a href="#about">À propos</a></li>
  <li><a href="#actualites">Actualités</a></li>
  <li><a href="#social">Réseaux</a></li>
  ```

- [ ] **Step 2 : Ajouter le lien "Actualités" dans le footer**

  Trouver dans `index.html` (ligne ~1347) :
  ```html
  <li><a href="#about">À propos</a></li>
  <li><a href="#social">Réseaux</a></li>
  ```

  Remplacer par :
  ```html
  <li><a href="#about">À propos</a></li>
  <li><a href="#actualites">Actualités</a></li>
  <li><a href="#social">Réseaux</a></li>
  ```

- [ ] **Step 3 : Vérifier dans le navigateur**

  Ouvrir `index.html` → vérifier que la barre de navigation contient maintenant "Actualités" entre "À propos" et "Réseaux". Cliquer dessus → la page doit scroller jusqu'à la section Actualités.

---

## Task 7 : Déployer sur Netlify

> Étape manuelle — aucun code.

- [ ] **Step 1 : Créer un compte Netlify**

  Aller sur https://netlify.com → cliquer "Sign up" → s'inscrire (gratuit, pas de carte bancaire).

- [ ] **Step 2 : Déployer le site**

  1. Dans le dashboard Netlify, aller sur l'onglet **"Sites"**
  2. Faire glisser-déposer le **dossier `Ecole_Ibn_Rachik`** entier dans la zone "Drag and drop your site folder here"
  3. Netlify génère automatiquement une URL du type : `https://joyful-fox-abc123.netlify.app`

- [ ] **Step 3 : Personnaliser l'URL**

  Dans le dashboard du site → **"Site settings"** → **"Change site name"** → écrire `ecole-ibn-rachik` → sauvegarder.
  L'URL devient : `https://ecole-ibn-rachik.netlify.app`

- [ ] **Step 4 : Tester toutes les fonctionnalités**

  Ouvrir `https://ecole-ibn-rachik.netlify.app` dans un navigateur et vérifier :

  - [ ] Le formulaire de contact envoie bien un email (tester avec un vrai message)
  - [ ] Les annonces du Google Sheets s'affichent correctement
  - [ ] Le widget Facebook affiche les publications de la page
  - [ ] Le lien "Actualités" dans le nav fonctionne
  - [ ] Le site est lisible sur mobile (redimensionner la fenêtre)

- [ ] **Step 5 : Procédure de mise à jour future**

  Pour toute future modification du site :
  1. Modifier `index.html` sur l'ordinateur
  2. Retourner sur https://netlify.com → dashboard du site
  3. Aller sur **"Deploys"** → glisser-déposer à nouveau le dossier
  4. Le site se met à jour en ~30 secondes

---

## Note : Tester le formulaire en local (optionnel)

Si vous souhaitez tester le formulaire sans déployer, vous pouvez utiliser un serveur local simple :

**Option 1 — VS Code** : Installer l'extension "Live Server" → clic droit sur `index.html` → "Open with Live Server"

**Option 2 — Python** (si installé) : Ouvrir un terminal dans le dossier du projet et taper :
```
python -m http.server 8000
```
Puis ouvrir `http://localhost:8000` dans le navigateur.

---

## Résumé des valeurs à remplacer

| Valeur | Où la trouver | Où l'insérer dans index.html |
|--------|--------------|------------------------------|
| `VOTRE_FORM_ID` | Dashboard Formspree → votre formulaire → l'URL `/f/xxxxx` | Task 2, attribut `action` du `<form>` |
| `VOTRE_URL_GOOGLE_SHEETS` | Google Sheets → Fichier → Publier sur le web → URL générée | Task 4 Step 2 (HTML) et Task 4 Step 3 (JavaScript) |
