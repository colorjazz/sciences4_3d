# NIGHTLY_LOG

Journal des sessions de travail autonome. Nouvelle entrée à chaque
étape significative, la plus récente en haut.

---

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

---

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
