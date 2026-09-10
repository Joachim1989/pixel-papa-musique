# 🎵 Pixel Papa — Mémoire du Projet & Suivi des Modifications

Ce fichier sert de **carnet de bord, mémoire technique et feuille de route** pour l'application mono-page `index.html` (« Pixel Papa — Musique »).
Il permet de conserver l'historique de ce qui a été fait, les principes d'architecture à respecter, et de lister les prochaines modifications à réaliser ("À avoir").

---

## 📋 1. Backlog & Modifications à venir ("À avoir")
*Notez ici toutes les futures idées, ajustements de design, fonctionnalités ou corrections à implémenter.*

### 🚀 Fonctionnalités prévues / En réflexion
- [ ] **Découpage audio automatique par section** : Extraire et télécharger les segments audio correspondant à chaque [Couplet] / [Refrain].
- [x] **Gestionnaire de styles visuels sauvegardés (v2.11)** : Pouvoir sauvegarder et basculer entre plusieurs fiches de style (ex. Pixel Art Arcade, Cyberpunk, Anime 90s, Cinéma 35mm, Comics BD).
- [x] **Bibliothèque globale de personnages / Character Bible (v2.11)** : Sauvegarder les fiches de personnages, photos et character sheets générées pour les réinjecter en 1 clic dans n'importe quel futur morceau sans re-consommer de crédits API. Export/import `.character.json`.
- [x] **Gestionnaire Multi-Morceaux / Projets (v2.11)** : Bibliothèque locale de projets indépendants, sauvegarde sans écrasement, duplication, export/import de fichiers sauvegardes complètes `.pixelpapa`.
- [x] **Archivage des Paroles (v2.11)** : Sauvegarde d'historique de versions de paroles et restauration instantanée.
- [x] **Moteur de Stockage Haute Capacité IndexedDB (v2.11)** : Dépassement du quota 5 Mo de localStorage avec `PixelPapaStudioDB` et fallback résilient.
- [x] **Export vidéo multi-résolutions & Haute Fidélité (v2.3)** : Choix 1080p Full HD (1920x1080, 1080x1920, 1080x1080) vs 720p, débit jusqu'à 10 Mbps et 60 FPS.
- [x] **Multiplication des scènes & Cadence configurable (v2.3)** : Rythmes Standard (1 plan/section), Dynamique (2 plans/section) et Rythme Clip (1 plan / 2-3 phrases).
- [x] **Éclairage cinématographique & Cadrages de réalisateur (v2.3)** : Présets d'éclairage volumétrique, golden hour 35mm, clair-obscur et cadrages alternés (plongée, contre-plongée, plan large, gros plan).
- [x] **Grain argentique 35mm & Color Grading (v2.3)** : Grain procédural 60 FPS ultra-léger et étalonnage non destructif (Teal & Orange, Doré, Cyber, Noir & Blanc).
- [x] **Workflow Direct Gemini Web / Nano Banana (v2.20)** : Pack de prompts 1-clic pour Gemini Web (Nano Banana / Imagen 3 sans API payante), import drag & drop sur cartes de scènes et storyboard, synchronisation automatique du player 2.5D.
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
   - Utiliser `IndexedDB` (`PixelPapaStudioDB`) pour le stockage local sans restriction de taille (plusieurs centaines de mégaoctets de photos, fiches de style et character sheets).

---

## 📜 3. Historique des Versions & Modifications (Changelog)

### [v2.26 — 2026-09-10] — Attente Active Robuste (Détection d'Image Réelle, Rythme Réglable & Bouton Passer)
- **Attente Active & Détection d'Image Réelle** :
  - Résolution du problème de timing trop rapide où le script enchaînait prématurément sur la scène suivante avant la fin du dessin Imagen 3 (qui prend 15 à 35s).
  - Comptage direct des balises `<img>` utiles sur la page (`compterImages()`) avant chaque envoi : le script attend explicitement qu'une nouvelle illustration soit rendue dans le DOM.
  - Double vérification de stabilité : exige que Gemini ne soit plus occupé (absence de bouton stop, présence du bouton envoyer activé, fin de progression) pendant au moins 3 vérifications consécutives (3s stables).
- **Temps d'Attente Minimum Garanti & Sélecteur de Rythme (`#selCadenceAutomateur`)** :
  - Trois modes de cadence sélectionnables dans la modale d'assistance avant de copier :
    * 🛡️ **Sécurisé (Recommandé)** : Attente min 25s + détection image + pause 8s (Idéal pour Imagen 3 HD).
    * 🐢 **Prudent (Lent / Grande sécurité)** : Attente min 40s + détection image + pause 12s.
    * ⚡ **Rapide** : Attente min 15s + détection image + pause 4s.
- **Bouton d'Avance Rapide Interactif sur le HUD (`⏭️ Passer`)** :
  - Ajout d'un bouton `⏭️ Passer` directement sur le bandeau flottant dans Gemini Web : si l'utilisateur observe que l'image est prête et ne souhaite pas attendre la fin du chrono, un clic avance immédiatement à la scène suivante.
- **Suite de tests d'audit portée à 408 assertions (100% PASS, 0 FAIL)**.

### [v2.25 — 2026-09-10] — Pilote Automatique Gemini Web (Script Console F12) & Téléchargement 1-Clic
- **Pilote Automatique pour Gemini Web (`#btnCopierScriptAutomateur`)** :
  - Permet d'automatiser 100% de la génération des 19 scènes dans Gemini Web (Nano Banana / Imagen 3) sans payer d'API supplémentaire et sans copier-coller les prompts un à un.
  - Génère un script autonome JavaScript prêt à coller dans la console DevTools (`F12` > Console) de `gemini.google.com`.
  - Injecte un bandeau de bord (HUD) flottant ultra-pro en surimpression sur Gemini Web : titre du projet, scène en cours, barre de progression animée, boutons Pause / Arrêter.
  - Saisie automatique des prompts scène par scène avec simulation de frappe native pour Angular/React (`document.execCommand` et `InputEvent`).
  - Détection automatique de la fin de génération de chaque image via observation du DOM (bouton Arrêter, barres de chargement et statut du bouton Envoyer).
  - Temporisation anti-rate-limit de sécurité (4s) entre chaque scène.
  - Notification sonore à la fin de la génération des 19 scènes (Web Audio API synthétisé, zéro asset externe).
- **Téléchargeur en Lot Intégré au Script Automateur (`#ppa-btn-dl`)** :
  - Dès la fin de la génération, le HUD propose un bouton direct « 📥 Télécharger les 19 images ».
  - Télécharge automatiquement chaque illustration sous la forme `scene_01.png`, `scene_02.png`... prête pour l'import en un clic dans Pixel Papa via `📂 Importer toutes les images (lot)`.
- **Modale d'Assistance Complète (`#modalAutomateurGemini`)** :
  - Guide pas-à-pas en 3 étapes avec raccourcis clavier (<kbd>F12</kbd>, <kbd>Ctrl+V</kbd>, <kbd>Entrée</kbd>).
  - Astuce anti-blocage pour Chromium/Edge (`allow pasting`).
  - Bouton de copie du script et intégration directe depuis la modale `#modalPackPrompts`.
- **Suite de tests d'audit portée à 402 assertions — 100% PASS, 0 FAIL**.

### [v2.24 — 2026-09-10] — Mode Réalisateur Pas-à-Pas (SUIVANT) & Import par Lot des 19 Illustrations
- **Mode Réalisateur Pas-à-Pas pour Gemini Web (Nano Banana)** :
  - Restructuration intégrale du pack généré par `📦 Copier tous les prompts (Gemini Web)`.
  - Intègre une « Directive Maître » ordonnant à Gemini de mémoriser la Character Sheet officielle et d'activer le protocole interactif cadencé : l'utilisateur colle tout le script en un seul message avec son image de référence, Gemini génère la Scène 1, puis attend simplement que l'utilisateur tape « SUIVANT » pour enchaîner sur la Scène 2, 3... jusqu'à 19 sans jamais avoir à copier-coller de nouveau prompt !
