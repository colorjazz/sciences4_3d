# TODO — Banque d'objets 3D Sciences 4

Objectif : 30 objets techniques dans la banque, avec une préférence
forte pour la qualité (mécanismes réalistes, proportions correctes,
vue éclatée fonctionnelle, fiche pédagogique) plutôt que la quantité.

## Structure de l'app (2026-09-04, mise à jour)

L'app a maintenant un écran d'accueil avec deux choix :
- **Comprendre** : contenu théorique général (mécanismes, liaisons et
  guidages, schématisation, matériaux et contraintes, fonctions
  électriques), indépendant des objets — voir CURRENT_TASK.md.
- **Tester** : la banque d'objets 3D ci-dessous, renommée **« L'Atelier »**
  dans l'interface (remplace l'ancien libellé « Banque d'objets »).

Les 9 objets "mécanisme simple" (section ci-dessous, maintenant tous
marqués "retiré") ont été retirés de la banque affichée à la demande
de l'utilisateur — moins riches pédagogiquement qu'un objet détaillé à
mécanismes couplés. Le moteur générique (`Mechanisms.*`) qui les
alimentait N'A PAS été supprimé du code : il reste utilisé par l'onglet
« Atelier — générer » (constructeur de mécanisme personnalisé, une
fonctionnalité distincte de la banque vitrine).

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
- [x] Perceuse électrique sans fil (`perceuseSansFil`, 2026-10-17) —
      REMPLACE l'ancienne perceuse-visseuse à réducteur planétaire, à la
      demande de l'utilisatrice, par son modèle « perceuse.txt » (style
      cahier d'examen MEQ) : 13 pièces numérotées avec nomenclature,
      4 mécanismes couplés (circuit électrique, engrenage droit Z10→Z30,
      arbre + roulements, mandrin à liaison hélicoïdale).
- [x] Roadster électrique volant (`roadster`, 2026-10-17) — modèle
      d'examen fourni (« tesla.html »), 4 mécanismes (propulsion par
      engrenage Z12→Z36, direction pignon-crémaillère + biellettes,
      compresseur bielle-manivelle, électrovanne + tuyères), physique
      corrigée (voir NIGHTLY_LOG).
- [x] Piano à queue (`piano`) — 5 mécanismes couplés (touche-levier,
      pilote et bascule, échappement et marteau, levier d'étouffoir,
      pédale forte). Portage fidèle du modèle Blender du dépôt
      colorjazz/bombe (`piano/piano_a_queue.py`) : mêmes cotes et mêmes
      rapports de leviers, 88 notes. Géométrie générée par le code (pas
      de fichier 3D) ; pièces répétées en InstancedMesh (~70 commandes
      de dessin au lieu de ~1 300).

## Objets catalogue génériques — RETIRÉS de la banque (2026-09-04)

Ces 9 objets utilisaient un seul `Mechanisms.*` générique directement
(pas un `DetailedObjects` bespoke). Retirés du `CATALOGUE` affiché à la
demande explicite de l'utilisateur (ménage) — moins riches
pédagogiquement qu'un objet "détaillé" à mécanismes couplés. Le code
`Mechanisms.*` sous-jacent existe toujours et reste utilisé par
l'onglet « Atelier — générer ».

- [x] ~~Taille-crayon manuel (`taille-crayon`, gear)~~ — retiré
- [x] ~~Perceuse à manivelle (`perceuse-manivelle`, bevel)~~ — retiré
- [x] ~~Lève-vitre électrique (`leve-vitre`, worm)~~ — retiré
- [x] ~~Store vénitien à enrouleur (`store-venitien`, pulleyLoad)~~ — retiré
- [x] ~~Cric de voiture à vis (`cric-voiture`, screw)~~ — retiré
- [x] ~~Essuie-glace d'auto (`essuie-glace`, crankRocker)~~ — retiré
- [x] ~~Scie sauteuse électrique (`scie-sauteuse`, crankSlider)~~ — retiré
- [x] ~~Portail motorisé à crémaillère (`portail`, rack)~~ — retiré
- [x] ~~Batteur électrique (`batteur`, mixerCombo)~~ — retiré

Si un de ces principes mérite d'être ré-introduit, le faire comme un
vrai `DetailedObjects` à mécanismes couplés (voir liste ci-dessous),
pas en le remettant simplement dans le `CATALOGUE` sous forme
générique.

## Prochains objets à créer (viser ~25 de plus pour atteindre 30)

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

- [ ] Ajouter repères numérotés + nomenclature au vélo et à l'ouvre-boîte
      (déjà faits : piano, perceuse à colonne, perceuse sans fil, sécheuse)
- [ ] Vérifier la vue éclatée sur tous les objets `DetailedObjects`
- [ ] Vérifier la fiche technique / mini-aperçus sur tous les objets
- [ ] Optimiser les performances si le nombre d'objets détaillés
      animés simultanément devient lourd (30 objets × mini-viewers)
- [ ] Repasser sur les 2 objets détaillés existants après un moment
      pour rattraper d'éventuelles régressions visuelles
