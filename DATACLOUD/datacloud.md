# DataCloud

La semaine prochaine, on passera en distanciel.

Tous les document seront sur Moodle.

# Cours 1 : Introduction au bigdata

Scala repose sur un paradigme fonctionnel (lambda, toussa toussa). On va l'étudier dans la suite du cours.

### Diapos introduction_bigdata_polycopie.pdf

Slide 11/11 : Stockage dans des fichiers | Indexation logique sur les données. Dessus vient se fixer une surcouche SQL et d'autres sur-couches (Spark, Flink, ...), pour permettre aux développeurs de réutiliser simplement des langages qu'ils connaissent déjà (un peu comme chez Indeximan, d'ailleurs...). Tout ce que nous allons utiliser est développé par l'organisation Apache.


## Map-reduce, diapo cours_map_reduce_polycopie.pdf

*OSDI : la conférence mondiale en système.*
En quelques mots, map-reduce, c'est du traitement parallèle de données.

(p. 6/16 la phase reduce)
Le shuffle fait un truc du genre "group by". Associe un ensemble de valeurs à une même clef, i.e. regroupe les valeurs en fonction de leur clef.

Souvent, le nombre de reduce est très inférieur au nombre de map.

Interactif (comme SQL) : traiter des données particulières. Batch (comme map-reduce) : traiter les données par lot.
Les données en fin d'un Map-Reduce ne pevent pas être mises à jour. Pour rajouter / modifier des données, il faut jeter le résultat du précédent map-reduce, et refaire tourner le map-reduce.

**Un truc que j'ai pas compris** : p.12 du cours, une tâche de Reduce prend une association <clef, liste de valeurs> et dans l'exemple, il semble n'y avoir qu'un seul Reduce pour plusieurs associations. Je suppose que c'est une seule tâche de reduce mais plusieurs appels à la fonction `Reduce()`, une par tuple <clef, liste de valeurs>. <- A demander confirmation.
Ou alors, c'est que la politique du tri du shuffle permet de ne pas trier seulement par valeur, mais aussi selon d'autres critères ?? Mais ça serait chelou vu ce qu'on a vu précédemment, comme la fonction Reduce prend en paramètre une (seule) clef et une liste valeurs. La clef peut-elle être une structure / objet ???

p.15 SQL ne passe pas à l'échelle, beaucoup plus de données implique des traitement beaucoup beaucoup plus longs, alors que Map-Reduce peut facilement scaler. 


## TD de SRCS - Travaux dirigés Exercices de Map-Reduce

Les 3 premiers exercices sont des cas d'école qui nous suivront tout au long de cette UE.

**Squelette de base :**

```java
Map(<TypeCléMap> key, <TypeValeurMap> value) {
    //ici votre pseudo code map
}

Reduce(<TypeCléReduce> key, liste de <TypeValeurReduce> values) {
    //ici votre pseudo code reduce
}

IdReduce getPartition(<TypeCléReduce> key, <TypeValeurReduce> value, entier nbReduce) {
    //retourne l’identifiant reduce de 0 à nbReduce-1 pour <key,value>}

entier compare(<TypeCléReduce> key1, <TypeCléReduce> key2) {
    //retourne négatif si key1 < key2
    //retourne nul si key1 = key2
    //retourne positif si key1 > key2
}
```


### Exercice 1

#### 1.1.

Dans cette question, la clef est le numéro de ligne, la valeur la ligne en elle-même. On considère qu'on a une fonction `extract` permettant de parser cette ligne, pour en extraire l'année et le prix.

```java
// key est ici le numéro de ligne (osef)
// value est ici une chaine de carcatères représentant la ligne lue
Map(Entier key, String value) {
    prix = extract_prix(value);
    annee = extract_annee(value);

    if (annee == 2018)
        emit(2018, prix);
}

Reduce(Entier key, Liste de Entier values) {
    sum = 0;
    for value in values {
        sum += value;
    }
    emit(key, sum);
}
```

#### 1.2.

```java
Map(Entier key, String value) {
    prix = extract_prix(value);
    cat = extract_catégorie(value);
    emit(cat, prix);
}

// key est donc ici la catégorie
// values la liste des prix des objets vendus
Reduce(String key, Liste de Entiers values) {
    sum = 0;
    for value in values {
        sum += value;
    }
    emit(key, sum);
}
```

#### 1.3.

```java
Map(Entier key, String value) {
    categorie = extract_catégorie(value);
    nom = extract_nom_produit(value);
    emit(categorie, nom);
}

Reduce(String key, List of String values) {
    Map<String, int> compteurs = new Map<>();

    for v in values {
        if (compteurs.contains(v))
            compteurs.put(v, compteurs.get(v) + 1);
        else
            compteurs.put(v, 1);
    }
    
    emit(key, map.getKeyAssociatedToMaxValue());
}
```

Il faut, autant que possible, faire le moins de reduce possible. A revoir : je suppose que moins il y a de reduce, moins il y a d'activité réseau et d'échanges, et que du coup c'est plus opti ainsi. C'est à dire regrouper le plus possible les clefs, et faire le gros du travail dans les maps.

### Exercice 2

#### 2.1.
A la fin, on devrait avoir 48 lignes : une ligne par demie-heure.

```java
// Emission des mots clefs par tranche horaire
Map(Entier key, String value) {
    send = 0;
    heure = extract_heure(value);
    minute = extract_minute(value);
    minute = (minute < 30) ? 0 : 1;
    // i.e. si minute < 30  => minute = 0
    //      si minute >= 30 => minute = 1
    listeMotClef = extractMotsClef(value);

    for mot in listeMotClef {
        if (!send) {
            // On indique qu'il faut compter la requête
            emit(heure + "-" + minute, (mot, 1));
            send = 1;
        } else {
            // On indique qu'il ne faut pas compter la requête
            // lorsqu'il y a plus d'un seul mot clé par requête.
            emit(heure + "-" + minute, (mot, 0));
        }
    }
}

// Calcul du mot clef le plus recherché dans une tranche horaire donnée
// et du nombre de requêtes reçues.
Reduce(String key, List of (String, int) values) {
    // Map qui associe le nom du mot clef au nombre de fois qu'il a été recherché
    Map<String, int> map;
    // Compte le nombre de requêtes
    int req = 0;

    for item in values {
        if map.contains(item[0])
            map.put(item[0], map.get(item[0]) + 1);
        else
            map.put(item[0], 1);
        
        req += item[1]; // ajoute soit 1 (il faut compter la requête) soit 0 (ne pas compter cette requête)
    }

    emit(key, (Max(map), req));

    // demie heure -- nom du mot clé le + recherché et nombre de requêtes reçues
    // str key, (String, int)
}

/* En pratique, il aurait fallu définir une structure ayant deux champs :
   un String (le mot clef) et un int (indique s'il faut compter la requête ou non). */
```

Explication de la solution mise au tableau (il existe plusieurs solutions possibles) :
Il envoie une requête par mot clef, avec un entier en plus : 1 pour indiquer qu'il faut compter ce mot clef comme une requête, et 0 si un mot clef pour cette requête a déjà été envoyé (et donc que la requête a déjà été comptée, pour ne pas la compter plusieurs fois).

Le nombre de taches de reduce est indépendant des données, c'est un paramètre qui est défini par l'utilisateur.

Alors j'ai pas totalement pu recopier la solution en rouge sur mes photos (et en plus j'ai mal cadré) mais je pense que l'autre manière de faire est de regrouper par demie-heure, d'envoyer pour chaque requête : <demie_heure, liste_de_mots_clé>. La solution en rouge (photo) est du coup moins gourmande en bande passante que la noire, mais demande un peu plus de calculs au niveau du `Reduce()`. Jonathan dit qu'il choisirait la rouge, mais que la noire était une solution intéressante qu'il n'avait encore jamais vue.

