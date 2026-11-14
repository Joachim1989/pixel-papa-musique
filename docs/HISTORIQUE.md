# Historique — de pixel-papa-music (v1) à pixel-papa-musique

Récupéré le 2026-11-14 du README de
[pixel-papa-music](https://github.com/Joachim1989/pixel-papa-music) (v1,
dernier commit `6dee8f0` du 2026-10-06), avant son archivage. Ce dépôt-ci en
reprend toutes les fonctions et ajoute les projets multiples, le stockage
IndexedDB, la bibliothèque de personnages et l'export vidéo MP4/WebM.

Notes d'écart avec la version actuelle :
- la v1 disait l'export vidéo indisponible sur Safari/iOS ; le README actuel
  le cite comme compatible — à vérifier sur appareil avant de l'affirmer ;
- plusieurs points de la roadmap ci-dessous sont faits depuis (relance des
  scènes manquantes, `docs/test_audit.html`) : la garder comme mémoire, pas
  comme liste à jour (voir `docs/MEMOIRE.md`).

## Choix d'architecture (v1, toujours valables)

- **L'écriture reste conversationnelle.** Le skill `pixel-papa-suno`
  propose des angles et fait un vrai contrôle qualité (rimes, bars,
  prononciation) avant de livrer — un bouton "générer" dans l'outil
  produirait un texte moins abouti en sautant ces vérifications.
  L'onglet 0 ne fait que relayer, pas écrire.
- **Le montage abouti reste externe** (CapCut/VN/Canva confirmés à
  l'usage) : l'outil fournit les matériaux (sous-titres, karaoké, images,
  repères) et, depuis l'export vidéo de l'onglet Aperçu, un brouillon
  animé exploitable tel quel — mais pas un vrai moteur de montage
  (transitions personnalisées, réglages fins, etc.). L'export vidéo tourne
  en temps réel dans le navigateur (MediaRecorder + `canvas.captureStream`),
  produit du `.webm` (pas de `.mp4` sans encodeur serveur) et demande
  Chrome/Firefox/Edge (pas Safari/iOS, qui n'exposent pas ces API).
- **Aucune image générée n'est sauvegardée** (trop lourd pour
  localStorage) : un avertissement bloque la fermeture/rafraîchissement
  tant qu'il y a des visuels non téléchargés dans la session.
- **Le dernier modèle image qui a fonctionné est mémorisé** (comme le
  modèle texte) pour éviter de retenter des modèles en échec à chaque
  scène du storyboard.

## Roadmap héritée de la v1

- Variantes multiples par scène (choisir parmi 2-3 plutôt qu'une seule)
- "Réessayer seulement les échecs" plutôt que relancer tout le storyboard
- Extraction automatique de plusieurs teasers candidats
- Suite de tests committée (aujourd'hui vérifié ad hoc avec Playwright à
  chaque changement, rien n'est gardé dans le dépôt)

## Les 9 onglets de la v1 (référence)

`sous-titres/index.html` — page HTML autonome (aucune
installation, ouvre le fichier directement dans un navigateur). Un seul
calage (paroles + audio), neuf onglets qui en découlent sans reprendre le
travail :

0. **Écriture** — pas un générateur : récupère le titre/les paroles déjà
   écrits en conversation avec Claude (skill `pixel-papa-suno`, qui
   propose des angles puis vérifie rimes/minutage/prononciation avant de
   livrer) et les envoie vers le Calage. Inclut un pense-bête (checklist
   qualité + rappel que Suno limite les téléchargements, pas les crédits).
1. **Calage** — colle les paroles (balises `[Refrain]` etc. reconnues),
   charge l'audio, cale au clavier ("tap to sync" — Entrée à chaque
   ligne, fiable à 100 %) avec pré-remplissage optionnel par IA (Gemini,
   clé perso). Une fois calées, les lignes défilent toutes seules à
   l'écoute (suivi de lecture) pour vérifier sans rien taper.
2. **Sous-titres** — export `.srt` / `.vtt`.
3. **Karaoké** — export `.ass`, surlignage doré, ligne entière (exact) ou
   mot par mot (estimation, à vérifier).
4. **Repères animation** — le même minutage en `.json` (lignes + sections).
5. **Pack SEO / pub** — titres, description YouTube avec chapitres réels,
   tags, et une **légende Instagram/TikTok prête à coller** (accroche +
   texte de pub qui vend le morceau + appel à l'action + hashtags), plus
   les hashtags seuls pour un premier commentaire séparé.
6. **Hook / teaser** — repère le refrain (balise ou répétition) et
   propose une fenêtre de clip courte pour Shorts/Reels.
7. **Visuels** — deux volets séparés en amont, comme en pré-prod
   illustration : **Personnages** — un ou PLUSIEURS (papa, enfant...),
   chacun sa carte, ses photos et sa propre **character sheet** générée
   indépendamment (planche de référence déjà dans le style Pixel Papa,
   nettement plus fiable qu'une photo brute réinjectée à chaque scène ;
   toutes les character sheets définies sont jointes ensemble, nommées,
   à chaque génération pour rester reconnaissables individuellement) —
   et **Référence de style** (une image séparée, dédiée uniquement à
   remplir la fiche de style texte, indépendante du/des personnage(s)).
   Puis
   vignette YouTube + storyboard **en deux étapes** : d'abord un plan
   de scènes (appel texte qui lit la chanson ENTIÈRE, comprend
   l'histoire racontée et propose, section par section — chaque
   occurrence numérotée si une balise revient, ex. "Refrain 1/2" — une
   scène cohérente avec les scènes voisines), relisible et modifiable
   avant de dépenser des générations d'image, avec une **photo
   d'inspiration optionnelle par scène** (pose/action à évoquer,
   distincte du personnage/style globaux) ; puis les images
   elles-mêmes (Gemini). Chaque scène reste régénérable (prompt
   éditable, sa propre photo d'inspiration) ; cohérence d'une scène à
   l'autre via chaînage ; format 16:9/9:16/1:1 au choix, réellement
   respecté (paramètre d'API `imageConfig.aspectRatio`, pas qu'une
   phrase dans le prompt) ; **sélecteur de modèle d'image** qui
   interroge le compte Google en direct (bouton "Voir les modèles
   disponibles") pour choisir explicitement un modèle plus récent
   plutôt que de deviner un nom figé dans le code.
8. **Aperçu** — previz dans le navigateur (images du storyboard
   enchaînées au bon timing avec les sous-titres par-dessus, pour valider
   le rythme) **+ export vidéo animée** : anime les images (zoom marqué +
   travellings qui alternent de scène en scène, fondu enchaîné entre les
   scènes plutôt qu'un cut sec), incruste les sous-titres et enregistre
   le tout avec l'audio réel via MediaRecorder — un vrai fichier `.webm`
   téléchargé, généré entièrement dans le navigateur. Brouillon
   utilisable tel quel ou base à reprendre dans CapCut/VN/Canva pour un
   montage plus abouti.
