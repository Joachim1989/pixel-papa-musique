# 🎵 Pixel Papa — Musique

> **Studio tout-en-un de synchronisation audio, sous-titrage karaoké, animations et export vidéo.**  
> Application web monopage (*single-file*), 100% autonome, sans dépendance serveur, hébergeable gratuitement sur **GitHub Pages**.

---

## ✨ Fonctionnalités

- ⏱️ **Calage Audio Précis** : Synchronisation ligne par ligne des paroles / sous-titres avec raccourcis clavier intuitifs (Espace, Enter, Flèches).
- 🎤 **Karaoké Dynamique** : Rendu visuel temps réel avec gestion du glow, de la typographie et des animations.
- 🎬 **Animations & Marqueurs** : Insertion de marqueurs visuels calés sur le rythme musical.
- 🚀 **Pack SEO YouTube** : Génération instantanée des titres optimisés, descriptions structurées avec chapitrage timecodé et tags.
- 📹 **Export Vidéo Direct** : Enregistrement vidéo via l'API MediaRecorder avec suivi de progression en pourcentage en temps réel.
- 💾 **Sauvegarde Automatique Réactive** : Architecture réactive basée sur JavaScript `Proxy` et `localStorage` (aucun risque de perte de travail).

---

## 🚀 Déploiement & Utilisation

### Option 1 : Directement dans votre navigateur (Local)
Double-cliquez simplement sur `index.html` dans Chrome, Edge, Firefox ou Safari. Aucun serveur ni installation Node.js nécessaire.

### Option 2 : En ligne via GitHub Pages (Recommandé)
Publication automatique par le workflow `.github/workflows/pages.yml` à chaque push sur `main` :
seul `index.html` est mis en ligne (les notes internes de `docs/` et `GEMINI.md` ne sont plus servies).
Réglage unique : **Settings** > **Pages** > Source : **GitHub Actions**.
L'application est accessible sur `https://joachim1989.github.io/pixel-papa-musique/`.

---

## 🛠️ Architecture Technique

- **Fichier unique** : Tout est encapsulé dans `index.html` (HTML5, CSS3, JavaScript Vanilla).
- **Moteur réactif** : `state` enveloppé dans un `Proxy` qui intercepte automatiquement les mutations et déclenche la mise à jour sélective du DOM (`updateLineDOM`) et la persistance (`draftSave`).
- **Performance** : Zéro réécriture brute du DOM (`box.innerHTML = ''` banni lors des modifications de timecodes).
- **Gestion de mémoire** :
  - `docs/MEMOIRE.md` : Journal des modifications, règles d'architecture et roadmap.
  - `docs/HISTORIQUE.md` : Choix d'architecture et roadmap repris de la v1 (`pixel-papa-music`, archivé).
  - `docs/test_audit.html` : suite de tests manuelle (servir le dépôt en local, ouvrir `docs/test_audit.html`).
  - `GEMINI.md` : Guide de contexte et instructions strictes pour les agents IA et LLM intervenant sur le code (reste à la racine, lu par l'agent).
- **Sécurité** : toute donnée importée (projets `.pixelpapa`, `.character.json`, paroles, réponses Gemini) passe par `escHtml()` / `safeImgSrc()` avant `innerHTML` ; CSP en `<meta>` limitée à Google Fonts, Gemini et Pollinations.

---

## 👤 Auteur

**Pixel Papa** — Joachim Janowski  
Site / Projets : [Pixel Papa](https://pixelpapa.com)
