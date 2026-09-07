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
1. Activez **GitHub Pages** dans les paramètres du dépôt :
   - Rendez-vous dans **Settings** > **Pages**
   - Source : **Deploy from a branch**
   - Branch : `main` / `(root)`
   - Cliquez sur **Save**
2. Votre application est immédiatement accessible en ligne sur :  
   `https://<votre-compte>.github.io/pixel-papa-musique/`

---

## 🛠️ Architecture Technique

- **Fichier unique** : Tout est encapsulé dans `index.html` (HTML5, CSS3, JavaScript Vanilla).
- **Moteur réactif** : `state` enveloppé dans un `Proxy` qui intercepte automatiquement les mutations et déclenche la mise à jour sélective du DOM (`updateLineDOM`) et la persistance (`draftSave`).
- **Performance** : Zéro réécriture brute du DOM (`box.innerHTML = ''` banni lors des modifications de timecodes).
- **Gestion de mémoire** :
  - `MEMOIRE.md` : Journal des modifications, règles d'architecture et roadmap.
  - `GEMINI.md` : Guide de contexte et instructions strictes pour les agents IA et LLM intervenant sur le code.

---

## 👤 Auteur

**Pixel Papa** — Joachim Janowski  
Site / Projets : [Pixel Papa](https://pixelpapa.com)
