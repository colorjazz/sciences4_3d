# NIGHTLY_LOG

Journal des sessions de travail autonome. Nouvelle entrée à chaque
étape significative, la plus récente en haut.

---

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
