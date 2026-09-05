# CURRENT_TASK

> Ce fichier est la mémoire persistante du projet. Toute nouvelle
> session Claude Code doit le lire en premier, avant de demander quoi
> que ce soit à l'utilisateur. Voir aussi TODO.md et NIGHTLY_LOG.md.

## Tâche actuelle

La restructuration demandée le 2026-09-04 (accueil Comprendre/Tester,
ménage du catalogue, thème papier — voir "Structure de l'app"
ci-dessous) est terminée et vérifiée. Prochaine étape : choisir et
commencer le prochain objet de la liste TODO.md, section "Prochains
objets à créer" (premier non coché : robinet mélangeur ou ciseaux à
cliquet).

## État actuel

- Vélo (`DetailedObjects.velo`) : **terminé et vérifié visuellement**.
  5 mécanismes couplés, chaîne visible et correctement routée par le
  dérailleur (vérifié par rendu isolé en vert), guidon proportionné,
  selle connectée, frein à disque fonctionnel et visible.
- Perceuse à colonne (`DetailedObjects.perceuse`) : terminée dans une
  session précédente (5 mécanismes couplés).
- Ouvre-boîte électrique (`DetailedObjects.ouvreBoite`) : **terminé et
  vérifié**. 2 mécanismes couplés (réducteur poulies/courroie, levier
  de serrage). Vérifié par mesure de bounding-box 3D que la molette
  d'entraînement et la molette coupante touchent tangentiellement la
  boîte. Vue éclatée testée sans erreur.
- Sécheuse à linge (`DetailedObjects.secheuse`) : **terminée et
  vérifiée, y compris le bug d'axe de rotation "pièce qui flip"
  finalement corrigé le 2026-09-04**. 2 mécanismes couplés (courroie
  sur tambour, loquet à came). Poulie moteur et galet tendeur tournent
  maintenant sur un axe fixe (vérifié par mesure directe de l'axe dans
  le temps, pas juste visuellement). Vue éclatée testée sans erreur.
- Perceuse-visseuse sans fil (`DetailedObjects.perceuseSansFil`) :
  **terminée et vérifiée**. 2 mécanismes couplés (réducteur
  planétaire, mandrin auto-serrant). Positions vérifiées par mesure
  de bounding-box, vue éclatée testée sans erreur. Carter/poignée/
  batterie retravaillés le 2026-09-04 (LatheGeometry + ExtrudeGeometry,
  inspirés d'une référence Three.js testée séparément) — les 2
  mécanismes n'ont pas changé, seul l'habillage visuel statique.
- Les 9 objets catalogue "mécanisme simple" ont été **retirés** de la
  banque affichée le 2026-09-04 (ménage demandé par l'utilisateur —
  voir section "Structure de l'app" ci-dessous et TODO.md). Leur code
  `Mechanisms.*` sous-jacent existe toujours, utilisé par l'onglet
  « Atelier — générer ».
- Total banque actuelle : **5/30 objets** (les 5 `DetailedObjects`
  ci-dessus).

## Structure de l'app (2026-09-04 — restructuration majeure)

L'app a maintenant 3 écrans, gérés par `showScreen(id)` (dans le script
principal, juste avant la section "Boot") :

- **`#homeScreen`** — écran d'accueil, affiché par défaut au chargement
  (`showScreen('homeScreen')` en toute fin de boot). Deux cartes :
  `#goComprendre` → écran Comprendre, `#goTester` → écran Tester.
