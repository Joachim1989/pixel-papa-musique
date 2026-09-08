# 🎵 Pixel Papa — Mémoire du Projet & Suivi des Modifications

Ce fichier sert de **carnet de bord, mémoire technique et feuille de route** pour l'application mono-page `index.html` (« Pixel Papa — Musique »).
Il permet de conserver l'historique de ce qui a été fait, les principes d'architecture à respecter, et de lister les prochaines modifications à réaliser ("À avoir").

---

## 📋 1. Backlog & Modifications à venir ("À avoir")
*Notez ici toutes les futures idées, ajustements de design, fonctionnalités ou corrections à implémenter.*

### 🚀 Fonctionnalités prévues / En réflexion
- [ ] **Découpage audio automatique par section** : Extraire et télécharger les segments audio correspondant à chaque [Couplet] / [Refrain].
- [ ] **Gestionnaire de styles visuels sauvegardés** : Pouvoir sauvegarder et basculer entre plusieurs fiches de style (ex. Rétro Chiptune, Cyberpunk, Aquarelle, etc.).
- [x] **Export vidéo multi-résolutions & Haute Fidélité (v2.3)** : Choix 1080p Full HD (1920x1080, 1080x1920, 1080x1080) vs 720p, débit jusqu'à 10 Mbps et 60 FPS.
- [x] **Multiplication des scènes & Cadence configurable (v2.3)** : Rythmes Standard (1 plan/section), Dynamique (2 plans/section) et Rythme Clip (1 plan / 2-3 phrases).
- [x] **Éclairage cinématographique & Cadrages de réalisateur (v2.3)** : Présets d'éclairage volumétrique, golden hour 35mm, clair-obscur et cadrages alternés (plongée, contre-plongée, plan large, gros plan).
- [x] **Grain argentique 35mm & Color Grading (v2.3)** : Grain procédural 60 FPS ultra-léger et étalonnage non destructif (Teal & Orange, Doré, Cyber, Noir & Blanc).
- [ ] **Historique d'annulation (Undo / Redo)** : Raccourci `Ctrl+Z` pour annuler le dernier pointage de timecode.
- [ ] *(Ajoutez vos prochaines idées ici...)*

### 🎨 Améliorations de confort & Ergonomie
- [ ] Thèmes de couleur d'interface (Mode sombre profond, contraste élevé).
- [ ] Détection automatique du tempo (BPM) pour assister le calage mot à mot.

---

## 🏛️ 2. Règles d'Architecture & Principes Techniques
*Ces règles doivent être respectées lors de chaque future intervention sur `index.html`.*

1. **Fichier unique & Zéro dépendance** :
   - Tout doit rester dans un seul fichier `index.html`, sans étape de build, sans `npm`, ni serveur requis (ouvrable en double-clic).
2. **State réactif via Proxy (`state`)** :
   - Ne jamais faire d'appels manuels dispersés à `draftSave()` ou `render()` dans les EventListeners.
   - Toute modification de propriété sur `state` ou `state.lines[i]` doit automatiquement déclencher les mises à jour d'interface ciblées et la persistance.
3. **Rendu DOM chirurgical (`updateLineDOM`)** :
   - Ne jamais écraser `#lignes.innerHTML = ''` lors d'un simple changement de timecode ou de sélection de ligne.
   - Utiliser `updateLineDOM(idx)` pour cibler uniquement `.ligne[data-idx="..."]`.
4. **Classes utilitaires CSS dans `<style>`** :
   - Ne pas ajouter d'attributs `style="..."` dans les balises HTML. Utiliser les classes utilitaires existantes (`.card-md`, `.card-sm`, `.textarea-sm`, `.w-full`, etc.).
5. **APIs modernes & Standard Web** :
   - Utiliser `navigator.clipboard.writeText` sans fallback déprécié (`document.execCommand`).
   - Utiliser `MediaRecorder` et `canvas.captureStream` pour l'export vidéo temps réel.

---

## 📜 3. Historique des Versions & Modifications (Changelog)

