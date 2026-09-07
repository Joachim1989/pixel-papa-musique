# Directives Projet — Pixel Papa Musique

## Description du Projet
Application web mono-page (`index.html`) sans dépendances ni build (ouvrable en local d'un double-clic), dédiée au calage audio, sous-titres, karaoké, animations et visuels pour les chansons Pixel Papa.

## Fichier de suivi & Mémoire
Consulter et maintenir à jour le fichier `MEMOIRE.md` à la racine pour connaître :
1. Le backlog des modifications futures prévues ("À avoir").
2. L'historique complet des versions et refactorings.

## Règles Techniques Obligatoires
1. **Autonomie** : Conserver l'application dans un seul fichier `index.html`. Aucun framework externe lourd ni package.json.
2. **State Réactif (`state`)** :
   - L'état global est un `Proxy`.
   - Ne pas ajouter d'appels manuels à `draftSave()` ou `render()` dans les écouteurs : les setters du Proxy s'en chargent.
3. **Rendu DOM ciblé** :
   - Pour les modifications de timecodes, appeler `updateLineDOM(idx)` ou `render(idx)` ciblé. Ne jamais vider `#lignes` avec `box.innerHTML = ''` pour un simple changement de temps.
4. **CSS & Styles** :
   - Définir les classes dans la balise `<style>` (ex. `.card-md`, `.w-full`, etc.). Ne jamais réintroduire d'attributs `style="..."` dans les balises HTML.
5. **APIs Modernes** :
   - Préférer `navigator.clipboard.writeText` sans fallback obsolète `execCommand`.