# Cours de NMV

Point à faire avec Julien Sopena, fin novembre, sur comment on a ressenti BDLE et DATACLOUD (on manifestement assez peu à faire les deux). Parce que ça l'intéresse de savoir ce qu'on en pense du combo.

## Cours du 07-09-2020

Lien vers le cours en ligne : http://julien.sopena.fr/enseignements/M2-SAR-Git/cours/01-Git/01-Git.pdf

Le cours actuel sera doublé cette semaine et la semaine suivante. D'ici 3 semaines, ça sera probablement totalement en distantiel (ou tout en physique, mais c'est peu probable).

Cours sur Git : architecture. Git c'est un système de fichiers versionné. C'est un gestionnaire de versions décenralisé. Créé pour le noyau Linux à la base.

p.7 Un historique n'est pas un graphe : un arbre est un graphe **non orienté, acyclique, connexe**. Alors qu'un historique est **orienté**.

p.8 Une branche va jusqu'à la racine : c'est importat à retenir.

p.9 la branche principale peut changer, c'est juste celle qu'on a décidé.

p.10 une sous-branche est l'historique d'une branche privé son intersection avec la branche dont elle est issue.

p.12 CVS était centralisé.
Git est pensé par Linus comme un vrai système de fichiers qu'il est possible de mount comme tout système de fichier.

p.14 tous les 4 types d'objets sont des Blob. Les Tree, Commit et Tag sont juste des Blobs particuliers. Dans le cours, quand on parlera de blob, on parlera du blob et pas des autres types. On préisera toujours la nature exacte du type d'objet. Ces objets sont immuables, i.e. non nutables : on ne peut pas les modifier. (une fois créés, il ne sont plus modifiables) (non-mutable en anglais)


Comme le fichier n'est pas modifiable, on peut créer un identifiant unique basé sur leur contenu. On utilise une fonction de hashage qui rend peu probable les collisions. Ca ressemble un peu aux inodes vues en noyau au S1, i.e. un stockage des noms de fichiers / identifiants dans une structure.

Le nom d'un fichier blob est le sha-1 de son contenu.