### [v2.4 — 2026-09-08] — Refonte Moteur Caméra 2.5D, Angles Réalisateur par Scène & Consignes Personnage
- **motion-camera-25d-overhaul** :
  - Élimination complète de la vibration trigonométrique à 48 rad/s (7.6 Hz) qui donnait un tremblement parasite et masquait les mouvements cinématiques.
  - Remplacement par une pulsation de basse musicale propre (`pulseZoom`), fluide et unidirectionnelle (`kickDisplacement` vertical amorti).
  - Ampleur visuelle décuplée : augmentation de l'échelle de zoom de base (`1.15` à `1.45`) offrant une marge de 200 à 350px pour des travellings amples, nets et continus (120-250px de déplacement au lieu de quelques pixels imperceptibles).
  - Nouveaux profils de mouvements :
    - *Auto* : harmonisé en temps réel avec l'angle de cadrage de chaque scène du storyboard (panoramique sur plan large, élévation sur contre-plongée, descente sur plongée, zoom intime sur gros plan).
    - *Cinématique* : travellings gauche-droite / droite-gauche alternés à chaque coupe.
    - *Punch & Zoom* : crash zoom énergique sur le sujet.
    - *Dézoom révélateur (Pull-out)* : recul progressif pour dévoiler le décor complet.
    - *Angle hollandais (Dutch Tilt)* : inclinaison 2.5° à 4° (0.055 rad) sans bord noir ni distorsion.
    - *Caméra portée* : balancement organique steadicam lissé à basse fréquence (0.25 - 0.4 Hz).
  - Nouveau sélecteur d'ampleur dans l'Aperçu : **Dynamique** (100% ampleur), **Cinéma** (65%), **Subtil** (35%).
  - Élimination des écrans noirs en début (intro avant 1er marqueur) et fin (outro audio) dans `fenetreSectionActuelle`.
  - Durée de fondu enchaîné de transition portée à 0.8s pour des coupes cinématiques douces.
  - Renforcement visuel des Color Grades (*Teal & Orange*, *Golden Hour*, *Cyber Synthwave*, *Film Noir N&B*) en mode overlay/soft-light.
- **angles-camera-storyboard** :
  - Définition de 7 angles de caméra réels avec terminologie cinématographique (`wide`, `medium`, `low_angle`, `closeup`, `high_angle`, `dutch`, `three_quarter`).
  - Sélecteur d'angle par scène (`<select class="select-sm">`) intégré sur chaque carte de storyboard dans l'Étape 1 du plan.
  - Injection d'une directive de cadrage stricte en tête de prompt Imagen (`[CADRAGE CINÉMA STRICT : ...]\n\n`).
  - Mémorisation de l'angle choisi sur chaque élément de storyboard (`item.angleKey`) pour lier le moteur 2.5D au cadrage généré.
- **character-notes-consignes** :
  - Ajout d'un champ texte dédié (`#characterNotes`) dans la carte Personnage (onglet 7) pour saisir les consignes spécifiques (âge, vêtements, barbe, accessoires, expression, personnalité).
  - Intégration réactive dans `state.characterNotes` et persistance automatique dans le brouillon local `localStorage`.
  - Injection automatique des consignes dans la génération de la Character Sheet (`promptCharacterSheet`), dans le chaînage visuel (`buildImageParts`), et dans chaque scène du storyboard (`promptSceneStoryboard`).

### [v2.3.1 — 2026-09-08] — Correctifs de stabilité : Pré-calage IA & Character Sheet
- **fix-precalage-ai-tofixed** : Sécurisation de `buildAnimJSON` pour tolérer les timecodes partiels sans déclencher `null.toFixed(3)`. Écriture par lot sur `_raw` dans `btnAI.onclick` et synchronisation de `recomputeEnds()` dans le setter du Proxy `state`.
- **fix-visuels-charactersheet-dom** : Restauration des balises DOM de la planche de référence de personnage (`dropzoneRef`, `fileRef`, `refGallery`, `btnCharacterSheet`, `refStatus`, `characterSheetResult`) dans l'onglet Visuels et sécurisation contre les accès nuls.

### [v2.3 — 2026-09-08] — Studio Réalisateur & Master Vidéo 1080p 60fps (Chantiers 1, 2, 3, 4)
- **chantier-1-video-master** :
  - Support de l'exportation en **1080p Full HD** (`1920x1080` paysage, `1080x1920` vertical Shorts/TikTok/Reels, `1080x1080` carré) avec repli vers le 720p HD.
  - Débit d'encodage paramétrable jusqu'à **10 Mbps** (`videoBitsPerSecond`) pour éliminer tout macro-bloc ou artefact de compression vidéo.
  - Fluidité d'enregistrement sélectionnable à **60 FPS** (ultra-fluide) ou 30 FPS.
- **chantier-2-cadence-multi-scenes** :
  - Sélecteur de cadence créative dans l'onglet Visuels : **Dynamique** (2 plans / section pour varier l'action), **Standard** (1 plan / section), ou **Rythme Clip** (1 plan toutes les 2-3 répliques).
  - Découpage automatique des fenêtres temporelles (`debut`, `fin`) pour synchroniser parfaitement chaque plan à l'écoute et dans l'export.
  - Synchronisation en direct de `fenetreSectionActuelle(instant)` et `indexSectionEnCours(t)` sur le découpage multi-plans.
