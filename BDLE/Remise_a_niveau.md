# Cours 0 de remise à niveau en BDLE - 02-10-2020

Site de l'UE : http://www-bd.lip6.fr/wiki/site/enseignement/master/bdle/start

**Mattermost**
*Inscription :* https://channel.lip6.fr/signup_user_complete/?id=wjmzhwc4ein59jidwzq5a6u96a

*Accès :* https://channel.lip6.fr/dac-19-21/channels/town-square

*Accès BDLE :* https://channel.lip6.fr/dac-19-21/channels/bdle

*Mail du prof des cours 1 & 2 :* hubert.naacke@lip6.fr

PDF de cette première remise à niveau : cours00_2020_intro_SQL_distribue_et_projet_IMDB_v3

Au cours de l'UE, on utilisera beaucoup Spark. Manifestement c'est une bonne chose à marquer sur son CV.

## Rappels SQL

### Les motivations, pourquoi SQL c'est le bien.

En gros, le SQL c'est utilisé partout, et tout le temps. Par exemple, Facebook a développé une API permettant de faire des requêtes simples et spécialisées, converties en SQL.

SQL optimise en interne les requêtes (c'est l'optimiseur qui le fait), sans conséquence sur le résultat, bien entendu. Globalement, comme vu dans le cours de L2, la requête est "transformée en programme".

En fonction des besoins de la requête, on va adapter la gestion des ressources de calcul et de mémoire, pour gagner en efficacité. Le SQL est super flexible et essaie d'optimiser un maximum les choses.


### Schéma

Une relation décrit un n-uplet. Un tuple est une structure telle un dictionnaire, et chaque attribut a un type. Le schéma est la ligne d'en-tête du "tableau", au final. (edit : et les interactions entre les différentes tables de la base, i.e. tout le bordel avec les clefs primaires et clefs externes)

Références entre les relations : permet de lier les relations.

Prédicat global : contraintes pour certaines valeurs (bornes de valeurs, longueur des chaînes...)

"upper" permet de convertir en majuscule le nom du pays. Ça nous sera super utile lorsque les données sont à traiter : lorsqu'on veut extraire de l'information d'une valeur par exemple. Par exemple, extraire l'année d'un film si elle se situe à la fin de la chaîne. C'est un peu du "nettoyage" de chaîne de caractère par exemple. (p. 8) Automatise donc le nettoyage de données.

`like` = pattern matching que ça s'appelle.

-> regarder ce qu'il y a sur `Mattermost` (vu que je n'y suis jamais allé). C'est surtout entre nous le mattermost, passer par mail si on a des questons à des intervenants.

count( distinct pays ) -> le disinct pays est fait avant le count, d'où le nombre de pays distincts retournés.