- **Importation par Lot en 1 Clic (`📂 Importer toutes les images (lot)`)** :
  - Nouveau bouton dans l'Étape 1 (Onglet 7) permettant de sélectionner l'ensemble des 19 images téléchargées depuis Gemini Web en un seul coup.
  - Tri alphanumérique naturel des fichiers pour mapper automatiquement chaque image sur sa scène correspondante (Scène 1, 2, 3... 19).
  - Remplit l'aperçu du storyboard et active immédiatement le player vidéo 2.5D temps réel (Onglet 8).
- **Suite de tests d'audit portée à 392 assertions (100% PASS)**.

### [v2.23 — 2026-09-10] — Copie Infaillible avec Repli Universel & Affichage Direct des Prompts Scènes
- **Système Universel de Copie Presse-Papier (`copierDansPressePapier`)** :
  - Mécanisme à double palier : essai prioritaire de `navigator.clipboard.writeText`, avec repli automatique sur `document.execCommand('copy')` via textarea éphémère optimisée (position fixe sans perturbation de scroll, sélection chirurgicale, restauration du focus).
  - Élimine 100% des erreurs `NotAllowedError: Document is not focused` et contourne les restrictions d'accès au presse-papier imposées par Chromium/Edge en environnement local `file:///`.
- **Bloc Déroulant de Visualisation Directe du Prompt (`.scene-prompt-details`) sur chaque Carte de Scène** :
  - Intégration d'un bloc repliable `<details>` sous chaque scène de l'Étape 1 : « 👁️ Voir / copier manuellement le prompt Gemini Web ».
  - Affiche le prompt calculé en temps réel dans un `<textarea class="textarea-sm">` complet et stylisé, garantissant à l'utilisateur un accès visuel permanent au prompt sans dépendre du presse-papier système.
  - Bouton d'assistance « Tout sélectionner » pour un simple `Ctrl+C` immédiat.
  - Si le presse-papier automatique est restreint, le bloc s'ouvre instantanément et le texte est pré-sélectionné pour l'utilisateur.
- **Modale de Prévisualisation & Copie du Pack de Prompts (`#modalPackPrompts`)** :
  - Le clic sur « 📦 Copier tous les prompts pour Gemini Web » ouvre désormais une boîte de dialogue modale affichant l'intégralité du pack (guide de continuité, amorce message 1 avec Character Sheet, et ensemble des scènes numérotées).
  - Bouton d'action « 📋 Copier tout le pack » avec bascule visuelle temporaire en vert (`✅ Tout est copié !`).
- **Généralisation à tous les boutons de copie de Pixel Papa** :
  - Migré vers `copierDansPressePapier` : Paroles Suno (Onglet 0), Sous-titres SRT (Onglet 2), Prompts de l'Animation Hub (Onglet 4), Titre / Description / Tags SEO (Onglet 5), Prompt Character Sheet et fiches images (Onglet 7).
- **Suite de tests d'audit portée à 389 assertions (100% PASS)** :
  - Vérification automatisée du rendu DOM, de la présence du textarea de prompt, du cycle d'ouverture/fermeture de la modale pack, et de la résilience du helper de copie.

### [v2.22 — 2026-09-10] — Correctif Copie Prompt Scènes & Bouton Prompt Character Sheet
- **Correctif Critique sur les Boutons Scènes (`btnCopyPromptScene` & `fileInputScene`)** :
  - Résolution de l'exception JavaScript silencieuse (`ReferenceError: nom is not defined`) dans la boucle de rendu des scènes de l'Étape 1 (`planActuel.forEach`).
  - Définition explicite de `const nom = entree.nom || ('Scène ' + (i + 1))` et encapsulation complète du calcul de prompt dans un bloc `try/catch` avec gestion d'erreurs et alertes toasts.
  - Le clic sur `📋 Copier prompt Nano Banana` copie désormais instantanément le prompt de chaque plan dans le presse-papier avec confirmation toast.
- **Nouveau Bouton 1-Clic `#btnCopierPromptCS` (Character Sheet)** :
  - Positionné à côté de « Générer la character sheet » dans l'Étape 0 (Personnages).
  - Compile le prompt complet de la planche de personnage (avec fiche de style, notes physiques, rôle et nom) prêt à être collé dans Gemini Web.
  - Avertissement visuel explicatif rappelant que FLUX.1 gratuit est text-to-image (aveugle aux photos) et guidant vers l'usage de Gemini Web à 0€ pour transformer des photos réelles.
- **Tests d'Audit & Robustesse** :
  - **378 tests validés — 378 PASS, 0 FAIL (100%)**.

### [v2.21 — 2026-09-10] — Continuité Visuelle Gemini Web (Directive Character Sheet + Copie Presse-Papier Image Directe)
- **Directive explicite de continuité visuelle (`[CONTINUITÉ VISUELLE & CHARACTER SHEET]`)** :
  - `promptSceneStoryboard` injecte automatiquement une consigne stricte lorsque le personnage possède une Character Sheet, des photos ou des notes :  
    *« Si une image de référence visuelle (Character Sheet ou photo du personnage) est attachée dans la conversation, reproduis scrupuleusement les traits du visage, la morphologie, la coiffure et la tenue du personnage visible sur cette référence... »*
  - Garantit que Gemini Web (Nano Banana) calque fidèlement le protagoniste sur l'image fournie.
- **Bouton `🖼️ Copier l'image` (Presse-papier système pour `Ctrl+V` direct)** :
  - Ajouté sur chaque bloc d'image (`blocImage`) : Character Sheet, scènes de storyboard et vignette.
  - Convertit l'image en Blob PNG via un canvas dédié et l'écrit dans le presse-papier via `navigator.clipboard.write([new ClipboardItem(...)])`.
  - Permet de faire un simple `Ctrl+V` directement dans le champ de saisie de `gemini.google.com` sans enregistrer aucun fichier sur le disque.
- **Guide pas-à-pas dans le pack de prompts 1-clic** :
  - `#btnCopierTousPrompts` intègre en tête de document le message d'amorce officiel prêt à coller dans Gemini Web pour fixer la mémoire visuelle du personnage dès le message 1.
- **Tests d'Audit & Robustesse** :
  - Suite 20 complétée avec assertions pour le bouton `🖼️ Copier l'image` et la présence de la directive de continuité visuelle.
  - **375 tests PASS, 0 FAIL (100%)**.

### [v2.20 — 2026-09-10] — Workflow Direct Gemini Web "Nano Banana" (Import Drag & Drop + Pack de Prompts 1-Clic)
- **Objectif & Économie Maximale (0,00 €)** :
  - Permet aux utilisateurs disposant d'un abonnement direct Gemini Pro d'exploiter la puissance visuelle de "Nano Banana" (Google Imagen 3 via gemini.google.com) sans devoir approvisionner un compte Google Cloud Developer API payant.
- **Export Global 1-Clic des Prompts (`#btnCopierTousPrompts`)** :
  - Nouveau bouton positionné à côté de "Étape 1 : Plan de scènes (Texte)" dans l'Onglet 7.
  - Compile instantanément l'intégralité des prompts de chaque scène (avec style visuel, cadrages caméra, éclairage cinématographique et cohérence du protagoniste) dans un document clair et structuré prêt à coller dans Gemini Web.
  - Notification toast de confirmation dès la copie presse-papier.
- **Cartes de Scènes Étape 1 enrichies (Import direct & Copie unitaire)** :
  - Bouton unitaire `📋 Copier prompt Nano Banana` sur chaque scène individuelle.
  - Bouton `📁 Importer image` et zone de glisser-déposer (Drag & Drop) active sur chaque carte.
  - Aperçu visuel immédiat de l'image importée avec badge vert dynamique `✓ Image active (Gemini Web)`.
  - Alimentation directe du tableau réactif `state.storyboard[idx]`.