#### 2.2.

C'est la solution en verte. Recopier de mes photos.

Je recopie toute la solution

```java
// Emission des mots clefs par tranche horaire
Map(Entier key, String value) {
    send = 0;
    mois = extractMois(value);
    heure = extractHeure(value);
    minute = extractMinute(value);
    minute = (minute < 30) ? 0 : 1;
    // i.e. si minute < 30  => minute = 0
    //      si minute >= 30 => minute = 1
    listeMotClef = extractMotsClef(value);

    for mot in listeMotClef {
        if (!send) {
            // On indique qu'il faut compter la requête
            emit((mois, heure + "-" + minute), (mot, 1));
            send = 1;
        } else {
            // On indique qu'il ne faut pas compter la requête
            // lorsqu'il y a plus d'un seul mot clé par requête.
            emit((mois, heure + "-" + minute), (mot, 0));
        }
    }
}

// On redéfinie la fonction 

// nbReduce = 12 : valeur paramétrée dans le mar-reduce

getPartition((mois, tranche), (mot, nb), int nbReduce) {
    // retourne l’identifiant reduce de 0 à nbReduce-1 pour <key,value>}
    return id(mois) - 1; // valeur comprise entre 0 et 11
    // si nbReduce < 12, on peut probablement envisager de mettre % 12.
    /*
    Je suppose que c'est pour produire les logs de chaque mois dans le bon fichier ? Et du coup pour ne pas avoir autant de Reduce que de clefs différentes (i.e. juste 12 fichiers Reduce, et non 12 * le nombre de demies heures)
    */
}

// Calcul du mot clef le plus recherché dans une tranche horaire donnée
// et du nombre de requêtes reçues.
Reduce((mois, tranche), List of (String, int) values) {
    // Map qui associe le nom du mot clef au nombre de fois qu'il a été recherché
    Map<String, int> map;
    // Compte le nombre de requêtes
    int req = 0;

    for item in values {
        if map.contains(item[0])
            map.put(item[0], map.get(item[0]) + 1);
        else
            map.put(item[0], 1);
        
        req += item[1]; // ajoute soit 1 (il faut compter la requête) soit 0 (ne pas compter cette requête)
    }

    emit((mois, tranche), (Max(map), req));

    // demie heure -- nom du mot clé le + recherché et nombre de requêtes reçues
    // str key, (String, int)
}

/* En pratique, il aurait fallu définir une structure ayant deux champs :
   un String (le mot clef) et un int (indique s'il faut compter la requête ou non). */
```

