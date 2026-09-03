# TODO — Banque d'objets 3D Sciences 4

Objectif : 30 objets techniques dans la banque, avec une préférence
forte pour la qualité (mécanismes réalistes, proportions correctes,
vue éclatée fonctionnelle, fiche pédagogique) plutôt que la quantité.

## Fait (objets "DetailedObjects" — qualité cible)

- [x] Vélo (`velo`) — 5 mécanismes couplés (transmission, roue libre,
      direction, frein à disque, dérailleur). Chaîne, guidon, selle,
      frein tous corrigés et vérifiés visuellement.
- [x] Perceuse à colonne (`perceuse`) — 5 mécanismes couplés (poulies
      étagées, avance par crémaillère, ressort de rappel, vis de
      table, mandrin à trois mors).
- [x] Ouvre-boîte électrique (`ouvreBoite`) — 2 mécanismes couplés
      (réducteur à poulies/courroie, levier de serrage). Vérifié par
      mesure de bounding-box 3D (pas juste visuel) que la molette
      d'entraînement et la molette coupante touchent bien la boîte.
- [x] Sécheuse à linge (`secheuse`) — 2 mécanismes couplés
      (entraînement du tambour par courroie, loquet de porte à came).
      Châssis ouvert (pas de carénage plein) pour garder le mécanisme
      visible.

## Objets catalogue existants (mécanisme générique simple — à évaluer)

Ces 9 objets utilisent un seul `Mechanisms.*` générique directement
(pas un `DetailedObjects` bespoke). Ils fonctionnent mais sont moins
riches pédagogiquement qu'un objet "détaillé" à mécanismes couplés.

- [ ] Taille-crayon manuel (`taille-crayon`, gear)
- [ ] Perceuse à manivelle (`perceuse-manivelle`, bevel)
- [ ] Lève-vitre électrique (`leve-vitre`, worm)
- [ ] Store vénitien à enrouleur (`store-venitien`, pulleyLoad)
- [ ] Cric de voiture à vis (`cric-voiture`, screw)
- [ ] Essuie-glace d'auto (`essuie-glace`, crankRocker)
- [ ] Scie sauteuse électrique (`scie-sauteuse`, crankSlider)
- [ ] Portail motorisé à crémaillère (`portail`, rack)
- [ ] Batteur électrique (`batteur`, mixerCombo)

Décision : les garder comme objets catalogue "mécanisme simple" est
acceptable pédagogiquement (ils illustrent un principe unique
clairement). Les convertir en `DetailedObjects` seulement si le temps
le permet, en priorité pour ceux avec plusieurs pièces réelles
identifiables (essuie-glace, cric, portail).

## Prochains objets à créer (viser ~19 de plus pour atteindre 30)

Candidats réalistes pour Sciences 4 (mécanismes-machines), par ordre
de priorité suggéré :

- [ ] Robinet mélangeur (vis + came ou simple rotation→translation)
- [ ] Ciseaux de jardin à cliquet (cliquet-rochet)
- [ ] Serrure à pêne (came + ressort)
- [ ] Grille-pain (levier + ressort + électroaimant simplifié)
- [ ] Store à lames orientables (tige de commande + biellettes)
- [ ] Presse-agrumes électrique (moteur + réducteur)
- [ ] Roue à aubes / moulin (came multiple)
- [ ] Chaise de bureau à vérin à gaz (approx. vis/piston)
- [ ] Trottinette pliable (charnière + verrou à came)
- [ ] Compas de précision (vis de réglage)
- [ ] Presse à salade (bielle-manivelle)
- [ ] Store à enrouleur motorisé (déjà couvert par store-venitien —
      envisager plutôt un volet roulant à lames à crémaillère)
- [ ] Poulie à moufle (avantage mécanique, poulies multiples)
- [ ] Différentiel de jouet RC (engrenages coniques multiples)
- [ ] Boîte de vitesses simplifiée à baladeur
- [ ] Essoreuse à salade (manivelle + engrenages + roue libre)
- [ ] Store de fenêtre à manivelle (vis sans fin + crémaillère)

À ajuster/affiner au fil du développement — remplacer par des idées
plus fortes si une meilleure illustre mieux un mécanisme du programme.

## Tâches transverses

- [ ] Vérifier la vue éclatée sur tous les objets `DetailedObjects`
- [ ] Vérifier la fiche technique / mini-aperçus sur tous les objets
- [ ] Optimiser les performances si le nombre d'objets détaillés
      animés simultanément devient lourd (30 objets × mini-viewers)
- [ ] Repasser sur les 2 objets détaillés existants après un moment
      pour rattraper d'éventuelles régressions visuelles