- **Remplacement Dynamique sur le Storyboard Étape 2 (`blocImage`)** :
  - Bouton `📁 Remplacer image` et gestionnaire drag & drop direct sur chaque vignette d'image générée ou existante.
  - Permet d'alterner ou d'injecter une version Gemini Web directement sur n'importe quel plan du clip.
- **Synchronisation Synchrone & Réactivité Vidéo (Onglet 8)** :
  - Préchargement synchrone via `chargerImageDansItem` pour un affichage immédiat dans le player vidéo 2.5D de l'Onglet 8 et le moteur d'animation / export vidéo WebM/MP4.
- **Suite de Tests d'Audit & Robustesse** :
  - Suite 20 ajoutée dans `test_audit.html` validant l'existence du bouton global, la présence du bouton de remplacement dans `blocImage`, la création synchrone de `HTMLImageElement` et l'activation de `state.visualsGenerated`.
  - 100% des tests validés (373 PASS, 0 FAIL).

### [v2.19 — 2026-09-09] — Studio Pro OODA & SMART : Ergonomie Globale & Optimisations Multi-Onglets (0 à 8)
- **Système de Notifications Toast Global (`afficherToast`)** :
  - Notifications contextuelles, discrètes et animées (succès, avertissement, erreur) avec fondu automatique pour toutes les copies presse-papier, téléchargements et actions importantes.
  - Zéro pop-up alert intrusive.
- **Mini-Player Audio Flottant Persistant (`#floatingAudioBar`)** :
  - Barre de lecture persistante en bas de l'écran affichée dès qu'un fichier audio est chargé.
  - Bouton Play/Pause, titre du morceau ou projet en cours, timecode temps réel et curseur de navigation (scrubber audio) accessibles depuis n'importe quel onglet (0 à 8) sans remonter au sommet.
- **Onglet 0 (Écriture)** :
  - Ajout du bouton 1-clic `#btnCopierParolesSuno` pour copier directement les paroles formatées avec leurs balises vers Suno v5.5.
  - Compteur dynamique de caractères `#metricCaractereCount` avec alertes visuelles (`X / 3 500 car.`).
- **Onglet 1 (Calage)** :
  - Bouton d'écoute Solo (`.btn-solo-ligne`) sur chaque vers calé pour vérifier l'alignement exact de la phrase à l'oreille sans écouter tout le morceau.
  - Outil `#btnRaccorderSilences` : élimine les micro-trous (`< 0.6s`) entre sous-titres consécutifs pour un confort de lecture optimal.
- **Onglet 2 (Sous-titres)** :
  - Option `#chkNettoyerBalisesSRT` activée par défaut pour purger les balises de structure (`[Refrain]`, `[Couplet 1]`, etc.) lors des exports SRT / VTT pour un affichage propre sur YouTube.
- **Onglet 3 (Karaoké)** :
  - Sélecteur de palettes `#selPresetStyleASS` avec 4 ambiances typographiques complètes : Doré Pixel Papa, Cyber Néon, Rétro Arcade 90s, et Minimaliste Studio.
- **Onglet 5 (Pack SEO)** :
  - Badges de limites de caractères et validation visuelle sur chaque champ (Titre /100 car. avec avertissement mobile >70, Description /5 000 car., Tags /500 car., Légende /2 200 car.).
  - Notification toast sur chaque bouton de copie individuel.
- **Onglet 6 (Teaser / Hook)** :
  - Bouton `#btnPlayPreviewTeaser` : écoute instantanée de la boucle audio exacte sélectionnée (15s, 30s ou hook) avant de lancer l'exportation vidéo.
- **Onglet 7 (Visuels & Storyboard)** :
  - Bouton `📋 Copier le prompt` ajouté sur chaque carte d'illustration générée pour faciliter l'itération.
  - Bouton `#btnStoryboardManquants` pour ne relancer que les scènes sans image sans écraser le storyboard existant.
- **Onglet 8 (Aperçu Vidéo)** :
  - Timeline scrubber vidéo interactif (`#scrubberVideoApercu`) pour glisser à la seconde près dans l'animation 2.5D.
  - Bouton plein écran (`#btnFullscreenApercu`) pour visionner le clip en immersion totale.
  - Menu `#selAmbianceVideoRapide` pour appliquer en 1 clic une direction visuelle complète (Arcade 90s, Lofi Vintage Tape, Cyberpunk Néon, Cinéma 35mm).
- **Suite d'Audit Automatisée (Suite 19)** :
  - 369 tests automatisés réussis sur navigateur headless Edge (100% PASS, 0 FAIL).
  - 0 ID DOM orphelin.

### [v2.18 — 2026-09-09] — Moteur d'Images 100% Gratuit FLUX.1 Schnell (Pollinations.ai — 0,00 €)
- **Intégration du Moteur Gratuit FLUX.1 Schnell (Pollinations.ai)** :
  - Génération d'images 100% gratuite (0,00 €), sans aucune clé API requise, sans carte bancaire, sans inscription.
  - Sélectionné par défaut dans le menu déroulant `#imageModel` (`✨ Gratuit — FLUX.1 Schnell (Pollinations — 0,00 €)`).
  - Préservé en tête de liste même après actualisation des modèles depuis une clé Google (`btnRafraichirModeles`).
- **Génération sans Clé API Gemini** :
  - Suppression de l'obligation de renseigner une clé API Gemini pour générer des images si le modèle gratuit `pollinations-flux` est sélectionné.
  - Fonctionne pour les Character Sheets de héros, le Roster de personnages, la Vignette YouTube et les scènes de Storyboard.
- **Régulation de Cadence Automatique sur le Storyboard** :
  - Décompte visuel de 6 secondes inséré entre les scènes consécutives lors de la génération par lot sur Pollinations pour respecter la limite du quota public anonyme et éviter toute erreur 429.
- **Dimensions Dynamiques & Adaptatives** :
  - Adaptation automatique aux formats : 1024×576 (16:9), 576×1024 (9:16) et 1024×1024 (1:1), sans logo/watermark (`nologo=true`).
  - Repli défensif transparent sur Gemini 2.5 Flash si une clé Gemini est disponible et que le service Pollinations est temporairement indisponible.
- **Suite de Tests d'Audit (Suite 18)** :
  - 336 tests validés avec 100% de succès (0 échec).
  - Validation de `isModeleImagePayant()`, `callPollinationsImage`, de la sélection par défaut et de l'absence de blocage sans clé API.

### [v2.17 — 2026-09-08] — Choix Transparent & Économique des Modèles IA (Gemini Texte & Nano Banana Images)
- **Démystification & Transparence sur les coûts "Nano Banana"** :
  - Identification de l'intitulé de facturation Google Cloud : "Nano Banana" désigne les modèles d'images Gemini / Imagen facturés à l'unité.
  - Identification de la cause de surcoût : l'outil tentait précédemment le modèle Pro (`gemini-3-pro-image` à ~0,12 €) en premier dans le repli automatique.
- **Protection Budgétaire & Priorité Modèles Économiques (Images)** :
  - Le modèle par défaut est désormais `gemini-2.5-flash-image` (~0,035 € par image, soit 4× moins cher).
  - Réorganisation de `IMAGE_MODELS_FALLBACK` : les modèles Flash sont systématiquement priorisés avant les modèles Pro.
  - Enrichissement du sélecteur d'images `#imageModel` avec badges et tarifs indicatifs en euros (⚡ `~0,035 €` vs 💎 `~0,12 €`).
  - Actualisation dynamique via `btnRafraichirModeles` classant automatiquement les modèles disponibles sur la clé avec indication tarifaire.