L'exercice demande un fichier de sortie par mois, donc il n'y a pas besoin de faire de modulo dans le `return id(mois) -1`.
Chaque reduce produit ses données dans son propre fichier ?


## Exercice 3

Voir photos (écriture de Jonathan, denrières photos)

### 3.1

Solution que j'ai trouvée :

```java
// le nombre de personnes qui l’ont écouté au moins une fois (en local ou en radio)
// key est le numéro de ligne du log (on va dire) et value le log.
map(entier key, String value) {
    nbLocal = extraireNbEcoutesLocal(value);
    nbRadio = extraireNbEcoutesRadio(value);
    nbEcoutesTotal = nbLocal + nbRadio;
    if (nbEcoutesTotal > 0) {
        trackID = extractTrackID(value);
        emit(trackID, 1);
    }
}

reduce(int trackID, liste de int values /*osef*/) {
    int count = 0;
    for value in values {
        count++; // un utilisateur a écouté le morceau
    }
    emit(trackID, count);
}
```

Solution du prof (+ synthétique) :

```java
Map(entier key, String value) {
    if (extractLocal(value) + extractRadio(value) > 0) {
        emit(extrackTrackId(value), 1);
    }
}

Reduce(String trackId, List<Integer> cpts) {
    emit(trackId, cpts.size());
}
```

### 3.2.

En premier, voici la solution que j'ai trouvée :

```java
Map(entier key, String value) {
    nbLocal = extraireNbEcoutesLocal(value);
    nbRadio = extraireNbEcoutesRadio(value);
    nbEcoutesTotal = nbLocal + nbRadio;
    nbSkips = extraireNbSkips(value);

    if ( (nbEcoutesTotal > 0) || (nbSkips > 0) ) {
        trackID = extractTrackID(value);
        emit(trackID, (nbEcoutesTotal, nbSkips));
    }
}

Reduce(entier key, liste de (int ecoutes, int skips) values) {
    int nbEcoutes = 0;
    int nbSkips = 0;

    for value in values {
        nbEcoutes += value.ecoutes;
        nbSkips += value.skips;
    }

    emit(key, (nbEcoutes, nbSkips));
}
```

Voici la solution du prof :

```java
Map(entier key, String v) {
    emit(extractTrackId(v),
         (extractLocal(v) + extractRadio(v), extractSkip(v)) );
}

Reduce(String trackId, List of <Entier, Entier> values) {
    (entier, entier) res = (0, 0);
    foreach v in values {
        res = res + v; // le + est ici à definir, enfin, comme dans ma solution en gros
    }
    emit(trackId, res);
}
```

### 3.3.

Alors voici ma solution détaillée, basée sur celle du prof :