- **`#atelierScreen`** — l'app originale (rail d'objets + scène 3D +
  fiche technique), inchangée fonctionnellement, mais désormais nommée
  **« L'Atelier »** dans l'interface (le `<h2>` du rail, anciennement
  « Banque d'objets »). Accès via la carte « Tester » de l'accueil, ou
  retour à l'accueil via `#atelierHomeBtn`.
  ⚠️ Piège de layout déjà rencontré et corrigé : `#atelierScreen` DOIT
  avoir `display:flex;flex-direction:column;min-height:0` en CSS, sinon
  le flex-layout du `body` (qui traitait `header.titleblock` et
  `.workspace` comme enfants directs) casse dès qu'on les enveloppe
  dans un conteneur.
  ⚠️ Autre piège : le canvas Three.js est dimensionné à 0 tant que
  l'écran est `[hidden]` (`display:none`). `showScreen()` appelle donc
  `resizeRenderer()` et `resizeWorkshop()` explicitement à chaque
  passage vers `'atelierScreen'`, sinon la scène resterait mal cadrée
  après un premier affichage caché.
- **`#comprendreScreen`** — nouveau contenu théorique général,
  INDÉPENDANT des objets de la banque (décision explicite de
  l'utilisateur : "contenu général, pas ancré sur les objets"). 5
  sujets dans `.topic-nav` / `.topic-article`, bascule via classe JS
  simple (pas de framework) : mécanismes (transmission/transformation/
  combiné), liaisons et guidages (4 caractéristiques + 3 types de
  guidage avec schémas SVG), schématisation (principe vs construction),
  matériaux et contraintes (5 contraintes mécaniques + 5 propriétés de
  matériaux, avec schémas SVG à flèches), fonctions électriques (6
  fonctions). Si l'utilisateur demande un jour d'ancrer ce contenu sur
  des pièces réelles des objets de L'Atelier (l'autre option qu'il
  avait proposée), ce sera une réécriture, pas un ajustement mineur.

**Thème visuel** : repeint le 2026-09-04 à partir d'une texture/couleur
papier fournie par l'utilisateur — palette beige/crème chaude
(`--bg:#f1e9d8`, `--surface:#fffcf5`, `--ink:#3a2f20`, etc.) au lieu du
bleu-gris technique précédent, accent orange conservé (`--accent`,
retouché légèrement vers `#e8622a`), léger grain de papier (bruit SVG
en superposition sur le fond du `body`). Seul le thème CLAIR (`:root`)
a été retouché — le mode sombre (`prefers-color-scheme` et
`[data-theme="dark"]`) n'a pas été touché, faute de référence fournie
pour cette variante : il reste bleu-nuit. Si l'utilisateur demande un
jour un mode sombre cohérent avec le nouveau thème papier, il faudra
concevoir une palette sombre chaude (brun foncé plutôt que bleu marine)
plutôt que de réutiliser l'ancienne.

## Note technique importante pour les prochains objets détaillés

Les engrenages génériques (`gearGroup()`) tournent nativement autour
de l'axe **Z** (leur face est dans le plan XY). Pour un mécanisme où
une roue doit tourner autour d'un axe **vertical (Y)** — typique pour
tout ce qui "serre" ou "entraîne" un objet cylindrique debout (boîte,
tambour, etc.) — il faut l'envelopper dans un groupe, appliquer une
rotation STATIQUE `.rotation.x = Math.PI/2` sur l'engrenage lui-même
(une seule fois, à la construction), puis animer la rotation
DYNAMIQUE via `.rotation.y` sur le groupe englobant. Voir
`verticalGear()` dans `DetailedObjects.ouvreBoite` pour l'implémentation
de référence. Une roue simple (`cyl()`, pas un gearGroup) a déjà son
axe par défaut en Y — pas besoin de cette gymnastique pour un simple
disque/poulie/roue lisse.

Leçon (perceuse sans fil, retouche du carter) : quand on retravaille
l'habillage visuel STATIQUE d'un objet existant (LatheGeometry,
ExtrudeGeometry pour une silhouette plus réaliste), vérifier les
rayons/dimensions de TOUTES les pièces mécaniques fixes que ce
carénage doit envelopper — pas seulement la pièce qu'on modifie. Un
carter qui semble correct isolé peut quand même laisser dépasser une
pièce interne (moteur, ailettes) si son profil descend, même
localement, sous le rayon réel de cette pièce. Vérifier par un dump
des rayons/dimensions de chaque maillage (pas seulement une capture
d'écran), comme fait pour ce correctif. Attention aussi : contrairement
à `CylinderGeometry`, `LatheGeometry` ne ferme PAS automatiquement son
extrémité si le dernier point du profil n'a pas un rayon de 0 — ça
peut rester invisible tant qu'une autre pièce masque l'ouverture, puis
apparaître comme un trou béant dès que cette pièce s'écarte (vue
éclatée). Ajouter un disque de fermeture si besoin.

Autre leçon (sécheuse) : pour un appareil normalement fermé (électro-
ménager, boîtier), NE PAS construire un carénage plein qui englobe le
mécanisme — ça le rend invisible, ce qui va à l'encontre du but de
l'outil. Construire plutôt un châssis ouvert (plaque de base, montants
d'angle fins, une paroi arrière au besoin, un dessus) qui suggère
l'appareil réel sans cacher les pièces mobiles.

**Piège critique à ne JAMAIS reproduire** (sécheuse, poulie moteur +
galet tendeur, corrigé le 2026-09-04) : NE JAMAIS appliquer une
inclinaison statique (`rotation.x`, typiquement via `zAxis()`) ET une
rotation dynamique animée (`rotation.z=angle` à chaque frame) sur le
MÊME objet mesh. L'ordre de composition des angles d'Euler 'XYZ' de
Three.js applique Z AVANT X — combiner les deux sur un seul objet fait
dériver l'axe de rotation réel au fil de l'animation, donnant un effet
"pièce de monnaie qui flip dans les airs" au lieu d'une roue qui tourne
normalement sur un axe fixe. C'est un bug SILENCIEUX visuellement
subtil : une rotation qui semble "à peu près correcte" en capture
d'écran statique peut être fausse en mouvement — seule une mesure de
l'axe réel dans le temps (direction locale Y du cylindre transformée
par `matrixWorld`, échantillonnée à plusieurs instants) le révèle de
façon fiable.
Solution obligatoire : séparer en groupe parent/enfant — inclinaison
statique sur le disque/mesh (enfant), rotation dynamique sur un groupe
englobant créé spécifiquement pour ça (parent), ex. `motorPulleySpin`,
`idlerSpin`. Déjà fait correctement ailleurs dans le fichier :
`motorGroup`/`motorBody` (sécheuse), `verticalGear()` (ouvre-boîte).
Un `boltRing()` (groupe de repères) est un exemple de bon pattern
existant : ses enfants ont chacun leur `zAxis()` statique, et c'est le
GROUPE qui reçoit la rotation dynamique — jamais l'inverse.

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

- Aucun bug connu actif sur les 5 objets détaillés à ce jour.
- Les 9 objets catalogue "mécanisme simple" ont été retirés de la
  banque affichée (2026-09-04) — plus à vérifier dans ce contexte.
- Attention à la perte de contexte du scratchpad entre sessions : le
  fichier de travail à `/tmp/...` peut disparaître. **Toujours partir
  de `index.html` dans le dépôt GitHub comme source de vérité**, pas
  du scratchpad, si le scratchpad semble absent ou périmé au début
  d'une nouvelle session.

## Prochaine action exacte (mise à jour)

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
