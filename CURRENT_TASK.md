# CURRENT_TASK

> Ce fichier est la mémoire persistante du projet. Toute nouvelle
> session Claude Code doit le lire en premier, avant de demander quoi
> que ce soit à l'utilisateur. Voir aussi TODO.md et NIGHTLY_LOG.md.

## Tâche actuelle

Terminer la vérification finale du vélo (fait) puis choisir et
commencer le prochain objet de la liste TODO.md, section "Prochains
objets à créer".

## État actuel

- Vélo (`DetailedObjects.velo`) : **terminé et vérifié visuellement**.
  5 mécanismes couplés, chaîne visible et correctement routée par le
  dérailleur (vérifié par rendu isolé en vert), guidon proportionné,
  selle connectée, frein à disque fonctionnel et visible.
- Perceuse à colonne (`DetailedObjects.perceuse`) : terminée dans une
  session précédente (5 mécanismes couplés).
- 9 objets catalogue "mécanisme simple" existants et fonctionnels
  (voir TODO.md).
- Total banque actuelle : 11/30 objets.

## Fichiers concernés

- Fichier source de travail :
  `/tmp/claude-0/-home-user-francais5/29fd2b1d-92f9-5bfe-a51a-18ae1fae80cf/scratchpad/atelier-mecanismes.html`
  (⚠️ ce chemin est dans un scratchpad éphémère lié à la session — il
  ne survit PAS entre les sessions. **Le dépôt Git est la seule copie
  durable.**)
- Dépôt GitHub : `colorjazz/sciences4_3d`, fichier `index.html`
  (copie exacte du fichier source ci-dessus après chaque commit).
- Site live : https://sciences3d.netlify.app
- Environnement de test local (Playwright + three.js local, pour
  éviter les dépendances réseau) :
  `.../scratchpad/testenv/test.html` — **ATTENTION** : ce fichier a
  deux substitutions par rapport au source réel :
  1. CDN three.js → `node_modules/three/build/three.min.js` (local)
  2. `window.__mainScene=scene;` ajouté **uniquement** après la
     déclaration de la scène principale (celle suivie de
     `var camera=new THREE.PerspectiveCamera(38,1,0.05,100);` — le
     far-plane `100` est le marqueur unique qui la distingue de la
     scène des mini-aperçus de fiche technique, qui utilise un far
     de 50). Ne PAS faire un remplacement global naïf de
     `var scene=new THREE.Scene();` — il y a DEUX occurrences dans le
     fichier (scène principale + scène de chaque mini-viewer dans
     `createMiniViewer`), et un remplacement global pointe
     `window.__mainScene` vers le mauvais objet (déjà arrivé une
     fois cette session, a causé une fausse alerte de bug).

## Ce qui fonctionne

- Rendu 3D complet du vélo et de la perceuse, animation, vue éclatée,
  changement de vitesse, fiche technique avec mini-aperçus par étage.
- Interface responsive (rail rétractable sur mobile).
- Chaîne de vélo : algorithme à 4 centres tangents (`beltPts`), routé
  à travers le dérailleur, TubeGeometry rayon 0.019, couleur
  0x555b62 (assez claire pour être visible).

## Bugs connus / points de vigilance

- Aucun bug connu actif sur le vélo ou la perceuse à ce jour.
- Les 9 objets catalogue "mécanisme simple" n'ont pas été passés en
  revue visuellement dans cette session — à vérifier si le temps le
  permet.
- Attention à la perte de contexte du scratchpad entre sessions : le
  fichier de travail à `/tmp/...` peut disparaître. **Toujours partir
  de `index.html` dans le dépôt GitHub comme source de vérité**, pas
  du scratchpad, si le scratchpad semble absent ou périmé au début
  d'une nouvelle session.

## Prochaine action exacte

1. `git clone` ou lire `colorjazz/sciences4_3d` (branche `main`) pour
   récupérer `index.html` à jour comme point de départ.
2. Choisir un objet dans TODO.md → "Prochains objets à créer" (le
   premier de la liste qui n'a pas encore de coche, sauf meilleure
   idée pédagogique).
3. Concevoir ses 2-4 mécanismes couplés réels (comme le vélo/la
   perceuse), pas juste un `Mechanisms.*` générique isolé.
4. L'ajouter à `DetailedObjects`, l'enregistrer dans `CATALOGUE`.
5. Tester avec Playwright (screenshots + vérification de geometry
   non-dégénérée comme fait pour la chaîne du vélo) avant de
   considérer la tâche terminée.
6. Commit + push vers `colorjazz/sciences4_3d` branche `main`.
7. Mettre à jour TODO.md (cocher l'objet) et NIGHTLY_LOG.md (nouvelle
   entrée), puis recommencer à l'étape 2.