p.11 : voir les dépendances fonctionnelles de L3. ("y a deux ans" mais c'est pas essentiel, manfestement)

p.12 en général, on doit ajouter `group by personID, profession` plutôt que juste `group by personID`

p 13 : personID est clef étrangère qui fait référence à la clef primaire de Personne (`id`)

p.14 : afficher les personnes n'ayant jamais mis une note inférieure à 4. (i.e "toujours mis de notes supérieures ou égales à 4")

p. 16 : c'est bien de formuler ça en langague naturel, puis de de transcrire en SQL (double négation bien utile). On remarque que les variables définies en début de requête sont réutilisables durant toute la requête. Il faut savoir faire ce genre de requêtes de manière fluide (ie. il faut bien savoir faire ça)

p. 18 : bonne habitude : dès qu'une relation est déclarée dans `from`, bien déclarer les variables pour pouvoir aisément les réutiliser dans la requête.

p.19 : le prof dit que ça serait top de beaucoup s'exercer en SQL. Essayer de penser en SQL, à exprimer des traitements de manière ensembliste. Essayer de se familiariser avec SQL. Essayer de penser les phrases en langage naturel en "il existe" ou "il n'existe pas" ...
Ne pas hésiter à demander les liens / fichiers manquants aux profs s'il n'y en a pas sur les sites des annales des UE (L3, M1)
En gros, faut s'exercer en SQL.
Lien vers le `TP de M1 avec le SGBD H2 et l’interface SQLWorkbench` : https://nuage.lip6.fr/apps/onlyoffice/s/XiRqikEzcrGjzLL

Z. va nous envoyer un mail nous disant de nous inscrire sur databricks.com (p 21). C'est déjà fait pour moi à l'heure actuelle.

La plateforme Spark a été écrite en scala. En SQL on peut définir ses propres fonctions, on pourra alors les écrire en Java, Python ou Scala. Ces langagues vont nous permettre d'étendre SQL. Le prof nous encourage à apprendre Scala, même si c'est pas urgent, ça reste juste "intéressant" donc pas une priorité si j'ai beaucoup de travail. L'aspect fonctionnel de Scala permet de mieux comprendre le concept de Map-reduce. Il est aisé d'exprimer les map-reduce en Scala.

Databricks est un notebook, en ligne, persistant en ligne contrairement à Jupiter notebook.
`dbfs` -> dataBricks filesystem

On peut s'incrire dès maintenant sur databricks, pas besoin d'attendre un mail. Pour vendredi prochain, savoir utiliser databricks notebook, s'y familiariser pour ne pas perdre trop de temps vendredi prochain. Importer le notebook qui sera dispo sur le site de l'UE.

Question : concrèement, quoi réviser pour vendredi, quoi lire ? Proposition : lire les cours, s'entrainer sur ressources données dans les dispos. (p. 19 pour les liens & infos)


## SQL Large échelle

Le SQL crée des "objets de traîtement" c'est à dire des objets qui ne sont pas immédiatement exécutés, et qu'on va pouvoir manipuler (réutiliser des résultats intremédiaires par exemple, ...) (p. 27)

On va accéder à Spark en python (p. 30)

Pour la semaine prichaine : lire la fin des diapo (p. 31 et +)

Vendredi : y aura pas mal de jointues et de regroupements

*Tout ça, c'est bon et déjà fait :*
Dire que je suis en SAR et demander le TP 1 de master pour les exos sur BDLE, demander à être inscrit sur la liste de diffusion du master DAC M2. Demander à m'inscrire à la liste de diffusion à cette adresse : master.info.dac@upmc.fr


# Cours 1a  : Bases de données Large échelle - 09-10-2020

On attaque la partie SQL à large échelle. Analyse de données brutes : pouvoir faire une requête sur un fichier non indexé, non structuré, non passé par un SGBD.

c1p.8 Vue virtuelle = vue logique. (COURS 1 PAGE 8)

c1p9 : service : offrir un service en masquant la complexité sous-jascente. L'utilisateur n'a pas à conaitre les spécificités de l'implémentation.

il faudra connaître le syntaxte pyspark

req.persist : losque la reqête sera exécutée, on veut que le résultat soit persisté en mémoire. Généralement suivi de l'instruction count pour compter le nombre de résultats retournés par la requête. Count doit exécuter la requête pour en connaître le nombre de résultats, et le fait d'avoir fait persist avant permet de garder le résultat en mémoire lorsque count est exécuté.


### p.13

le F.createOrReplaceTempView("F") est l'équivalent SQL de `create or replace temp view`. Ça permet de créer une vue (un peu comme un table, sauf que je pense qu'on appelle ça vue parce que c'est une "vue" sur une ou des tables) et de l'utiliser par la suite, comme `TitleDetail` ou `NameDatail` dans le TP.

req = spark.sql : req représente, dans l'interface pyspark, la requête qu'on a écrite en SQL.

En TME, on sera amenés à manipuler SQL et python (pyspark).

**collect** permet de transformer les données manipulées dans l'environnement Spark, demande à Spark de calculer le résultat de la requepete, prend le résultat et l'injecte dans un tableau python qui se trouve dans l'environnement de l'application. Il y a d'un côté le service Spark et d'un autre côté l'application. **Collect** permet d'instancier les données dans l'application, importe les données dans python. Du coup, il est ensuite possible d'en faire ce qu'on veut par la suite (l'afficher, de le manipuler...)

p.14

Chaque requête, quand elle est déclarée comme ça, est réutilisable dans d'autres requêtes. WITH permet d'affecter un nom. En SQL, on peut aussi définir des vues temporaires.

-> réviser la syntaxe des expression régulières, ça peut être très utile.

p.17 : every : est-ce que la propriété est vraie pour tous les éléments du tableau / exists : sur au moins un élément.

explode : permet de re-normaliser les données.

p.18 : lorsque qu'on définie une fonction, elle est dans l'application. Il faut l'exporter pour pouvoir l'utiliser dans le service qui gère les données.

p.19 un tuple est un tabeau composé de champs.

#### Projet qui sera à faire au cours du semestre

Les savoirs faire qui deveront être acquis au cours de l'UE sont listés p.23. Il faudra pratiquer et être à l'aise avec les notebooks qu'on va utiliser. C'est pas trop gênant de ne pas trop connaître le shell, mais ça serait bien de réviser/apprendre un peu.

p.25 les données IMDB ne sont pas très structurées, et elles sont intéressantes à restructurer du coup.

p.27 les attributs issus de jointures seront affichés de la même manière que les attributs compris dans la table Title. C'est d'ailleurs bien là l'intérêt des jointures que nous allons faire.

Commencer par utiliser le dataset donné à la page 29.

BDLE est beaucoup de pratique, mais s'appuie sur quelques bases théoriques, le prof nous a dit que ça serait sympa de lire l'article renseigné à la page 32. Ça nous aidera à comprendre des choses sur les cours suivants. Il y a pleins de ressources sur le site indiqué `https://www-bd.lip6.fr/wiki/site/enseignement/start`

Il serait bien qu'on débute assez rapidement le projet, pour qu'il soit fini avant la 3ème séance.

Mattermost cours : https://channel.lip6.fr/signup_user_complete/?id=wjmzhwc4ein59jidwzq5a6u96a

https://channel.lip6.fr/dac-19-21/channels/town-square

### Poly cours 1b - cours01b_2020_SQL_multidim_avec_URL