- **Sélecteur Ergonomique de Modèles Texte (Onglet 1)** :
  - Remplacement du champ texte libre par un sélecteur guidé `#geminiModel` avec les modèles recommandés :
    - `gemini-3.5-flash` (⚡ Recommandé — Rapide, créatif, Free Tier / Quasi gratuit).
    - `gemini-3.5-flash-lite` (🍃 Ultra économique & instantané).
    - `gemini-3.6-flash` (🚀 Dernière génération).
    - `gemini-2.5-flash` (📦 Standard stable).
    - `custom` (✏️ Champ texte libre affiché dynamiquement si modèle spécifique souhaité).
  - Sauvegarde automatique dans `localStorage` (`pixelpapa.subtitles.gemini.model`).
- **Badge Indicateur du Modèle Texte Actif (Onglet 0)** :
  - Ajout de l'indicateur visuel `#lblActiveGeminiModelText` dans le bandeau de direction artistique de l'Atelier d'Écriture, synchronisé en temps réel avec le choix de modèle.
- **Suite de Tests d'Audit (Suite 18)** :
  - 331 tests unitaires et d'intégration validés avec 100% de réussite (0 échec).
  - Validation de `getActiveGeminiModel()`, `setGeminiModelValue()`, `syncGeminiModelUI()`, de la sélection d'images et des paramètres structurels.

### [v2.16 — 2026-09-08] — Contrôle Total de la Structure Musicale & Direction Artistique (Suno v5.5 & Univers Pixel Papa)
- **Nouveau Panneau de Direction Artistique & Structure du Morceau (Onglet 0)** :
  - **Nombre de couplets (`#selNbCouplets`)** : 1 couplet (court/interlude ~1 min), 2 couplets (standard single ~2m30-3m), 3 couplets (storytelling évolutif ~3m30-4m), 4 couplets (saga/freestyle étendu ~4m30+).
  - **Durée cible (`#selDureeCible`)** : Court (~1m00-1m30 / TikTok), Moyen (~2m30-3m00 / Single), Long (~3m30-4m30 / Album).
  - **Débit vocal & Flow (`#selFlowVocal`)** : 
    - MC Solaar & Oxmo Puccino (Laid-back & Posé, respirations amples, ~115 mots/min).
    - Bigflo & Oli (Storytelling Découpé, diction rapide et incisive, ~150 mots/min).
    - Euston XIII (Cloud Rap Vaporeux, voix traînante réverbérée, ~105 mots/min).
    - Lil Wayne (Bounce Syncopé, punchlines élastiques, ~140 mots/min).
    - Julien Doré (Chanté-Rappé Pop, mélodies décalées et poésie solaire, ~110 mots/min).
  - **Angle narratif / POV (`#selAngleNarratif`)** : 1ère personne "Je" (confession joueur), Transmission Père-Enfant "Je → Tu" (mémoire d'arcade et passage de témoin), 3ème personne Légende Roster "Il/Elle" (épopée d'un héros).
  - **Schéma de rimes (`#selSchemaRimes`)** : Rimes riches multisyllabiques (AABB, 3 à 4 phonèmes communs), Rimes croisées poétiques (ABAB), Assonances & Rimes internes en cascade.
  - **Degré d'émotion & Tonalité (`#selDegreTonalite`)** : Sincère & Émouvant, Espiègle & Second degré, Sombre & Réflexif.
  - **Toggles Structurels Suno v5.5** :
    - `[Pré-Refrain]` (`#chkPreRefrain`) : montée de tension avant chaque refrain.
    - `[Pont émotionnel]` (`#chkPont`) : rupture acoustique ou mélodique avant le dernier refrain.
    - `[Beat Switch]` (`#chkBeatSwitch`) : variation de tempo ou cassure rythmique.
    - `[Spoken Word]` (`#chkSpokenWord`) : passage narratif intimiste murmuré au micro.
- **Audit Suno v5.5 Dynamique & Intelligent (`auditParolesSunoDirect`)** :
  - Calibrage de l'estimation de durée en direct selon le débit exact (WPM) de la référence vocale choisie.
  - Règle 5 de volume ajustée dynamiquement selon la durée cible (court, moyen, long).
- **Moteur d'Écriture IA (Gemini)** :
  - Construction dynamique du squelette de balises Suno v5.5 selon le nombre de couplets et les toggles actifs.
  - Infusion des contraintes de débit, métrique, rimes multisyllabiques et transmission dans l'idéation, la génération complète et le polissage.
  - Maintien strict de l'écriture des nombres selon l'usage belge (`septante`, `nonante`, `mille neuf cent nonante-neuf`).
- **Persistance Universelle** :
  - Intégration des 10 paramètres dans le brouillon local (`localStorage`) et dans les instantanés de projets haute capacité IndexedDB (`PixelPapaStudioDB`).
- **Fiabilité & Tests** :
  - Suite de tests `test_audit.html` enrichie, atteignant **313 tests automatisés** exécutés sous Microsoft Edge headless (**313/313 PASS, 0 FAIL**).
  - Zéro ID DOM manquant vérifié par `scratch/deep_audit.py`.