- **chantier-3-prompts-cinema** :
  - Intégration de présets d'éclairage photoréalistes dans les prompts Imagen : *Néons & Volumétrique (Arcade/Cyber)*, *Golden Hour 35mm*, *Clair-obscur Dramatique*, *Lumière naturelle diffuse*.
  - Règle de cadrage directeur alternée à chaque scène (Plan large immersif, Contre-plongée dynamique, Gros plan dramatique, Plongée trois-quarts, Composition règle des tiers).
- **chantier-4-compositing-etalonnage** :
  - **Grain argentique 35mm procédural** : texture de bruit générée hors écran une seule fois et répétée sous mode de fusion `overlay` avec translation pseudo-aléatoire (60 FPS, zéro impact CPU).
  - **Color Grading cinématique non destructif** : étalonnage appliqué avant le dessin des sous-titres (*Teal & Orange Hollywood*, *Chaleur Dorée Pixel Papa*, *Cyber Neon*, *Film Noir N&B*).
  - Sous-titres positionnés au-dessus du grain et de l'étalonnage pour conserver une netteté et un contraste impeccables.
- **reactive-controls-ui** :
  - Intégration de 7 nouveaux contrôles réactifs dans le Proxy `state` avec sélecteurs stylisés dans les onglets 7 et 8.

### [v2.2 — 2026-09-08] — Moteur Motion 2.5D, Audio-Réactif & Prévisualisation Temps Réel (Approche A)
- **audio-reactive-bass** : Intégration de la Web Audio API (`AudioContext` + `AnalyserNode`) pour extraire l'énergie spectrale des basses (40-160Hz) avec amortissement musical (*decay*). L'image pulse subtilement sur les kicks.
- **motion-2.5d-camera** : Nouveau moteur de caméra dynamique (`dessinerImageAnimee25D`) supportant 4 styles créatifs pour le monteur (Auto/Alterné, Cinématique Travelling, Punch & Zoom, Caméra Portée organique) avec micro-tilts et balancier 2.5D.
- **atmospheric-overlays** : Système de particules légères en suspension (poussières dorées) et vignette respirante / fuite de lumière chaude calée sur le son.
- **previz-canvas-60fps** : Refonte de l'onglet 8 (Aperçu) avec un véritable Canvas 60 FPS animé en direct pendant la lecture audio, boutons de transport (Play/Pause, -5s, +5s) et console de réglages créatifs en direct.
- **unified-video-export** : L'export vidéo WebM exploite désormais exactement le même compositeur (`dessinerFrameComposite`), garantissant que la vidéo finale soit identique au rendu de prévisualisation.

### [v2.1 — 2026-09-07] — Refactoring majeur : Performance DOM, State Proxy & Nettoyage
- **opt-dom-render** : Refonte complète de `render()` avec mise à jour chirurgicale via `updateLineDOM(idx)` et `updateSelectionDOM(newIdx, oldIdx)`. Élimination du scintillement et des re-créations intégrales de la liste `#lignes`.
- **opt-state-proxy** : Migration vers un `Proxy` JavaScript réactif (`state` et `makeLineReactive`). Déclenchement automatique de `draftSave()` et suppression des dizaines d'appels manuels dans les écouteurs.
- **ui-css-cleanup** : Remplacement de 100% des styles inline du balisage HTML par des classes utilitaires propres dans `<style>`.
- **ux-video-progress** : Ajout d'un indicateur de progression en temps réel (`#exportVideoProgress`) affichant le pourcentage (`⏳ X%`) à chaque frame de la boucle d'export vidéo.
- **clean-tab-0** : Suppression complète du générateur de premier jet obsolète (onglet 0) et de tout son code JS (`GENERATION_SCHEMA`, `promptGenerationTexte`, etc.).
- **clean-copy-fallback** : Suppression définitive de l'API dépréciée `document.execCommand('copy')` dans `copyBtn()`.

### [v2.0 — 2026-09-06] — Système de personnages multiples & Mouvements caméra
- Ajout du système multi-personnages avec character sheets indépendantes dans l'onglet Visuels (onglet 7).
- Découplage strict entre les images de référence du personnage et la référence de style visuel.
- Ajout de mouvements de caméra alternés (travellings directionnels, dézoom, zoom progressif) pour l'export vidéo.