# 01 - Vision et cadrage produit

[← Retour au sommaire](../README.md)

## La promesse

**Une maquette qu'on a le droit de casser.** Le joueur survole un monde miniature en voxels, à l'échelle réelle, et tout ce qu'il touche se découpe en blocs. Le genre (shoot'em up twin-stick roguelite) est connu ; ce qui le distingue, c'est que le décor est la cible.

| | |
|---|---|
| Genre | Shoot'em up twin-stick roguelite, vue isométrique, défilement en diagonale |
| Public | Joueurs d'action de 12 ans et plus, habitués des roguelites ; pas un « bullet hell » pur |
| Plateformes | PC (clavier et souris, manette) d'abord, Steam Deck ensuite ; version web pour la démonstration |
| Session | Un secteur dure 75 à 100 s ; une sortie complète, une demi-heure ; première mort vers 5 à 8 min |
| Références | *Viewpoint*, *Zaxxon* (iso, ombre d'altitude) · *Xevious* (air et sol) · *Teardown* (destruction en voxels) · *Hades*, *Slay the Spire*, *Nova Drift* (structure, builds) |

## Cinq piliers, qui disent aussi ce qu'on refuse

1. **Une maquette qu'on a le droit de casser.** On refuse les décors intouchables et les explosions qui ne laissent pas de trace.
2. **Lisible d'abord.** Les tirs ennemis ont une couleur réservée, chaque objet volant a son ombre, un seul plan de collision aérien. Aucun effet ne passe avant la lisibilité.
3. **Viser le sol est un plaisir, pas une corvée.** Une seule arme pour l'air et le sol, un réticule posé sur le décor, le jeu résout la hauteur. On refuse le double système de tir.
4. **Chaque choix change la façon de jouer.** Une amélioration modifie un comportement ou se paie par une contrepartie. On refuse le « +10 % de dégâts ».
5. **Le monde justifie la boucle.** L'ennemi réimprime sa flotte en blocs chaque nuit : c'est pour cela que tout est en voxels et que la carte change à chaque sortie.

Un pilier qui ne dit pas ce qu'il refuse ne sert pas à trancher. Ces cinq-là ont tranché des dizaines de demandes des agents (« faut-il un tir séparé pour le sol ? », « cette amélioration à +15 % passe-t-elle ? »).

## La boucle de jeu

```
viser et détruire → ferraille et noyaux → choix d'un module → secteur plus dense → boss → hangar → nouvelle sortie
```

- **Secondes :** piloter, viser, tirer, esquiver, découper.
- **Minutes :** finir un secteur, choisir un module parmi trois, repartir.
- **Entre les parties :** le hangar, où l'on dépense les noyaux pris aux boss en améliorations définitives, et l'expérience de chaque appareil.

## Jalons à critère de fin

Chaque jalon dit **quand** il est atteint, pas seulement ce qu'il contient. C'est ce qui permet à un agent de savoir s'il a fini.

| Jalon | Fait quand | État |
|---|---|---|
| M0, preuve du look | une capture moteur montre avion, char, vedette et décors à l'échelle | fait le 17/11 |
| M1, secteur jouable | un secteur de 75 s se joue du début à la fin | fait le 17/11, d'abord au pilote automatique |
| M2, zone complète | une partie se termine par une victoire ou une défaite, et le hangar s'ouvre | atteint : une sortie va jusqu'au porte-avions, puis le hangar |
| M3, boucle roguelite | trois parties d'affilée donnent trois builds différents | en place : modules à quatre raretés et six familles, hangar, sauvegarde |
| M4, contenu | biomes 2 à 4, carte à embranchements, niveaux d'alerte | quatre pays et trois boss faits ; embranchements et niveaux d'alerte à venir |
| M5, finition | audio, accessibilité, performance, build de sortie | en cours : musiques, voix, versions web et bureau |

## Un journal de décisions, pas un historique de tâches

39 décisions sont tracées dans le GDD. Chacune a sa date, les alternatives envisagées, la raison et les documents qu'elle touche. Quelques exemples :

| Décision | Alternatives écartées | Pourquoi |
|---|---|---|
| Les modèles viennent de maillages 3D texturés voxélisés, pas de géométrie codée à la main | sculpture en code, modélisation voxel à la main | test comparatif du jour : le char codé à la main reste un pavé, le même char voxélisé a sa tourelle, ses jupes et son camouflage |
| Trois tailles de voxel en rapports entiers : 12,5 cm, 25 cm, 50 cm | 10 / 25 / 50 cm ; tout à 50 cm | à 50 cm un char fait 20 voxels ; 10 → 25 n'est pas un rapport entier |
| Tirs ennemis en magenta à cœur blanc, couleur interdite partout ailleurs | rouge, orange | l'orange est la couleur des explosions et du sable |
| Trois armes : canon illimité, missiles sur recharge, bombes en munitions | tout en munitions ; tout en recharge | trois gestes distincts ; la rareté des bombes en fait une décision |
| Les grands décors partent d'une référence réelle mesurée et dépassent l'écran | formes inventées à l'échelle de l'écran | retour de partie : le premier barrage (22 m) n'avait rien de grandiose ; il en fait 108 aujourd'hui |
| Altitude de vol portée de 28 m à 38 m | rester à 28 m ; 60 m | après la première partie à la main, l'avion volait « au ras des palmiers » ; à 38 m il est à 38 % de l'écran de son ombre, à 60 m il en serait trop loin pour viser |

Le journal sert de mémoire au projet : un agent qui arrive le lit avant de proposer ce qui a déjà été essayé et écarté.

## Mesurer plutôt qu'estimer

Une sortie devait durer une demi-heure. Plutôt que de l'estimer, un test fait voler la sortie entière à un pilote automatique invulnérable, une fois par appareil, sans aide aux boss : 30,8 minutes au chasseur sur trois pays, escales comprises. Quand le quatrième pays est arrivé, la durée a été recalculée à partir de ces temps mesurés (37 minutes), et la décision de garder des secteurs de 1 200 m a été prise en connaissance de cause.

Même logique pour la lisibilité : l'altitude, la plongée de la caméra et la taille des ouvrages se raisonnent en pourcentage de la hauteur d'écran visible (environ 78 m), pas en impression.

## Le récit au service de la boucle

Le scénario ne décore pas : il explique pourquoi le monde est en blocs et pourquoi il change. Le Fondeur, une usine détraquée, réimprime sa flotte chaque nuit. Le ressort comique (la machine fait la cour à Roxy en lui fabriquant des ouvrages de plus en plus démesurés) justifie que chaque boss soit plus grand que le précédent. Les répliques radio sont conditionnelles (elles réagissent à ce que le joueur vient de faire) et doublées en deux langues.

[Suivant : 02 - Piloter des agents IA →](./02-piloter-des-agents-ia.md)