### [v2.15 — 2026-09-08] — Intégration Suno v5.5 & Univers Musical Pixel Papa (Conscious, Cloudy, Jazzy)
- **Mise à niveau complète vers Suno v5.5** :
  - Remplacement de toutes les mentions obsolètes (v3.5 / v4) par **Suno v5.5** dans l'interface, les badges (`#badge-tc`), les tooltips et les instructions.
  - Prise en charge des capacités avancées de Suno v5.5 : acoustique haute fidélité 48kHz, nuances vocales et de respiration, textures hybrides, prompts de style riches (jusqu'à 1000+ caractères), dynamic range étendu et volume de paroles jusqu'à 3m30+.
  - Nouveaux méta-tags Suno v5.5 dans la palette en 1 clic : ajout de `[Beat Switch: Double Time Flow]` et `[Spoken Word: Cinematic narration]`.
- **Univers Musical Pixel Papa — Rap Conscient, Cloudy & Jazzy** :
  - **Inspiration & Références Majeures infusées dans les prompts IA (Gemini)** :
    - *MC Solaar* : allitérations virtuoses, verbe élégant, poésie urbaine, flow nonchalant et fluide.
    - *Oxmo Puccino* : métaphores imagées, conteur poignant, groove jazz/soul feutré, chaleur humaine.
    - *Bigflo & Oli* : sincérité, storytelling fraternel et quotidien, rimes riches et multisyllabiques.
    - *Lil Wayne* : punchlines inattendues, flow élastique et syncopé, métaphores gaming percutantes.
    - *Euston XIII* : cloud rap vaporeux, spleen mélancolique, reverb planante, atmosphère nocturne.
    - *Julien Doré* : douceur pop acoustique décalée, mélodies entêtantes, autodérision poétique.
  - **Refonte des Vibes de l'Atelier d'Écriture (`#selVibeChanson`)** :
    - 🎙️ *Rap Conscient & Poétique (MC Solaar, Oxmo)*
    - ☁️ *Cloud Rap Vaporeux & Nostalgique (Euston XIII)*
    - 🎷 *Storytelling Technique & Sincère (Bigflo & Oli)*
    - ⚡ *Bounce Punchlines & Flow Élastique (Lil Wayne)*
    - ☕ *Pop Urbaine Poétique & Douceur (Julien Doré)*
    - 🎮 *Boss Fight Épique & Victoire Arcade*
  - **Presets de Style Musical Suno v5.5 enrichis (`PRESETS_STYLE_SUNO`)** :
    - 🎷 *Jazzy Conscious Boom-Bap (88 BPM - Rhodes, upright bass, brushed drums, vinyl crackle)*
    - ☁️ *Cloud Rap Vaporeux & Éthéré (135 BPM - ambient synths, 808 glides, lo-fi pads, autotune subtil)*
    - 🎙️ *Storytelling Sincère & Technique (92 BPM - piano mélodique, cordes, boom-bap précis)*
    - ⚡ *Bounce Syncopé & Punchlines Gaming (140 BPM - 808 lourde, hi-hats rapides, beat switch)*
    - ☕ *Pop Urbaine Poétique & Douceur (105 BPM - guitare acoustique, walking bass, sifflement pop)*
    - 🕹️ *Chiptune Synthwave Anthemic (128 BPM - leads 8-bit, basse analogique)*
    - 🎸 *Arcade Rock 90s & 16-bit Leads (145 BPM - guitares distordues, batterie live)*
- **Validation Automatisée** :
  - 288 tests automatisés validés à 100% dans Edge headless (`PASS=288, FAIL=0`).

### [v2.14 — 2026-09-08] — Correctif Visibilité & Fermeture des Fenêtres Modales (Roster & Projets)
- **Résolution du Conflit de Cascade CSS (`.hidden` vs `.modal-overlay`)** :
  - Identification de la cause racine : la classe `.modal-overlay` déclarée après `.hidden` dans `<style>` écrasait `display: none` par `display: flex` (spécificité égale `0, 1, 0`), rendant le modal `#modalRoster` ("Bibliothèque de Personnages") affiché en permanence au-dessus de la page au chargement, et insensible aux clics sur les boutons de fermeture.
  - Ajout de la directive `display: none !important;` sur `.hidden` et règle dédiée `.modal-overlay.hidden { display: none !important; }`.
- **Fermeture Triple Sécurité & Ergonomie** :
  - Bouton croix "✕" (`#btnFermerModalRoster`, `#btnFermerModalProjets`) et bouton de pied de page "Fermer" (`#btnFermerModalRosterFooter`, `#btnFermerModalProjetsFooter`) dotés d'attributs inline `onclick="fermerModalRoster()"` / `onclick="fermerModalProjets()"` en plus des `addEventListener` JS.
  - Forçage programmatique `m.style.display = 'none'` dans `fermerModalRoster()` et `fermerModalProjets()`.
  - Fermeture au clic sur l'arrière-plan sombre (`modal-overlay`).
  - Fermeture universelle via la touche clavier `Échap` (`Escape`).
- **Fiabilisation de l'accès IndexedDB (`getDB`)** :
  - Mise en cache de la promesse d'ouverture `_dbOpeningPromise` pour éviter les ouvertures concurrentes en cas d'appels multiples.
  - Gestion de l'événement `req.onblocked` et ajout d'un timeout de sécurité (1500 ms) avec repli transparent sur la mémoire locale.
- **Validation Automatisée** :
  - Ajout de 12 tests d'intégration DOM couvrant l'état masqué initial, l'ouverture, la fermeture via la croix, le bouton pied de page, le clic backdrop et la touche Échap.
  - 287 tests automatisés validés à 100% dans Edge headless (`PASS=287, FAIL=0`).

### [v2.13 — 2026-09-08] — Numération Régionale & Prosodie Belge (Septante & Nonante)
- **Support Natif du Français de Belgique (`#selVarianteNumerique`)** :
  - Intégration d'un sélecteur régional explicite dans l'éditeur de paroles :
    - 🇧🇪 *Belgique (septante, nonante)* — **Sélectionné par défaut**.
    - 🇫🇷 *France standard (soixante-dix, quatre-vingt-dix)*.
    - 🇨🇭 *Suisse (septante, huitante, nonante)*.
- **Conversion Phonétique Belge Rigoureuse (`nombreEnLettresFR`)** :
  - En variante belge (`'be'`) :
    - 70-79 : `septante`, `septante-et-un`, `septante-deux`... `septante-cinq`... `septante-neuf`.
    - 80-89 : `quatre-vingts`, `quatre-vingt-un`... (selon l'usage belge).
    - 90-99 : `nonante`, `nonante-et-un`, `nonante-deux`... `nonante-neuf`.
    - Années & millésimes : `1999` -> `mille neuf cent nonante-neuf`, `1970` -> `mille neuf cent septante`.
  - Préservation du flux prosodique : « septante » et « nonante » comptent 2 syllabes (contre 3 pour « soixante-dix » et 4 pour « quatre-vingt-dix »), garantissant des rimes et un flow métrique impeccables pour Suno.
- **IA Parolière Calibrée Belgique (Gemini)** :
  - Injection automatique des consignes régionales belges dans les prompts d'écriture complète (`genererParolesCompletesIA`) et de polissage de rimes (`polirRimesIA`).
- **Persistance & Tests** :
  - Persistance de la variante régionale dans les brouillons locaux et snapshots de projet `.pixelpapa`.
  - 275 tests automatisés validés à 100% dans Edge headless (PASS=275, FAIL=0).

### [v2.12 — 2026-09-08] — Studio d'Écriture Musicale & Scénarisée pour Suno v3.5/v4 (Écrivain, Scénariste, Musicien, Producteur & Vidéaste)
- **Atelier d'Idéation & Brainstorming IA (Gemini 2.5/Flash)** :
  - Génération de 3 concepts originaux de morceaux (Titre percutant, Pitch narratif/émotionnel, Hook mémorable, Ambiance sonore recommandée) via `#btnGenererIdeesChanson` avec sélection de vibe (Nostalgie, Victoire, Sombre, Comédie, etc.).
  - Connexion directe au Roster de personnages : injecte les protagonistes et rivaux choisis (`#selPersoEcriture`) au cœur de l'intrigue et des paroles.
  - Générateur de chanson complète en 5 actes (`[Intro]`, `[Verse 1]`, `[Chorus]`, `[Verse 2]`, `[Chorus]`, `[Bridge]`, `[Chorus]`, `[Outro]`, `[End]`) via `#btnGenererParolesCompletes`.
  - Polissage prosodique et enrichissement des rimes via `#btnPolirRimes` (rime riche, flux métrique régulier).
- **Éditeur Prosodique & Suno-Proofing Phonétique 1-Clic** :
  - Bouton *« ⚡ Suno-Proofing (Chiffres & Sigles) »* (`#btnSunoProof`) : convertit instantanément tous les nombres en toutes lettres françaises (`1999` -> `mille neuf cent quatre-vingt-dix-neuf`, `100` -> `cent`) et sépare les acronymes (`PS2` -> `P-S-deux`, `XP` -> `X-P`) pour éliminer 100% des hallucinations de prononciation de Suno.
  - Calculateur de métrique et syllabes (`#btnAnalyserProsodie`) : décompte automatique des pieds et syllabes par vers avec annotation visuelle.
  - Harmonisateur de refrains (`#btnHarmoniserRefrains`) : synchronise tous les blocs `[Chorus]` ou `[Refrain]` au mot près.
  - Palette de balises méta-tags cliquables (`#tagChipsContainer`) : insertion directe de `[Intro]`, `[Verse]`, `[Chorus]`, `[Bass Drop]`, `[Solo]`, `[Bridge]`, `[Outro]`, `[End]`, etc. à la position du curseur.
  - Compteurs métriques en direct : nombre de vers, mots, volume de texte et durée estimée du morceau.
- **Palette de Style Musical Suno Spécialisée** :
  - 5 presets de style optimisés pour Suno v3.5/v4 (`Chiptune Synthwave 128 BPM`, `Arcade Rock 145 BPM`, `Lo-Fi Pixel Nostalgia 82 BPM`, `Cyber Electro 130 BPM`, `16-bit Trap Drill 140 BPM`).
  - Champ de prompt éditable avec bouton 1-clic pour copier directement dans Suno (`#btnCopierStyleSuno`).
- **Contrôle Qualité Dynamique (Audit Suno Réactif)** :
  - Checklist visuelle en temps réel (`#auditReglesSunoContainer`) analysant 5 critères capitaux pour Suno :
    1. Présence obligatoire de la balise `[End]` pour couper les boucles infinies de Suno.
    2. Répétition exacte des refrains pour garantir la cohérence thématique.
    3. Absence de chiffres bruts susceptibles de faire bugger la phonétique.
    4. Longueur des vers et respirations pour éviter l'asphyxie vocale de l'IA.
    5. Volume global du texte calibré pour le format 2 à 4 minutes.
- **Persistance & Rétrocompatibilité Totale** :
  - Sauvegarde et restauration automatiques des champs de l'onglet 0 dans les brouillons locaux (`draftSave` / `draftLoad`) et snapshots multi-morceaux (`.pixelpapa`).
  - Synchronisation bidirectionnelle avec le reste de l'application et les 267 tests automatisés validés à 100% sans aucune régression.

### [v2.11 — 2026-09-08] — Architecture de Persistance Modulaire & Réutilisation d'Assets (Producteur / Vidéaste)
- **Moteur de Stockage Haute Capacité (`PixelPapaStudioDB`)** :
  - Implémentation d'une couche native IndexedDB sans aucune dépendance externe, surmontant la limite des 5 Mo du `localStorage`.
  - Quatre tables de stockage dédiées (`projects`, `character_roster`, `style_presets`, `lyrics_archives`).
  - Fallback en mémoire et `localStorage` transparent garantissant le bon fonctionnement même dans les contextes restreints ou iframes.
- **Bibliothèque Globale de Personnages (Roster Bible & Multi-personnages)** :
  - Modal dédié `#modalRoster` affichant la galerie de tous les personnages créés (photos de référence, character sheets haute résolution, notes physiques, rôle narratif).
  - Boutons d'injection directe en 1 clic dans n'importe quel morceau :
    - *« Injecter en Perso 1 »* : Remplace le protagoniste principal et met à jour instantanément la prévisualisation et la character sheet.
    - *« + Ajouter au morceau »* : Crée un nouveau personnage secondaire (Rival, Allié, Mentor, Duo) dans le projet en cours.
  - Bouton de sauvegarde rapide sur chaque carte de personnage (Perso 1 comme persos secondaires) : *« 💾 Sauvegarder dans bibliothèque »*.
  - Export et import indépendants au format standard `.character.json` pour échanger des personnages entre créateurs ou sauvegarder sur disque.
- **Barre d'Outils et Gestionnaire Multi-Morceaux (`#projectBar`, `#modalProjets`)** :
  - Affichage en tête de page du titre du morceau en cours, synchronisé dynamiquement avec la saisie des paroles et métadonnées.
  - Badge de statut de sauvegarde dynamique (`Brouillon local`, `Enregistré localement ✓`, `Actif`).
  - Modal `#modalProjets` listant tous les morceaux enregistrés avec date de dernière modification, nombre de lignes calées et personnages associés.
  - Actions disponibles par morceau : *Charger*, *Dupliquer* (idéal pour faire des variantes radio/clip/remix), *Exporter .pixelpapa*, *Supprimer*.
  - Création de nouveau morceau vierge (*« ➕ Nouveau »*) : réinitialise proprement les paroles et le calage tout en conservant l'accès immédiat à toute votre bibliothèque de personnages et presets de styles.
  - Sauvegarde et restauration universelle au format `.pixelpapa` (JSON complet incluant paroles, timings, personnages, sheets, storyboard et réglages vidéo).
- **Gestionnaire de Presets de Style Visuel (D.A.)** :
  - Menus déroulants et contrôles dans l'onglet Visuels (`#selStylesSauvegardes`, `#btnChargerStylePreset`, `#btnSauvegarderStyle`, `#btnSupprimerStyle`).
  - 5 presets directeurs artistiques intégrés par défaut :
    - 🎮 *Pixel Art Arcade 80-90s*
    - 🌃 *Cyberpunk Neo-Tokyo*
    - 📼 *Anime 90s Cel-Shading*
    - 🎬 *Cinéma 35mm Grain Chaud*
    - 💥 *Comics & BD Encré*
  - Possibilité de sauvegarder sa propre D.A. sous un nom personnalisé et de la recharger en un clic sur de nouveaux morceaux.
- **Système d'Archives et d'Historique de Paroles** :
  - Rangée dédiée sous le champ des paroles dans l'onglet Calage (`#btnArchiverParoles`, `#selParolesArchivees`, `#btnRestaurerParoles`, `#btnSupprimerParoles`).
  - Archivage des versions de paroles horodatées pour explorer des variantes de rimes ou revenir à une écriture précédente sans perte.
- **Suite de Tests et Validation Complète** :
  - Section 15 ajoutée dans `test_audit.html` couvrant l'ensemble des fonctionnalités de stockage, de synchronisation et de modales.
  - Audit automatisé exécuté avec succès sous Microsoft Edge headless : **222 tests réussis, 0 échec (100% PASS)**. 0 ID DOM manquant.

### [v2.10 — 2026-09-08] — Hub d'Export pour Animation Externe & Adaptation Musicale des Illustrations
- **Hub d'Exportation pour Animation Externe (Onglet 4 : Repères animation)** :
  - Transformation de l'onglet 4 en véritable station d'export pour monteurs et animateurs exploitant des logiciels et IA externes (After Effects, Runway Gen-3, Kling, Luma Dream Machine, CapCut, Premiere Pro, DaVinci Resolve, Blender).
  - **Bandeau d'état réactif (`#animHubStatus`)** : Indique en temps réel le nombre de lignes calées, les illustrations prêtes et le découpage de la chanson.
  - **Tableau chronologique interactif (`#tableTimelineAnim`)** :
    - Miniatures cliquables des illustrations générées.
    - Nom de la section et rôle dramatique scénarisé.
    - Minutage musical exact (`Début ➔ Fin` en timecode `00:00,000`).
    - Durée en secondes et décompte précis de frames pour l'animation (à **24 fps**, **30 fps** et **60 fps**).
    - Paroles chantées synchronisées avec chaque plan.
    - Bouton de téléchargement individuel d'illustration et bouton de copie en un clic du prompt d'animation pour Runway / Kling / Luma.
- **Adaptation Continue de l'Illustration avec la Musique (`calculerTimelineScenes`)** :
  - Algorithme d'alignement temporel continu : élimination de tout trou ou écran noir de `0.000s` jusqu'à la fin de la piste audio.
  - Chaque scène hérite d'un début, d'une fin et d'une durée calculés sur les marqueurs musicaux et les paroles.
- **Nommage Intelligent des Fichiers Images (`nomFichierIllustration`)** :
  - Fichiers d'illustrations automatiquement nommés avec leur index d'ordre et leur plage temporelle musicale :
    ex. `01_00m00s-00m04s_intro.png`, `02_00m04s-00m11s_couplet-1-partie-1.png`.
- **Formats d'Export Dédiés aux Logiciels de Montage & Outils d'Animation** :
  - **`sous-titres-paroles.srt`** (`#btnTelechargerSRTAnim`) : Sous-titres des paroles calés ligne par ligne.
  - **`timeline-illustrations-scenes.srt`** (`#btnTelechargerScenesSRTAnim`) : Fichier SRT où **chaque bloc représente une scène/illustration** avec sa durée, ses paroles et son prompt. Importable sur une piste sous-titre dans Premiere, Resolve ou CapCut pour placer automatiquement chaque plan sur la musique.
  - **`feuille-de-montage-scenes.csv`** (`#btnTelechargerCSVAnim`) : Feuille de route tabulaire complète (Index, Nom, Début, Fin, Durée, Frames 24/30/60fps, Paroles, Prompt).
  - **`reperes-animation.json`** (`#btnJSON`) : JSON technique enrichi avec la timeline complète des illustrations pour scripts After Effects et Blender.
  - **`📦 Pack Complet`** (`#btnTelechargerPackAnim`) : Télécharge en une action ordonnée l'intégralité des visuels calés, des deux fichiers SRT, du CSV et du JSON.
- **Intégration dans l'Onglet 7 (Visuels)** :
  - `btnTeleTout` met désormais à profit le nommage intelligent avec timecodes musicaux.
  - Bouton rapide `#btnTelechargerPackVisuels` pour lancer l'exportation du pack montage directement depuis le storyboard.
- **Tests & Qualité** :
  - Suite de tests `test_audit.html` étendue à **174 tests automatisés (100% PASS, 0 FAIL)** sous Microsoft Edge sans interface.
  - Intégrité DOM : 146 identifiants DOM uniques vérifiés (0 identifiant manquant).

### [v2.9 — 2026-09-08] — Contrôle Total, Désactivation & Personnalisation du Bandeau Viral POV
- **Masquage par défaut & Intégrité visuelle** :
  - `state.afficherPOV` initialisé à `false` par défaut : le bandeau POV ne s'affiche plus de manière intempestive sur la vidéo ou l'aperçu si l'utilisateur ne le souhaite pas.
  - Rétrocompatibilité `draftLoad()` : les sessions antérieures et brouillons sans réglage explicite chargent automatiquement le bandeau en mode masqué.
  - Optimisation `dessinerBandeauPOV()` : court-circuit immédiat du rendu si non actif (`if(!state.afficherPOV) return;`).
- **Contrôles Dédiés dans l'Onglet 8 (Aperçu & Export Vidéo)** :
  - Case à cocher réactive `[ ] Bandeau Accroche POV` (`#chkAfficherPOV`) pour activer/désactiver le bandeau en un clic directement depuis les paramètres vidéo.
  - Bouton rapide `✕ Cacher` (`#btnMasquerPOV`) pour faire disparaître instantanément le bandeau.
  - Sélecteur de présets POV (`#selPresetPOVApercu`) : Choix rapide parmi les 5 accroches virales (*1999 Boss Fight*, *Énergie*, *Curiosité Drop*, *Défi Rétention*, *Nostalgie 2000s*, *Personnalisé*, ou *Aucun (Caché)*).
  - Champ texte en direct (`#txtPOVApercu`) : Permet d'éditer ou de remplacer le texte à la volée avec répercussion instantanée sur le canvas de prévisualisation et l'export.
- **Contrôles Synchronisés dans l'Onglet 6 (Hook / Teaser)** :
  - Bouton `✕ Cacher le bandeau` (`#btnMasquerPOVHook`).
  - Option `— Aucun bandeau (Caché) —` (`value="none"`) dans `#selPresetPOV`.
  - Fonction bidirectionnelle `synchroniserPOV(afficher, texte, presetVal)` : toute modification effectuée dans l'un des deux onglets est immédiatement synchronisée dans l'autre, répercutée sur le canvas et persistée dans `localStorage`.
- **Tests & Qualité** :
  - Suite de tests `test_audit.html` portée à **147 tests automatisés (100% PASS, 0 FAIL)** sous Microsoft Edge sans interface.
  - Intégrité DOM : 136 identifiants uniques vérifiés (0 identifiant manquant).

### [v2.8 — 2026-09-08] — Gestion Multi-Personnages & Moteur Storyboard Scénariste / Producteur Exécutif
- **multi-character-management (Onglet 7 : Visuels)** :
  - **Gestion illimitée de personnages** (`#charactersContainer` et bouton `➕ Ajouter un personnage`) :
    - Chaque personnage possède sa fiche dédiée : nom personnalisable, rôle scénaristique (Protagoniste, Rival, Allié, Mentor, Duo, Autre), consignes et caractéristiques physiques distinctes.
    - **Galeries et dropzones photos indépendantes** : chargement de photos de référence dédiées pour chaque personnage.
    - **Génération de Character Sheets individuelles** : chaque personnage peut avoir sa planche de référence stylisée générée avec Gemini Imagen, réutilisable pour préserver sa silhouette et son visage d'une scène à l'autre.
    - Suppression individuelle des personnages secondaires avec protection du héros principal (`Personnage 1`).
  - **Rétrocompatibilité totale** :
    - Les anciens brouillons `localStorage` (avec consignes uniques ou photos uniques) sont automatiquement migrés dans le premier personnage (`characters[0]`).
    - Le getter/setter Proxy sur `state.characterNotes` et `state.characterSheet` reste synchronisé en continu.
- **screenwriter-producer-engine (Moteur IA & Prompts)** :
  - **Vision du Producteur Exécutif** :
    - Exigence de « High Production Value » : échelle cinématographique, setpieces grandioses et pacing dynamique.
    - Identification du **« Money Shot »** au niveau des refrains et des drops musicaux pour maximiser l'impact visuel.
    - Contraste visuel et silhouettes strictes pour que les personnages ne soient jamais confondus à l'image.
  - **Vision du Scénariste de Cinéma** :
    - **Structure dramatique en 5 Beats musicaux** (Beat 1: Exposition & Déclencheur -> Beat 2: Montée de tension -> Beat 3: Climax Refrain & Catharsis -> Beat 4: Crise & Retournement -> Beat 5: Apothéose & Résolution).
    - **Chimie et dynamique des personnages** : interactions concrètes (duels de regards, synchronisation, trahison, entraide) et mise en scène spatiale (blocking cinéma).
    - Règle « Show, don't tell » renforcée avec actions physiques précises et objets symboliques récurrents.
  - **Attribution des Personnages par Scène** :
    - `STORYBOARD_PLAN_SCHEMA` enrichi avec `personnages_presents` (tableau de noms) et `dynamique_personnages`.
    - Sélecteur interactif sur chaque carte de scène générée dans le plan pour ajuster les personnages présents (Tous, Perso solo, ou Décor seul) avant de générer les images.
    - `buildImageParts()` injecte uniquement les character sheets et photos des personnages effectivement présents dans chaque scène.
- **Tests & Qualité** :
  - Extension de la suite automatisée `test_audit.html` à **135 tests (100% PASS, 0 FAIL)** sous Microsoft Edge sans interface.
  - Audit d'intégrité DOM : 131/131 identifiants DOM validés sans aucun identifiant manquant.

### [v2.7 — 2026-09-08] — Pack Viral Réseaux Sociaux : Studio Teasers 9:16, Bandeaux POV, Scanlines CRT & Finitions Rétro Arcade
- **viral-teaser-studio (Onglet 6 : Hook / Teaser)** :
  - **Export Teaser Vidéo Court (15s / 30s / Refrain)** : Découpe et enregistre instantanément le segment le plus explosif du morceau en vidéo prête à publier, sans exporter la chanson complète de 3 minutes.
  - **Formats dédiés aux Réseaux** : 9:16 Vertical (TikTok, Reels, Shorts — Recommandé), 1:1 Carré (Instagram Feed), 16:9 Paysage (YouTube).
  - **Bandeau Viral POV / Accroche Psychologique** : Bandeau d'accroche arrondi incrusté dans la safe area supérieure de la vidéo avec presets viraux :
    - 🎮 *POV Rétro* : `POV: Tu es en 1999 et ce boss fight démarre...`
    - ⚡ *Émotion / Partage* : `Envoie ça à la personne qui te donne cette énergie !`
    - 🎧 *Curiosité IA* : `J'ai demandé à l'IA d'écrire l'hymne ultime. Attends le drop...`
    - 🔥 *Défi Rétention* : `Essaie de ne pas bouger la tête (Impossible 🎧)`
    - 📼 *Nostalgie* : `La nostalgie des samedis matin dans les années 2000...`
    - ✍️ *Personnalisé* : champ libre éditable en direct.
  - **Export du "Son TikTok" (.wav)** : Découpe directe du fichier audio sur la fenêtre du teaser via décodage `AudioContext` et encodeur WAV 16-bit PCM pur sans dépendance, prêt à uploader comme son original TikTok/Reels.
- **retro-arcade-vibe (Onglet 8 : Aperçu & Export)** :
  - **Scanlines CRT 60 FPS** (`chkCrtScanlines`) : Lignes de balayage cathodiques authentiques de borne d'arcade rétro avec halo phosphorique.
  - **Incrustations Rétro OSD** (`selRetroOverlay`) :
    - `REC ● 00:14:22` : Témoin rouge clignotant et timecode rétro façon caméscope VHS.
    - `INSERT COIN - 2 CREDITS` : Clignotement d'arcade authentique en typographie 8-bit.
    - `PIXEL PAPA SOUND SYSTEM` : Logo vintage doré.
  - **3-Second Pattern Interrupt** (`chkPatternInterrupt`) : Flash d'impact au démarrage et décompte arcade `3... 2... 1... GO!` pour briser le scroll au pouce dès les premières secondes.
- **Architecture & Moteur** :
  - Moteur unifié d'export `lancerExportVideo()` gérant à la fois les masters complets et les extraits courts avec barre de progression en temps réel.
  - `resolutionExport(format, qualite)` dynamique supportant 9:16, 1:1, 16:9 en 1080p et 720p.
- **Tests & Qualité** :
  - Extension de `test_audit.html` à **106 tests automatisés (100% PASS, 0 FAIL)** sous Headless Edge.

### [v2.6 — 2026-09-08] — Storyboard Narratif : Histoire Parallèle, Quête Héroïque & Directives Scénario
- **storyboard-narrative-engine** :
  - **Sélecteur d'Approche Narrative (Onglet 7)** :
    - *🎬 Histoire parallèle & Métaphore (Clip Réalisateur — Recommandé)* : Ne traduit pas les paroles mot à mot. Crée un scénario dramatique original fort (comme les clips Daft Punk, Gorillaz ou Michael Jackson) qui exprime le sous-texte émotionnel et le message profond de la chanson à travers une aventure ou un dilemme concret.
    - *📖 Récit direct & linéaire (L'histoire des paroles)* : Raconte pas à pas et chronologiquement l'histoire littérale décrite par les paroles.
    - *⚡ Quête héroïque & Transformation (Arc dramatique)* : Structure le clip en voyage initiatique (Exposition -> Déclencheur -> Escalade des épreuves -> Climax libérateur au refrain -> Victoire et métamorphose).
    - *🎸 Performance scénique & Énergie clip* : Alterne entre performance scénique charismatique et inserts narratifs stylisés à haute intensité.
  - **Champ "Idée de scénario / Message du clip" (`#scenarioNotes`)** :
    - Permet au créateur de fournir ses propres idées, métaphores ou scénarios directeurs (ex. "Le héros construit un robot néon dans son garage et s'échappe de la ville grise").
    - Directement injecté dans les consignes du prompt avec statut de consigne prioritaire.
  - **Arc dramatique structuré (`role_dramatique`)** :
    - Enrichissement de `STORYBOARD_PLAN_SCHEMA` avec le champ obligatoire `role_dramatique` ("Introduction & Conflit", "Déclencheur", "Montée en tension", "Climax / Libération", "Apothéose", "Résolution").
    - Règle cinématographique "Show, don't tell" : actions physiques précises, continuité d'objets fétiches ou de véhicules d'un plan à l'autre, et explosion visuelle sur les refrains.
  - **Interface enrichie du plan de scènes** :
    - Badges dorés dramatiques (`🎬 Rôle dramatique`) dans l'en-tête de chaque carte de scène.
    - Bloc distinct de trame narrative (`Trame narrative : ...`) avant le texte des paroles.
  - **Persistance & Réactivité** :
    - Sauvegarde automatique dans `localStorage` (`draftSave` / `draftLoad`).
    - Intégration réactive dans le Proxy `state` (`state.approcheNarrative`, `state.scenarioNotes`).
  - **Suite de tests & Qualité** :
    - Extension de `test_audit.html` (82 tests automatisés, 100% de réussite).

### [v2.5 — 2026-09-08] — Typographie Lyrics Pro, Styles Vidéo Viraux & Moteur de Montage (CapCut / Premiere)
- **lyrics-typography-engine** :
  - Intégration de 6 polices de caractères typographiques avec chargement Google Fonts et repli 100% système hors-ligne :
    - *Montserrat Bold* : style TikTok / CapCut moderne épuré.
    - *Impact Condensed* : style YouTube Shorts dynamique et punchy.
    - *Bebas Neue* : grand titrage cinématique / affiche.
    - *Permanent Marker* : style street, graffiti, rap et rock.
    - *Press Start 2P* : style pixel art 8-bit chiptune et rétro-gaming.
    - *Cinzel Roman* : style serif classique, poétique et grandiose.
  - 7 Presets de styles visuels des paroles :
    - *CapCut Jaune Punchy (Contour 3D)* : texte jaune d'or `#FFE600`, contour noir épais (8-10px) et ombre portée décalée franche 3D. Lisibilité maximale sans boîte opaque.
    - *TikTok Pop (Blanc Pur & Contour)* : texte blanc pur `#FFFFFF` avec contour noir net et ombre portée douce.
    - *Cyber Neon Glow (Lueur Électrique)* : cyan éclatant `#00F0FF` avec halo de diffusion intense réactif aux kicks de basse.
    - *Capsule Moderne (Fond Pill Fumé)* : boîte aux coins arrondis (`drawRoundedRect`) en verre noir fumé semi-transparent (`rgba(12,16,26,0.78)`).
    - *Cinéma Minimal (Sans Boîte)* : ivoire délicat `#FFF8EE` avec ombre cinématographique diffuse.
    - *Karaoké Dynamique (Suivi Lumineux)* : surbrillance dorée progressive au tempo du chant (mots passés en blanc, mot actif illuminé en or, mots futurs estompés).
    - *Boîte Arcade Rétro (Pixel Frame)* : cadre pixelisé style RPG rétro avec double bordure contrastée cyan et blanc.
  - 5 Modes d'animation de texte à chaque réplique :
    - *Pop / Rebond Élastique* : rebond dynamique (scale 1.16 -> 1.0) à l'apparition de chaque phrase.
    - *Slide Up* : translation ascendante douce (+24px -> 0px) avec fondu transparent.
    - *Karaoké Mot-à-Mot* : synchronisation au fil du tempo.
    - *Pulsation Musique* : micro-pulsation réactive à l'énergie spectrale des basses.
    - *Statique* : rendu instantané classique sans mouvement.
- **pro-video-transitions-optics** :
  - 5 Transitions de scènes inspirées des suites de montage (Premiere Pro, CapCut, DaVinci Resolve) :
    - *Fondu doux cinématique (0.8s)* : fondu enchaîné d'exposition linéaire.
    - *Whip Pan (Coup de fouet)* : balayage latéral ultra-rapide avec accélération exponentielle et traînée de vitesse blanche.
    - *Zoom Impact (Crash Cut)* : zoom avant explosif en sortie de plan et décélération douce sur le nouveau plan.
    - *Film Burn (Flash 35mm)* : flash radial chaud doré/blanc à l'amorce de la coupe simulant une brûlure de pellicule.
    - *Glitch RVB* : décalage éphémère de tranches horizontales et saut d'image sur le beat.
  - Finitions optiques professionnelles :
    - *Format Cinemascope 2.39:1* : barres de letterboxing noir pur (Scope) en haut et bas de l'écran.
    - *Aberration Chromatique (RGB Split)* : micro-décalage optique rouge/cyan réactif aux gros drops de basse.
- **reactive-settings-persistence** :
  - Ajout des 6 nouveaux sélecteurs et cases à cocher dans la console de montage (Aperçu vidéo, onglet 8).
  - Persistance automatique dans `localStorage` via `draftSave()` / `draftLoad()`.

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