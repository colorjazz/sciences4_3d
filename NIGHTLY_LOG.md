# NIGHTLY_LOG

Journal des sessions de travail autonome. Nouvelle entrée à chaque
étape significative, la plus récente en haut.

---

## 2026-09-04 (suite 3) — Restructuration : accueil Comprendre/Tester, « L'Atelier », thème papier

- L'utilisateur a demandé un ménage dans l'app (image des 9 objets à
  retirer), un renommage de la banque en « L'Atelier », un nouvel
  écran d'accueil à deux choix (Comprendre / Tester) et un nouveau
  thème visuel (texture/couleur papier fournie en image). Clarifié par
  AskUserQuestion avant de coder : retirer aussi le Batteur électrique
  (10e objet, jugé aussi "schématique" par l'utilisateur) ; le contenu
  de "Comprendre" doit être général/indépendant des objets (pas ancré
  sur des pièces précises) ; la texture guide TOUTE l'app, pas
  seulement l'accueil.
- Ménage du `CATALOGUE` : retiré les 9 objets génériques (taille-crayon,
  perceuse-manivelle, lève-vitre, store-vénitien, cric-voiture,
  essuie-glace, scie-sauteuse, portail, batteur) — gardé les 5 objets
  `DetailedObjects` (vélo, perceuse-colonne, ouvre-boîte, sécheuse,
  perceuse-sans-fil). Le code `Mechanisms.*` sous-jacent N'A PAS été
  supprimé : vérifié qu'il reste utilisé par l'onglet « Atelier —
  générer » (constructeur de mécanisme personnalisé), une
  fonctionnalité séparée du catalogue vitrine — décision prise après
  avoir lu ce code, pas par supposition.
- Nouvel écran d'accueil (`#homeScreen`) avec deux cartes ; l'app
  originale (rail + scène 3D + fiche technique) déplacée dans
  `#atelierScreen` et renommée « L'Atelier » (`<h2>` du rail) ; nouveau
  contenu théorique dans `#comprendreScreen` (5 sujets : mécanismes,
  liaisons et guidages, schématisation, matériaux et contraintes,
  fonctions électriques — avec petits schémas SVG pour les guidages,
  la torsion/le cisaillement/la flexion, et principe-vs-construction).
- Piège de mise en page rencontré et corrigé : envelopper
  `header.titleblock` + `.workspace` dans `#atelierScreen` a cassé le
  flex-layout du `body` (qui comptait sur eux comme enfants directs) —
  corrigé en donnant à `#atelierScreen` son propre
  `display:flex;flex-direction:column`. Deuxième piège : le canvas
  Three.js reste à taille 0 tant que l'écran est masqué
  (`display:none`) — `showScreen()` appelle donc `resizeRenderer()` et
  `resizeWorkshop()` explicitement à chaque passage vers l'atelier.
- Nouveau thème : palette beige/crème chaude à partir de l'image
  fournie, accent orange conservé (déjà harmonieux sur fond crème),
  grain de papier ajouté en superposition sur le fond (bruit SVG en
  data-URI, alpha faible). Mode sombre non retouché (aucune référence
  fournie pour cette variante).
- Vérifié par Playwright : navigation complète (accueil ↔ tester ↔
  comprendre, les 5 sujets), banque affichant 5/30 avec exactement les
  5 bons objets, aucune erreur JS, rendu testé aussi en largeur mobile
  (390px). Diagrammes SVG (torsion, cisaillement, flexion) affinés
  après une première passe jugée peu claire visuellement.
- Commit poussé : `7f96e45` sur `colorjazz/sciences4_3d`.

## 2026-09-04 (suite 2) — Perceuse-visseuse sans fil : nouveau carter inspiré d'une référence testée

- L'utilisateur a fourni un PDF contenant un visualiseur Three.js
  autonome ("Perceuse Pro 3D", r160, modules ES, CDN unpkg) et a
  demandé de le tester. Reconstruit le HTML complet à partir du texte
  extrait du PDF, testé par Playwright (import map réécrit vers une
  copie locale de three@0.160.0 + petit serveur HTTP local, car les
  modules ES ne se chargent pas depuis `file://` — CORS — et unpkg.com
  n'est pas joignable depuis ce bac à sable). Résultat : aucune erreur
  JS ; gâchette (accélération/décélération), sens FWD/REV/LOCK, vue
  éclatée (lerp), et inspection par raycasting (nom + matériau de la
  pièce cliquée) fonctionnaient tous correctement.
- L'utilisateur a ensuite demandé d'intégrer ça à l'atelier et de
  remplacer la perceuse existante. Le fichier testé est incompatible
  tel quel (modules ES + CDN externe vs script classique r128 sans
  modules de l'atelier) — même décision qu'une session précédente pour
  un visualiseur PBR statique : pas réutilisable comme code, mais ses
  techniques de modélisation (LatheGeometry pour un carter bombé,
  ExtrudeGeometry pour une poignée/batterie profilées) ont servi à
  retravailler `DetailedObjects.perceuseSansFil` existant, en gardant
  intacts ses deux mécanismes réels (réducteur planétaire, mandrin
  auto-serrant) — seul l'habillage visuel statique (buildStructure())
  a changé.
- Deux bugs trouvés et corrigés par mesure directe des rayons de
  chaque maillage (pas seulement visuellement) :
  1. Le nouveau carter (Lathe), en s'effilant aux extrémités, passait
     par endroits sous le rayon réel du moteur/des ailettes (cylindre
     fixe de buildReducer, rayon constant 0.10) : le moteur dépassait
     du carter. Corrigé en gardant le profil toujours >= ce rayon,
     l'effilement du bout arrière se faisant désormais en z négatif,
     derrière le capot du moteur plutôt que dedans.
  2. La bague du réducteur (gearboxR, purement décorative, jamais
     animée) était nettement plus large (0.135) que le nouveau carter,
     créant un renflement disproportionné. Réduite à 0.116.
  3. LatheGeometry ne ferme pas automatiquement son extrémité avant
     (contrairement à CylinderGeometry) : invisible tant que la bague
     du réducteur la masque, mais un trou béant apparaissait en vue
     éclatée. Ajout d'un disque de fermeture (housingCap).
- Vérifié : aucune erreur JS (chargement, lecture, éclaté 100 %/0 %),
  comparaison capture d'écran avant/après le carter original.
- Commit poussé : `76a7c40` sur `colorjazz/sciences4_3d`.
- Leçon ajoutée à CURRENT_TASK.md : quand on retravaille l'habillage
  visuel statique d'un objet existant, vérifier les rayons/dimensions
  de TOUTES les pièces mécaniques fixes qu'il doit envelopper (pas
  seulement celle qu'on modifie) — un carter qui semble correct isolé
  peut quand même laisser dépasser une pièce interne si on ne compare
  pas les rayons réels.

## 2026-09-04 (suite) — Sécheuse : correction définitive de l'axe de rotation poulie/galet

- Après plusieurs allers-retours (moteur qui traversait le mur arrière,
  courroie mal alignée en Z, tambour transparent/plein), l'utilisateur
  a signalé un dernier bug persistant sur la poulie moteur et le galet
  tendeur : "elle doivent rouler avec la courroie et non tourner", puis
  précisé de façon décisive via AskUserQuestion : "ça ne tourne pas
  comme une roue qui roule. Ils tournent comme si je faisais 'flip' une
  pièce de monnaie dans les airs."
- Diagnostic par mesure directe (pas par inspection visuelle) : script
  Playwright échantillonnant l'axe de symétrie natif du cylindre
  (direction locale Y transformée par `matrixWorld`) à 6 instants
  espacés de 150ms. Résultat AVANT correctif : l'axe balayait tout le
  plan XZ (ex. [-0.57,0,-0.82] → [0.71,0,-0.71] → [0.87,0,0.49] → ...)
  au lieu de rester fixe — preuve directe du bug "pièce qui flip".
- Cause racine identifiée : `motorPulley` et `idler` avaient chacun
  `rotation.x` (statique, via `zAxis()`) ET `rotation.z` (dynamique,
  animée dans `update()`) appliqués sur le MÊME objet mesh. L'ordre de
  composition Euler 'XYZ' de Three.js applique Z avant X — combiner les
  deux sur un seul objet fait dériver l'axe réel de rotation au fil de
  l'animation.
- Correctif : chaque poulie enveloppée dans un nouveau groupe parent
  (`motorPulleySpin`, `idlerSpin`) qui reçoit la rotation dynamique
  Z ; l'inclinaison statique reste sur le disque (enfant), comme le
  pattern déjà correct de `motorGroup`/`motorBody` et `verticalGear()`.
- Vérifié : re-mesure du même axe après correctif → EXACTEMENT constant
  à (0,0,1) sur les 6 échantillons, pour les deux pièces. Vue éclatée
  et rendu retestés sans erreur JS (aucune régression, l'explode
  fonctionne toujours par `userData.explodeDir` au niveau des groupes
  wrapper, pas affecté par l'ajout d'un niveau d'imbrication interne).
- Commit poussé : `e06391c` sur `colorjazz/sciences4_3d`.
- Note technique ajoutée à CURRENT_TASK.md : ne jamais combiner
  `rotation.x` statique et `rotation.z` dynamique sur le même objet ;
  toujours séparer en groupe parent (dynamique) / enfant (statique).

## 2026-09-04 — Vélo : dérailleur retravaillé (proposition Gemini)

- L'utilisateur a soumis une proposition de Gemini pour retravailler
  le dérailleur du vélo. Analysée avant adoption plutôt qu'appliquée
  aveuglément : les galets suivent maintenant dynamiquement le rayon
  du pignon engagé (position Y = axleY - cassetteR - offset, pas
  seulement Z comme avant), le corps du dérailleur est un lien
  télescopique orienté par lookAt+scale, et la chaîne trace un vrai
  "S" croisé (cassette→plateau→galet bas→galet haut→cassette).
  Amélioration réelle, vérifiée visuellement (rendu isolé chaîne en
  vert : tracé tangent aux 4 pignons/galets) et par vue éclatée.
- Régression repérée AVANT de committer : le code proposé
  réintroduisait l'extension de potence à 0.40 (déjà corrigée à 0.05
  dans une session précédente). Remis à 0.05 avant de pousser, sinon
  le "cou de girafe" serait revenu.
- brakePivotY simplifié à une valeur fixe (0.78) cohérente avec le
  frein à disque (plus besoin d'un pont remontant à la jante).
- Commit poussé : `6077aa4` sur `colorjazz/sciences4_3d`.
- Leçon : quand l'utilisateur fournit du code externe (Gemini ou
  autre) pour une pièce déjà corrigée dans une session précédente,
  TOUJOURS diffé/vérifier que les correctifs précédents (potence,
  couleur de chaîne, etc.) survivent avant de committer — un collage
  complet écrase silencieusement les fix antérieurs qui ne sont pas
  dans le nouveau code source.

## 2026-09-03 (suite 3) — Nouvel objet : perceuse-visseuse sans fil

- L'utilisateur a fourni un visualiseur PBR statique d'une perceuse
  18V (Three.js r154, modules ES, aucun mécanisme animé) en demandant
  si ça pouvait aider à la modélisation. Évalué honnêtement : pas
  réutilisable comme code (incompatible avec l'architecture r128/
  script classique de l'app, et statique donc sans valeur pour montrer
  un mécanisme), mais utile comme référence de proportions pour un
  objet qu'on n'avait pas encore : une perceuse-visseuse PORTATIVE
  (on avait déjà la perceuse à colonne et la perceuse à manivelle).
- Construit `DetailedObjects.perceuseSansFil` avec 2 mécanismes
  couplés : un réducteur PLANÉTAIRE (soleil + 3 satellites + couronne
  fixe, avec un vrai calcul de rapport épicycloïdal
  ratio=Zsoleil/(Zsoleil+Zcouronne)) — premier mécanisme de ce type
  dans la banque — et un mandrin auto-serrant (bague + spirale + 3
  mors, sur le même principe que celui de la perceuse à colonne).
- Bug rencontré : la fonction de mise à jour des mors du mandrin avait
  été laissée incomplète lors de la première écriture (un
  `forEach` vide, aucun déplacement radial réel appliqué). Repéré en
  relisant le code avant même de tester, corrigé pour que les mors
  s'écartent/se referment réellement selon le paramètre `grip`.
- Vérifié par mesure de bounding-box 3D (moteur, soleil, satellites,
  mandrin) que chaque pièce est positionnée le long de l'axe attendu,
  et par surlignage d'un maillage en vert pour confirmer visuellement
  quelle pièce correspond à quelle mesure quand un résultat semblait
  incohérent (l'écart mesuré venait d'une question de tessellation,
  pas d'un vrai bug de position — vérifié en confirmant que le rendu
  visuel était correct).
- Vue éclatée testée sans erreur (réducteur et mandrin se séparent
  proprement du corps).
- Commit poussé : `fa4346e` sur `colorjazz/sciences4_3d`. Banque :
  14/30 objets.

## 2026-09-03 (suite 2) — Nouvel objet : sécheuse à linge

- Construit `DetailedObjects.secheuse` avec 2 mécanismes couplés :
  entraînement du tambour (moteur → petite poulie → courroie qui fait
  presque le tour du tambour, celui-ci agissant comme une très grande
  poulie), et loquet de porte à came (poignée → came → pêne qui se
  rétracte → porte à charnière libérée).
- Premier essai : carénage plein (boîte fermée façon vraie sécheuse)
  → cachait complètement le tambour et l'entraînement, contraire à
  l'objectif pédagogique. Corrigé en remplaçant par un châssis ouvert
  (base, 4 montants d'angle, paroi arrière, dessus) — mécanisme
  visible de l'extérieur, cohérent avec le vélo/la perceuse/l'ouvre-
  boîte.
- Vue éclatée testée sans erreur (entraînement et loquet se séparent
  correctement).
- Commit poussé : `35c46de` sur `colorjazz/sciences4_3d`. Banque :
  13/30 objets.
- Note ajoutée à CURRENT_TASK.md sur le piège du carénage plein pour
  les prochains objets de type appareil fermé.

## 2026-09-03 (suite) — Nouvel objet : ouvre-boîte électrique

- Construit `DetailedObjects.ouvreBoite` avec 2 mécanismes couplés :
  un réducteur à poulies/courroie (moteur rapide → arbre lent) et un
  levier de serrage (pivot qui abaisse la molette coupante sur le
  rebord de la boîte).
- Bug rencontré en cours de route : les engrenages génériques
  (`gearGroup`) tournent nativement autour de Z, mais ce mécanisme a
  besoin de roues à axe vertical (Y) pour serrer une boîte debout.
  Première tentative : anime directement `.rotation.z` sur la roue
  positionnée sans reorientation → la roue tournait dans le mauvais
  plan (face à la caméra comme une horloge, pas comme une molette qui
  serre le côté de la boîte). Deuxième bug lié : le bras du levier
  utilisait `box(w,h,d)` avec la longueur au mauvais paramètre (d/Z
  au lieu de w/X), donnant un bras qui partait dans la mauvaise
  direction visuellement.
- Correctifs : fonction `verticalGear()` qui enveloppe l'engrenage,
  lui applique une rotation statique X=90° (une fois, à la
  construction) pour que sa face devienne horizontale, puis anime la
  rotation dynamique de l'enveloppe autour de Y. Bras du levier
  reconstruit avec la longueur dans le bon axe (X), et sa longueur +
  l'angle "fermé" calculés par trigonométrie (`atan2`) pour que la
  molette coupante atteigne exactement le point de contact visé sur
  le rebord de la boîte plutôt que par ajustement visuel approximatif.
- Vérifié PAR MESURE (bounding-box 3D de chaque maillage, pas
  seulement par capture d'écran) que la molette d'entraînement et la
  molette coupante touchent bien tangentiellement la boîte aux
  positions attendues. Vue éclatée testée, aucune erreur JS.
- Commit poussé : `f87fdd8` sur `colorjazz/sciences4_3d` (branche
  `main`). Banque : 12/30 objets.
- Enrichi CURRENT_TASK.md d'une note technique sur la réorientation
  des engrenages (axe Z natif → axe Y voulu), pour que les prochaines
  sessions n'aient pas à redécouvrir ce piège.

## 2026-09-03 — Session : correction finale du vélo + fusion d'une itération parallèle

- L'utilisateur a fourni le code d'une itération parallèle du vélo
  (développée ailleurs, ex. Replit) avec des améliorations : cadre
  remonté (tube de direction/selle plus hauts), roues affinées, frein
  remplacé par un frein à disque. Mais cette itération avait un bug
  signalé par l'utilisateur : "la chaîne n'est pas sur les
  engrenages".
- Diagnostic : leur nouvel algorithme de chaîne dessinait des arcs de
  cercle fixes (demi-cercle) autour de chaque pignon, indépendamment
  de la position réelle des pignons voisins — pas de calcul de
  tangente. Résultat : la chaîne ne touchait pas exactement les
  dents aux points de transition.
- Décision : fusionner leurs améliorations de cadre/roues/frein, MAIS
  garder mon algorithme de chaîne à tangentes (`beltPts`, 4 centres)
  déjà validé visuellement dans une session précédente.
- Recalculé l'extension du tube de direction (potence) : avec le
  nouveau cadre remonté, la garde de sécurité par rapport à la roue
  avant est déjà largement suffisante avec une extension de 0.05 (au
  lieu de 0.40 dans leur version, qui aurait donné une potence encore
  plus disproportionnée qu'avant).
- Vérifié par un test de rendu isolé (chaîne recolorée en vert) que
  le tracé passe bien tangent aux 4 pignons/galets (plateau,
  cassette, 2 galets de dérailleur).
- Piège rencontré : le fichier a DEUX déclarations `new
  THREE.Scene()` (scène principale + scène de chaque mini-aperçu de
  fiche technique). Un remplacement sed naïf pour instrumenter les
  tests a fait pointer la référence de debug vers la mauvaise scène,
  provoquant une fausse alerte ("la chaîne a disparu"). Corrigé en
  ciblant la scène principale via son marqueur unique (caméra avec
  far-plane 100).
- Commits poussés sur `colorjazz/sciences4_3d` (branche `main`) :
  `091d19b` (chaîne invisible + potence), `310e888` (fusion
  cadre/roues/frein à disque).
- Mise en place de l'infrastructure de reprise autonome
  (CURRENT_TASK.md, NIGHTLY_LOG.md, TODO.md) à la demande explicite
  de l'utilisateur, pour que le travail puisse continuer entre
  sessions sans dépendre de l'historique de conversation.

## (Sessions précédentes, résumées rétroactivement)

- Création initiale du vélo avec 5 mécanismes couplés
  (transmission, roue libre, direction, frein, dérailleur).
- Plusieurs correctifs itératifs : câble de frein manquant,
  dérailleur mal placé, guidon traversant la roue avant, chaîne
  ignorant le dérailleur (ligne droite au lieu de passer par les
  galets), sens de rotation inversé, échelle guidon/selle
  disproportionnée, mise en page mobile.
- Perceuse à colonne créée avec 5 mécanismes couplés (poulies
  étagées, avance à crémaillère, ressort spiral, vis de table,
  mandrin trois mors).
- Correctif de mise en page mobile (rail rétractable, viewport meta
  tag).
