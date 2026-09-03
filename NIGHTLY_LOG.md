# NIGHTLY_LOG

Journal des sessions de travail autonome. Nouvelle entrée à chaque
étape significative, la plus récente en haut.

---

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