Analyse multidimensionnelle.

Les analyseurs de requêtes sont de plus en plus performants.

p.8
Pour un attribut, il peut y avoir plusieurs dimensions, donc plusieurs hiérarchies. Une branche peut se scinder en plusieurs hiérarchies à mesure qu'on remonte, qu'on "dézoome". Il faut toujours faire attention au sens des donnés qu'on manipule. C'est pas parce qu'on peut les manipuler qu'elles ont toujours du sens.

La semaine prochaine, le prof va détailler les analyses (p9+)

L'objectif de ce TME sera de prendre en main les données et de poser les bases du projet.

## TP

Dispo à l'adresse suivante :

https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/2791810606759612/2495359598332771/5157551416556042/latest.html

J'ai importé la dernière version dans mon notebook, je recopie ici aussi les questions auxquelles il fallait répondre pour ce TP (en gros, il s'agit d'enrichir les données, de les restructurer et de les lien entre elles) : 

```
Proposer d'autres requetes pour restructurer la base de manière à faciliter les analyses des films et des acteurs.

Exemple : Acteur(id, nom, sunom, date_naissance, nationalité, taille, ...)

Film(id, titre, année, budget, pays_de_sortie, pays_de_tournage, ....)

Garder en perspective, l'objectif d'analyser les données selon plusieurs dimensions. Exple: analyser les roles des acteurs : nombre de roles par acteur (ou par film) en fonction des années, des pays, etc...
```

A la fin de la séance, il faudrait savoir quel schéma on veut retenir pour les données.

Rendre un notebook avec beaucoup de commentaires, expliquant bien toutes les requêtes qu'on a faites.

Proposer des requêtes de nettoyage. Proposer une liste d'attributs à afficher.

Suite de mes notes sur le notebook :

```
%md
Ce que je propose d'utiliser

Un groupe prpose de faire quelque chose sur les interviews. Pour chaque interview, dire qui y ont participé, sur quel film ça porte etc.

On peut trouver un attribut qui nous semble intéressant, et le traiter. (avec des jointures toussa toussa)

Premier exemple simple : pour une personne donnée, 

-> Il faut passer d'un schéma générique à un schéma instancié. On peut transformer des catégories en atributs : exemple de film -> color_info. On choisit un des attributs de la catégorie color_info et on en sélectionne un attribut (ou plusieurs).

"Créer des features"
```

Normaliser / dénormaliser : c'est factoriser l'information pour qu'il n'y ait pas de redondance. Notre table est factorisée : le nom d'un film n'est présent qu'à un seul endroit. Ça rend beaucoup plus simple la modification et la mise à jour (parce qu'il n'y a pas de dédoublement des informations à gérer) mais faire des recherches dessus ou de l'apprentissage est beaucoup plus simple, comme les informations ne sont pas "compressées".
Ici, si on veut retourner plusieurs champs un un seul (comme film -> pays dans lesquels il est tourné) on retourner une chaine de caractères avec les valeurs séparées par des virgules par exemple, comme on ne peut pas avoir un nombre variable de colonnes (table de base de données oblige). C'est plus simple dans le cas de la catégorie color_info qui peut être résumée par son attribut color valant noir&blanc ou couleur. Du coup dans ce projet, globalement, il faut transformer des catégories (donc des "objets" qui font référence à ou sont référencés par un film) en attributs de ces films. J'utilise film comme exemple, mais on peut prendre n'importe quel "objet" ou attribut et l'enrichir. Un autre groupe va travailler sur les interviews et faire les liens entre les informations.

Un exemple de dimensions : type d'épisode pour les séries

Pour la question 2 : quelques requêtes seulement. 2 reqêtes de fenêtres, et 2 requêtes de regroupement. Il faut définir 1 table centrale, et au minimum 2 tables de dimensions. Il n'est pas demandé de le dessiner : voici la able de fait (auteur + role) et voici les dimensions qu'on a choisies.

Réserver un peu de temps pour la tache 3 et 4, disons 1h. Réserver le plus de temps pour la tâche 2,probablement plus d'une heure.

-- Je me suis arrêté à la cellule 60 du TP1, reprendre de là.



%md

Idées:

Sociétés de production pour un film :
FilmLanguages(titleID, title, company_name, company_type)
(dans TitleDetail et )

Les langues d'une oeuvre :
FilmCompanies(titleID, title, languages)

La durée d'un film
Info_Type()


Les adaptions d'un film

Un polycopié de cours qui m'a l'air pas mal (sur les slice, rollup, dice, ...) et les table de fait / dimensions :

http://www.lirmm.fr/~laurent/POLYTECH/IG4/DW/poly_bdm.pdf



# Cours 2020-10-30

Autocomplétion pour DataBricks : https://docs.databricks.com/notebooks/notebooks-use.html#autocomplete

(il faudra avoir exécuté toutes les cellules qui précèdent)

# Cours 2020-11-20

Questions :
- est-ce qu'on aura les corrigés des TP ?
- est-ce qu'on aura des annales pour se préparer ?
- Qu'est-ce qu'il y aura au programme de l'examen ?