COmme sans un filexystem unix, le nom du fichier n'est pas dans le fichier, le nom du blob n'est pas dans le blob. Si on déplace (move) un blob, son nom ne change pas, son contenu n'a pas changé donc son sha-1 n'a pas changé. Le blob est aussi signé avec sa taille : le hash hash taille + contenu et la taille est connue. (c'est à dire que pour réussir à attaquer et à changer un blob, il faut en trouver un du même hash et de la même taille)

Lorsqu'on utilise les chaînes, on a le droit d'utiliser la sous-chaîne la plus courte permettant d'identifier le blob d'une manière unique. Encore une fois, le nom n'esr pas stocké dans le blob, cf diapo 22/87 qui montre les infos (comme les inodes) stockés dans un Tree. On remplace les sha par un numéro d'inode et on a exactement un système de fichiers unix. On a la déduplication immédiate car un même contenu référencé deux fois aura le même nom (sha-1).

On considère que les collisions n'arrivent jamais. Mais si ça arrive, git fait un diff octet par octet et git (probablement) lève un flag.

Sous CVS, un commit d'un fichier à un autre n'est que composé des delta : ce qu'il faut changer pour arriver à ce nouvea fichier. Ca pose le problème suivant : restaruer un fichier demande beaucoup de calcul, il faut le recalculer depuis le début, et d'y ajouter toutes ses modifications. Avec CVS, il n'est pas possible de restaurer un instantané du système a un instant donné. Du coup, il fallait mettre des tags à la main.

SVN est toujours utilisé aujourd'hui, c'est du décentralisé, mais pas super bien décentralisé quand-même. Les versions sont par état global, du coup il est aisément possible de restaurer un état global cohérent.

Sous git, modifier un fichier crée un nouveau blob. B n'a pas été modifié d'une version à une autre, il est donc son blob n'a pas été modifié.

(p.26) Tree = une racine. On part d'un arbre pour rescenser l'intégralité des fichiers à ce moment donné.

Derrière git, il y a un algo de compression pour ne pas surcharger le stockage. Tag : sha + chaine de caractères. Un état stocke donc les sha vers tous les blobs.

p.35 bisect : dichotomie. Faire une recherce par dichotomie. Sert à regarder "quel commit a fait buguer une fonctionnalité" en adjonction à un test unitaire, au lieu de reparcourir tout l'historique.

On sera pas interrogé dessus, mais par culture générale :
p.39/87
le --bare sert d'éviter d'avoir les fichiers réeels. Avoir un fichier modifié bloque beaucoup de commandes git (ça j'ai bien vu !). Pour éviter que les fichiers ne puissent être modifiés (et donc les push, commits etc. bloqués), il est possible de n'utiliser que le stockage de l'historique, et no plus des fichiers. Julien nous recommande fortement de n'avoir que l'historique sur un serveur git, en faisant `git clone --bare`. Pour initialiser sur le serveur : `git init --bare toto.git`.

p.41 Staged est une enveloppe de choses à commiter. C'est un peu comme un snapshot des fichiers à commiter. C'est ce que fait `git add ... fichiers ...`. Le schéma montré s'appelle une machine à état.

(p.43) le `git add` crée le / les blob.s. Le commit les ajoute à l'index. D'un point de vue conceptuel, l'enveloppe c'est la prochaine racine. Un commit prend l'index, le transfome et blob *et crée un arbre qui le référence* (vérifier cette fn de phrase). Précision redondante : un blob n'est pas un fichier source, c'est un fichier .git.
Comme vu précédemment, un commit crée un Tree.
Modifier un fichier => créer un noveau blob => créer de nouveaux blobs pour les répertoires dans lesquels il est. C'est pas très lourd, et c'est nécessaire pour pouvoir toujours disposer d'un état cohérent à chaque commit. Les commits sont liés entre eux, pour vraiment avoir un historique.

Dans ce cours, on dézoome de plus en plus, allant du détail de l'implémentation à une vision plus globale. Le hash du dossier est calculé sur ses entrées : noms de fichiers et hash des fichiers (et non du contenu complet du dossier).

A chaque commit donc, il y a un un Tree associé. Ce Tree est le Tree de la racine, c'est exactement comme une inode : le Tree contient des sha des blob qu'il contient.

Globalement, c'est exactement un Merkle Tree.

p.46-48 lorsque on checkout dans une autre branche, les fichiers non présents dans la branche actuelle mais présents dans la branche dans laquelle on était, ces fichiers disparaissent du système de fichier courant mais restent stockés dans le .git, donc dans l'historique.

Par défaut, la branche principale s'appelle master, mais ce nom peut être changé.

le fast forward via git ?

Le rebase ajoute la branche courante à la branche qu'on spécifie dans le rebase. Le rebase est extrèmement utile, très souvent. Il sert à nettoyer et rendre plus propre et clair le graphe de l'historique. C'est beaucoup plus simple de débuguer avec un graphe avec le moins de ranches possibles, manifestement. Le rebase permet aussi plus de lisibilité.

p.54 la seule réelle bonne pratique est le revert. Les autres méthodes risques d'engnedrer des divergeances d'historiques, ce qui rendra (impossible) très difficile les futurs merge. Le amend supprime le commit.

p.56 le revert crée un commit pour annuler un autre commit. C'est simple et propre. Le commit de revert est juste l'inverse de l'autre commit, c'est un commit comme les autres.

Exemple de question d'examen : "combien de blobs sont créés avec cette commande ?". p.56 : bloc de commit4 barre + nouvelle racine. Si la chose a déjà existé, via la déduplication de git, pas besoin de recréer un blob. A chaque modification de fichier, tous les répertoires sont modifiés (car leur hash change dés lors qu'un hast d'un sous-dossier / fichier change.)

Git, par rapport à SVN, permet d'avoir une vision locale de l'historique.
Globalement, je ne peux push sur le serveur que si je suis à jour. Julien dit que c'est mieux de faire fetch + merge plutôt que juste pull. Ca permet de faire plus de choses. (et permet de faire un rebase au lieu d'un merge, par exemple).
origin/master est la vue qu'on a de ce qu'il y a sur le serveur. `master`, quant à elle, est la branche locale.

p.71 merge de C3 et C4. Le premier push origin foo sert à créer la ranche distante, pour pouvoir push dessus il faut la récupérer du serveur.

Il est possible d'ajouter un nouveau serveur pour partager des branches sans pour autant les mettre sur le serveur.

p.74 : la petite terre symbolise que c'est publique.

Git, ça tombe à l'exam un peu, ne pas oublier ça.

## Cours 2 - 2020-10-21

C'est essentiel de bien comprendre a manière dont la mémoire est génére dans nos systèmes.

cm-1-vmemory.pdf

L'accès à la mémoire est l'opération la plus fréquente, ça sature vite les bus.

p.6 : rsp : (sp = stack pointer, pointeur de pile, le r symbolise 64 bits, "register stack pointer"). L'assembleur ici décrit est un asembleur Intel.
R A X : R = 64 bits
AX symbolise 16 bits, HAX parties hautes d'un registre 16 bits (les 8 bits de pois fort) et LAX la partie basse (h à gauche, l à droite)
EAX : extendex AX sur 32 bits
RAX : register AX sur 64 bits

Une architecture 64 bits n'est pas forcément plus opti qu'une appli en 32 bits, ne setait-ce qe parce que les pointeurs prennent autant de bits que l'architecture.

En assembleur ici, $ signifie uen valeur et % annonce le nom d'un registre.

La pile est un concept matériel.

0(%rsp) déréférence en prenant l'abresse contenue dans le registre %rsp. %rdi symbolise le 1er argument de la fonction.

RDI RSI RDX RCX R8 R9 : registres symbolisent les paramètres d'une fonction. Au-delà de 5 paramètres, il faudra les passer par  la pile. Mais seulement un user-land.

En kernel, c'est RDI RSI RDX R10 R8 R9 car RCX est utilisé pour autre chose. DI : Destinatio Index, SI : source index. Antérieurement, on pouvuat faire un memcopy via %rdi %rsi %rcx : index dest, source et longueur. Ce qui pouvait être du coup faite en 1 instruction assembleur. Aussi, toutes les instructions assembleur n'ont pas toutes la même longueur.

Du coup, voir rsp et rdi indiquent qu'un call va être fait (on passe des paramètres à la future fonction).

call : saute dans le code de f. Sauvegarde l'endroit où on était pour pouvoir y revenir.

"Ajouter, c'st dépiler" (on avance dans les adresses) : 13 est plus proche de 0 que de 2^32 ou 2^64. @_start est à une adresse plus élevée que 13, dans cet exemple.

p.7 : ce n'est pas le noyau qui choisit l'adresse des malloc, c'est a glibc qui décide ça dynamiquement. Des parenthèses ymbolisent le fait de déréférecer. (le shift de 0 est immlocite et factultatif). En virtualisation, les adresses sont imposées.

La rpole de l'OS est de partager la mémoire et d'assurer l'isolation. Du coup l'isolation des programmes n'est pas qu'une affaire d'évitement de collisions mais aussi d'isolation et donc de sécurité. Une application ne doit pas pouvoir accéder à un eautre application.

p.16 la MMU Memory Managment Unit.

Page : mémoire continue de taille fixe. L'emplacement est nommé cadre de page.
p.20 on n epeut couper l'adresse directement pour en retirer l'adresse de la page ainsi qu el'offset dans la page que arce que les adresses des pages sont des puissances de 2.

p.22 registre CR3 : super important, c'est grâce à ça que les processus existent.
Les quatre gros rectanges sont des pages, et dans ces pages il y a des adresses. Chaque ligne fait 8 octets, ici en 64 bits.

2 puissance quelque chose : les dizaines sont le nombre de zéros *3 et les unités sont le nombre à mettre devant.

p.23 : KiB : kibibyte. En adresse cirtuelle, on a que 48 bits adressables, le reste, c'est juste des 0. Ce sont des bits réservés pour l'évolution et la rétrocompatibilité.
L'offset étant codé sur 12 bits, l'adresse des pages est sur 36 bits.
p.24 : l'extension de signe.   9  512

Pour résoudre une adresse, il faudra toujours 4 pages. 9 bits -> 512 pages différentes

p.25 : page frame = cadre de page = emplacement de page

p.28 : unsigned long : c'est un mot mémoire, toujours, par convention. (32 bits ou 64 bits seon architecture)

4 places dans la TLB : on accède à 1 ... 6 et à la fin on a juste les pages 3 4 5 6 dans la TLB mais on a oublié les 1 et 2, et on doit donc y accéder de nouveau sans passer par la TLB.

p.30 : (4 + 2) les accès aux pages + 2 accès aux données
3 + : les 3 pages PML 4 -> 2.
+ 10 * 6 * 2 -> accès aux données

Avant d'essayer de réoptimiser un algo, essayer de voir si le système peut être configuré pour que l'algo marche bien. Ici, via l'activation des HugePages. Il y a aussi des pages encore plus grosses : les pages de 1go (avec seulement PML4 et PML3). (contre 2Mb pour les HugePage)

p.32 : Commuter, c'est changer le CR3. i.e. changer l'espace dadressage. Il y a autant de tables de page que de processus, i.e. d'espaces de mémoire virtuelle séparés. Le vrai cout de la commutation est la TLB (changement de processus).

p.36 : adresse linéaire : traduction par segments et dans Linux adresse virtuelle = adresse linéaire.

La pagination c'est des bouts de mémoire de taille identique.

Segmentation : typé, taille variable.
Pagination : non type, toujours de même taille.

p.37 : U : bit pour savoir si la page est de type noyau ou utilisateur. La différence entre S et U est gérée en matériel et sont physiqueent protégées, ce n'est pas que logiciel.

A Access, D dirty, PWT page write through, PS page small (huge page ou page "small" normal)

p.39 : le ++retry == 2 sert à refaire le calcul au as où un eerrer se soit produite dans la précédent calcul ("rayon cosmique, prolème matériel, ...").

Adresses invalides pour la MMU : singifie qu'elles n'ont pas d'entrée dans la table de page.
p.42-43 : base de tous les modes lazy. Le noyau log pleins d'exceptions, mais sans que ça ne soit gènant. Le mode lazy fait ses propres trucs et jette des erreurs, mais on s'en fiche, il fait tout bien quand c'est nécessaire.

Vulnérabilités Spectre et Meltdown sur la table de pages qui reste du ùode User au mode noyau, et donc la TLB qui n'est pas flushée.

La majorité des adresses ne sont pas choisies par le noyau : variables dans des registres, soit relatives au stack pointer, soit ... . (etc)

Anciennement il fallait flush la TLB, maintenant ce n'est plus nécessaire comme les identifiant sont clairs.

Savoir bien faire ce qu'il y a à la page 28, c'est un bon test pour savoir si on a bien compris.


## Examen

Rappel : donc en NMV, on a un examen réparti 2, et un exposé en février. Pas d'ER1 du coup. On aura le sujets vers janvier, la soutenance sera le dernier vendredi de cours, après les ER2.

Il faut rendre le TP1 de NMV. Il est un peu long à mettre en place, mais il est censé être simple celui-là. Pour ce TP, en plus de qEmu, on aura beasoin de xorriso (chorizo) : https://www.gnu.org/software/xorriso/

Et aussi besoin de : (pour graver des images iso)

```
extra/libisoburn 1.5.2-2 [installé]
    frontend for libraries libburn and libisofs
```
