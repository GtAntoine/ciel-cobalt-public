# 03 - Technique

[← Retour au sommaire](../README.md)

## Vue d'ensemble

| Couche | Choix |
|---|---|
| Moteur | Unity 6 (6000.6), URP 17 Forward+, C# |
| Scène | Tout se construit par code : la scène ne contient qu'un objet de démarrage, aucune référence sérialisée à maintenir, tout se teste en ligne de commande |
| Rendu | Un seul matériau URP Lit pour tout le jeu ; couleur et occlusion lues dans un atlas de palette 1024 × 4 |
| Assets | Chaîne Python `VoxelForge` : maillage 3D texturé → voxels à taille réelle → format compact `VXG2` lu par Unity |
| Monde | Secteurs générés par graine, en passes successives |
| Eau | Simulation en compute shader sur la carte graphique, repli en cubes quand la carte ne suit pas |
| Cibles | Windows, macOS, navigateur (WebGL 2) |

## De la référence au voxel

Décision fondatrice du projet : les modèles viennent de maillages 3D texturés voxélisés, pas de géométrie codée à la main. Le test du premier jour l'a tranché : un char codé à la main restait un pavé, le même char voxélisé avait sa tourelle, ses jupes et son camouflage.

1. **Source** : une planche de référence validée, puis un maillage texturé (généré dans Tripo3D, modèle Sketchfab sous licence, ou kit CC0).
2. **Voxélisation** à taille réelle, avec trois tailles de voxel en rapports entiers : 12,5 cm pour les unités, 25 cm pour les grands navires, bâtiments et la flore, 50 cm pour le terrain.
3. **Retouche** : palette du jeu, couleurs réservées, exagération des traits d'identité.
4. **Export** au format `VXG2` : un en-tête, puis la grille compressée (un index de palette par cellule). Le premier chasseur (110 × 43 × 158 voxels) pesait 28 ko ; les 33 premiers modèles, 770 ko.

## Maillage et rendu

- **Fusion gloutonne des faces** avec occlusion ambiante par sommet : deux faces fusionnent si elles ont la même couleur et les mêmes quatre valeurs d'occlusion aux coins ; la diagonale de chaque quad passe par la paire de coins la plus sombre.
- **Un seul matériau** pour tout le jeu : couleur et occlusion passent par les UV vers l'atlas de palette. Ombres, SSAO et lumières multiples fonctionnent sans shader maison ; ça a marché à la première capture.
- **Un maillage par modèle**, partagé par toutes ses instances.

## Destruction

Principe : **une instance ne copie sa grille que lorsqu'elle est touchée.**

| Étape | Règle |
|---|---|
| Impact | retirer les voxels dans une sphère, noircir une couronne de suie |
| Débris | un cube pour 4 à 8 voxels retirés, de la couleur exacte du voxel, simulés en lot et dessinés par instanciation |
| Re-maillage | seulement les morceaux de 32³ touchés |
| Budget | 2 ms de re-maillage par image au plus ; le reste attend l'image suivante |
| Mort | l'entité éclate en 6 à 12 blocs balistiques, puis laisse une épave assombrie ou coule |
| Collisions | aucun collider de maillage : boîte englobante, puis test dans la grille |

Les grands ouvrages s'effondrent en morceaux rigides (travées de pont, tours), plafonnés pour tenir le budget. Les réactions en chaîne se propagent : dépôts qui explosent l'un après l'autre, incendies de forêt qui gagnent de proche en proche, arbres qui écrasent ce qui est dessous.

## Génération par graine

Un secteur est une bande de 1 200 m générée en passes : relief, eau, routes, points d'intérêt, flore, ennemis. Cinq garanties encadrent le générateur :

1. **La ligne de vol passe toujours** : tracée avant tout le reste, avec un couloir sans relief haut.
2. **Rien ne flotte** : chaque bâtiment aplanit son emprise et descend ses fondations jusqu'au relief.
3. **Routes et rivières sont continues** : s'il croise l'eau ou un ravin, le tracé pose un pont ancré sur les deux berges.
4. **Un pays a un écosystème fermé** : flore, roches, architecture et ennemis viennent de sa table.
5. **Même graine, même secteur** : chaque passe tire dans sa propre sous-graine, modifier la flore ne déplace pas les routes.

Les grands décors (barrage, volcan, citadelle, canyon) ne sont pas posés sur une dalle : le relief est généré autour de leur plan au sol, haut du côté opposé à la caméra et bas de son côté, ce qui les montre en coupe.

## L'eau sur la carte graphique

Gerbes, colonnes d'eau, couronnes de jets, pluie qui retombe, écume et brume sont simulées en compute shader. Une bombe en mer soulève une colonne de 56 m, puis une couronne de jets, puis une pluie qui laisse de l'écume là où chaque paquet retombe. Les réglages suivent une planche de référence (paquets en cubes de la palette, embruns, ombre de l'eau turquoise, jamais grise).

Quand la carte ne suit pas (WebGL 2 n'a pas de compute shaders), chaque effet renvoie faux et l'appelant garde l'ancien rendu en cubes : la version web reste jouable partout.

## Combat et lisibilité

- **Un seul plan de collision aérien**, qui suit le relief : avion, ennemis, tirs et caméra montent et descendent ensemble, à distance constante du sol survolé.
- **Une seule arme pour l'air et le sol** : le réticule est posé sur le décor et le jeu résout la hauteur.
- **Couleur réservée** : les tirs ennemis sont magenta à cœur blanc, une couleur interdite partout ailleurs.
- **Trois gestes** : canon illimité, missiles sur recharge marqués cible par cible, bombes rares ; un tonneau pour passer entre les tirs.

## Tests et outillage

- **150 tests** dans 63 fichiers, dont 142 joués dans le moteur avec un pilote automatique : durée d'une sortie, équité des boss, convois, ponts, avalanches, incendies, coopération, eau, menus, sauvegarde.
- **Unity sans interface** : compilation, parties de test et captures en ligne de commande, y compris pendant que l'éditeur est ouvert ailleurs (copie miroir du projet).
- **Rendu hors Unity** : un outil Python dessine un secteur sans lancer le moteur, pour juger un décor en quelques secondes.
- **Builds en ligne de commande** : Windows, macOS et WebGL, avec le nom, la version et l'icône du jeu écrits avant chaque build.

## Audio

- **61 musiques** composées avec Suno, une par pays et par boss avec variantes, choisies d'après un goût écrit noir sur blanc (mémorable, 115 à 128 BPM, moderne ; refusés : militaire lourd, trop rapide, jouet).
- **246 bruitages**, plusieurs prises par son pour éviter la répétition.
- **189 répliques** doublées en français et en anglais, générées depuis le texte du scénario par un script.

[← Précédent : 02 - Piloter des agents IA](./02-piloter-des-agents-ia.md) · [Suivant : 04 - Mise sur le marché →](./04-mise-sur-le-marche.md)
