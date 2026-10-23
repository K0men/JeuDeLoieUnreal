# JeuDeLoieUnreal

Goblin Adventure

Jeu type jeu de l'oie
On joue en 3ème personne, on joue un gobelin qui s'est fait enfermer dans un circuit par un génie après s'être moqué de lui. Le gobelin va devoir faire 5 tours en survivant à tous ses défis.

Idées de défis :
-Course d'obstacles
-Mini platformer
-Cache-cache
-Défis contre la montre

Le jeu doit mettre en place :
-Déplacement sur des cases avec des actions sur certaines cases. (= +2 cases, -3 cases, …)
-Actions de dialogues et / ou de quêtes. (interactions entre le gobelin et le génie)
-Actions déclenchant un mini jeu de cache-cache. (case d'un des défis)
-Actions bonus de collecte de ressources, achats d'objets par exemple. (à chaque fin de tour, le génie donne une vie supplémentaire, si le gobelin tombe exactement sur la case choisie au hasard par le génie, il pourra la récupérer et ainsi réessayer un défi si il meurt)


Unreal 

BP_Tile

Chaque case a un type (Normal, Bonus ou Malus) et une valeur. Quand le joueur s’arrête sur une case, on lui demande son décalage : 0 pour une case normale, + la valeur pour un bonus, - la valeur pour un malus. Le signe indique donc la direction.

Chaque cases possède deux variables : son type (TileType), qui peut être normal, bonus ou malus, et une valeur (Value), qui indique le nombre de cases concernées. Quand le joueur s’arrête sur une case, le jeu appelle sa fonction GetOffSet, qui renvoie un décalage. Selon le type, on obtient trois résultats : pour une case normale, zéro, donc rien ne se passe. Pour un bonus, la valeur telle quelle, donc un nombre positif, et le personnage avance. Pour un malus, la valeur multipliée par moins un, donc un nombre négatif, et le personnage recule. Le signe du résultat donne donc la direction, et sa taille donne le nombre de cases.

EXPLICATIONS DU CODE 


BP_Board

CUSTOM EVENT ROLLDICE : Quand le joueur appuie sur R on vérifie d’abord qu’un tour n’est pas déjà en cours, grâce à un verrou, et si c’est libre on bloque, on tire un nombre aléatoire entre 1 et 6 comme un vrai dé de 6, on le garde en mémoire pour que l’affichage et le déplacement utilisent exactement le même résultat, et on lance le déplacement du personnage de ce nombre de cases vers l’avant.


EVENT BEGIN PLAY : Le jeu commence, on récup le personnage du joueur et on le mémorise. On désactive ses déplacements normaux car c'est le plateaux qui va le déplacer sur les cases. On le téléporte sur la première case légèrement au-dessus du sol pour qu’il ne soit coincé dedans, on le met sur la case 0 et on le mémorise, on active la réception du clavier pour que la touche R puisse lancer le dé et commencer ROLLDICE.

ON ARRIVED : Quand le Player a fini de se déplacer on regarde la case où il est tombé et on lit son décalage. Si c’est zéro, ou si on a déjà appliqué un effet ce tour-ci, le tour est terminé et on débloque le dé pour bloquer la potentielle répétition et éviter le spam. Sinon on affiche un message de bonus ou de malus selon le signe, on fait une courte pause avec Delay, puis on relance le déplacement avec le même mécanisme : la valeur absolue donne le nombre de cases, et le signe donne la direction.

STEP FORWARD : L'event avance le personnage d’une case à la fois. S’il reste des cases à parcourir, on calcule l’index de la prochaine case, avec un modulo pour que le circuit reboucle, on en déduit sa position, et le personnage glisse jusqu’à elle en se tournant vers elle. Quand ce déplacement est terminé, l’évènement se rappelle lui-même avec une case de moins à parcourir. Quand il n’en reste plus, il passe la main à l’étape d’arrivée.

VARIABLES : Le plateau fonctionne avec 11 variables, qu’on range en 3 familles.
Les réglages : la liste des cases dans l’ordre du circuit (Tiles en ARRAY), la vitesse du personnage (MoveSpeed), sa hauteur par rapport au sol (HeighOffSet), et la durée de la pause avant un bonus ou un malus (PauseTime).
L’état du jeu : le personnage lui-même (PlayerRef), le numéro de la case où il se trouve (CurrentIndex), un verrou qui empêche de relancer le dé pendant qu’il marche (IsBusy), et un indicateur qui garantit qu’un seul bonus ou malus s’applique par tour (OffSetApplied).
La mémoire de travail : le résultat du dé (DiceResult), le décalage de la case actuelle (OffSet), et la position de la prochaine case à atteindre (TargetLocation).