```java
// Là, on a donc deux map qui vont émettre simultanément.

// Emission des résultats du job 1
map1(int key, String value) {
    emit(extractTrackId(value), (extractNbListener(value), 0, 0));
}

// Emission des résultats du job 2
map2(int key, String value) {
    emit(extractTrackId(value),
         (0, extractNbListening(value), extractNbSkips(value)) );
}

reduce(String trackId, List<int listener, int listening, int skips> values) {
    int nbListener = 0;
    int nbListening = 0;
    int nbSkips = 0;

    for value in values {
        nbListener += value.listener;
        nbListening += value.listening;
        nbSkips += value.skips;
    }

    emit(trackId, (nbListener, nbListening, nbSkips))
}
```

Ces corrections peuvent être retrouvées sur les photos que j'ai prises du tableau. Elles ont été recopiées ici intégralement et agrémentées de commentaires et d'explications.

*Sur mes photos : 1) tableau de gauche, 2) tableau juste à droite, 3) tableau tout à droite*

*Le + entouré signifie qu'il est à définir, c'est une addition membre à membre (élément de droite avec élément de droite, élément de gauche avec élément de gauche)*







**Questions à poser au prof ou à mes camarades :**
- Est-ce que chaque reduce produit ses données dans son propre fichier ? (exo 2.2)
- 


# Cours 2 - 2020-10-13

(notes du 2020-10-19 parce que je suis giga à la bourre)

Je crois que le prof a dit que sa version d'Eclipse est la 2020-06 comme elle est toujours compatible avec java 1.8, alors que la version 2020-09 n'est plus que compatible avec java 11.

Mon propre chemin vers ma version de Java : `/usr/lib/jvm/java-8-openjdk-amd64`.

# Génération des clefs SSH en local

Solution par le merveilleux Username :

```
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

## TME 2 préparatoire

Reboot total de Yarn et HDFS :

```
stop-dfs.sh && stop-yarn.sh && hdfs namenode -format && start-dfs.sh && start-yarn.sh && check_start.sh hdfs && check_start.sh yarn
```

Chaque `&&` permer d'exécuter la commande suivante si la précédente n'a pas échoué.

Avec votre navigateur, trouvez à quel service web chaque serveur correspond (ne s’intéresser qu’aux numéros de port supérieurs à 1000) :
`It looks like you are making an HTTP request to a Hadoop IPC port. This is not the correct port for the web interface on this daemon.`

Accès à un DataNode : http://0.0.0.0:9864/datanode.html

Hadoop : http://0.0.0.0:8042 et 8088

Pas grand chose : http://0.0.0.0:9868/status.html

Un autre truc : http://0.0.0.0:9870/dfshealth.html#tab-overview

13562 -> Incompatible shuffle request version

Encore un dataNode : http://0.0.0.0:43579/datanode.html

### Exo 3 étape 4

yarn jar <Fich jar> <nom absolu classe main> <fich entree> <dossier sortie>

yarn jar WordCount.jar mapreduce.WordCount /loremIpsum /

### Exo 3 étape 5


# Cours 3 - 2020-10-20

Jonathan Lejeune - Aussi, pour DataCloud, la date de rendu des TP est indicative, c'est pour nous pousser à le faire vite.

Jonathan s'occupe de l'aspcet plus pratique de cette UE. Ce qu'on fera en TP aujourd'hui pourra être réutilisé dans notre projet. Il y aura un projet en ARA.

# Cours 4 - 2020-10-27

Tous les TP comptent pour 15% de la note finale en DataCloud.

`A1 :> A` signifie A1 est de type A ou un sur-type de A.

p15 du cours : `([identifier: type, ... ]) =><expression>` signifie : `(la liste des paramètres sous la forme nom_paramètre : type paramètre, ....) => corps de la fonction (qu'est-ce que ça fait)`

`<function2>` singifie "une instance du type "function2"" i.e. instance du type fonction à deux arguments.

p.17 : `trait Function0 [+ R ] extends AnyRef` : ça veut dire que si j'utilise un sous-type de R, alors le résultat sera aussi un sous-type de R (exemple avec Panier[Pomme] qui est un sous-type de Panier[+Fruit]), comme on l'a déjà vu en cours dans des dispos précédentes, c'est la covariance. Le `-` symbolise la contra-variance (c'est le contraire, so on prend un sur-type alors le résultat sera lui-même un sur-type etc.)

```scala
scala > val egal = ( a : Any , b : Any ) = > a == b
egal : ( Any , Any ) = > Boolean = < function2 >
```

c'est commé écrire :

```scala
scala > val egal: ( Any , Any ) = > Boolean = ( a : Any , b : Any ) = > a == b
egal : ( Any , Any ) = > Boolean = < function2 >
```

le type de egal étant `( Any , Any ) = > Boolean`.



