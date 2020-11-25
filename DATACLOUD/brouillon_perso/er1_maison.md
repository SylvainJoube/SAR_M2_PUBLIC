# DATACLOUD : mes réponses en Datacloud + correction

-> Quand on sait pas, ne rien mettre.



## Exercice 1

### Q1

Volume, Vélocité, Variété.

### Q2

C'est : La politique de distribution des données à la sortie d'un shuffle. qui permet de répartir les données à la sortie d'un shuffle.

C'est le hachage de la clef qui va ensuite définir la partition sur laquelle la donnée va aller. (la fonction est supposée bien répartir)



### Q3

HDFS privilégie le débit, comme son but premier est de stocker des données massives.

Débit : nombre de données que le système est capable d'émettre par unité de temps.
Lattence : rapidité d'accès.

L'accès aux données est faite séquentiellement, on s'attaque à des données massives, pas à des données en particulier. D'où la lecture séquentielle qui fonctionne bien du coup. (s'il est implémenté (pas sur en 2020), le seek dans un fichier n'est absolument pas optimisé)

### Q4

DataNode -> repérer via l'absence de heartbeat (10mins) et réparer : il localise les autres copies des blocks qui étaient présents sur le DataNode, et les copie pour en avoir une quantité répliquée suffisante.

NameNode -> ça dépend de l'archi qu'on utilise (un Standby NameNode peut prendre immédiatement le relai si configuré) et sinon... bah je sais pas trop, c'est pas ouf vu que c'est ue leader quoi et que tous les échanges avec l'extérieur passent par lui...

Si le namenode crash et qu'on en a pas un autre, comment on fait ? (le namenode, c'est la vision globale, celui qui gère les accès, les métadata) Attention, les données (métadata) sont sur les DataNode, donc les données ne sont pas pardues. Zookeeper peut repérer que le NameNode ets down, et du coup en redémarer un nouveau (et restaurer depuis sa fsimage, image du filesystem).

Détécter : manuellement ou Zookeeper, réparer : recovery via fsimage.

### Q5

Alors là, je ne sais pas du tout comment YARN gère une application, et comment il l'empèche d'utiliser des conteneurs qui ne lui appartiennent pas. NON -> Je suppose qu'il crée un système de fichiers propre à l'application en lui faisant une sandbox, mais je sais pas trop... <- NON

Chaque conteneur d'une application communique avec une clef. Un conteneur doit avoir une cef en commun avec un autre conteneur pour qu'ils puisset échanger.


### Q6

Selon le cours :  Transformation d’une fonction à plusieurs arguments en une fonction à un argument qui retourne une fonction sur le reste des arguments. *ça, c'est incomplet : Curryfication : passer d'une fonction à n arguments à n fonctions à 1 argument.*

### Q7

La fonction f n'est pas pure car son résultat dépend d'une variable du contexte de l'objet. (même si on avait val à la place de var)



## Exercice 2

### Q1

lines : RDD[String]
links : RDD[(String, Iterable[String])]
ranks : RDD[(String, Double)]
(join : RDD[(String, (Iterable[String], Double))])
values : RDD[Iterable[String], Double]
tmp : RDD[(String, Double)]

(au passage `lines.map(_.split(" "))` est un `RDD[Array[String]]`)

### Q2

Le contenu de la variable links va être accédé de nombreuses fois, il est donc judicieux de pouvoir y accéder rapidement, via la mémoire vive. Il ne faut pas que sa mémoire soit recyclée, et il ne faut surtout pas avoir à la recalculer.

### Q3

correction : 1h37

Soit : 3 partitions pour stocker le fichier (et paralléliser les tâches). Et on pas 9 partitions ! La réplication, c'est une boite noire de notre point de vue.

### Q4

WTF c'est quoi cette question OMD. Je pense que la correction me sera bien nécessaire pour le coup...

Je suppose que le groupBuKey garde le même nombre de partitions.
Alors réponse sur la feuille manuscrite.

Lors d'un join, il n'y a pas de changement du nombre de partitions lorsqu'il y a le même nombre de partitions pour les deux RDD.

Bien penser à dessiner les flèches du shuffle depuis les deux RDD desquels on fait un join.

L'optimisation faite par Spark est que via reduceByKey, on est assuré que les clefs sont sur les mêmes partitions et du coup on est juste en local.

## Exercice 3

### Q1

```scala
def nbUsersByStationAndByhour(url : String, c : SparkContext) : RDD[((String, Int), Int)] = {
    val file = c.textFile(url) // On va dire ça marche...
    // 1) split en lignes
    val lines = file.flatMap(t => t.split("\n"))
    
    // 2) Slpit en tranche horaire et station ()
    val hr = lines.map( l => {
        val sp = l.split(" ")
        val heure = sp.apply(0)
        val station = sp.apply(2)
        val sh = heure.split("_")
        /*val keyh = sh(0) + "_" + sh(1) + "_" + sh(2) + "_" + sh(3)*/
        val keyh = sh(3).toInt
        
        ((station, keyh), 1)
    })

    // Ou immédiatement :
    val hr = lines.map(l => )

    // Agrégation en fonction de la clef (station, heure)
    val nb = hr.reduceByKey(_+_)

    return nb
}
```

### Q2

J'en ai fichtrement aucune idée... Je suppose que ça doit être un paramètre à passer au map ?

Il faut écrire une partitionneur :



### Q3

```scala
def proportionTicketCarteByStation(url : String, c : SparkContext) : RDD[(String, (Double, Double))] = {
    val file = c.textFile(url)

    val lines = file.flatMap(t => t.split("\n"))

    val st = lines.map( l => {
        val sl = l.split(" ")
        val pass = sl(1)
        val station = sl(2)

        // 1 seul élément, donc pass
        if (pass.contains("t")) {
            (station, (0, 1))
        } else {
            // pass
            (station, (1, 0))
        }
    })

    // Agrégation du nombre d'usagers
    val agr = st.reduceByKey((a, b) => (a._1 + b._1, a._2 + b._2))

    val res = agr.map(s => {
        val station = s._1
        val nbPass = s._2._1
        val nbTicket = s._2._2
        // En théorie, on a jamais (nbTicket + nbPass == 0)
        val total = nbPass + nbTicket

        (station, (nbPass * 100 / total, nbTicket * 100 / total))
    })

    return res
}
```

### Q4

```scala
object MyApp {
    def main(args : Array[String]) : Unit = {

        val myName = args(0)

        val conf = new SparkConf().setAppName("Métrododo")
        val sc = new SparkContext(conf)

        val prop = proportionTicketCarteByStation("fileURL", sc)

        // 1) transformer en valeur numérique le tuple
        // RDD[(String, Double)] : le nom de la station et l'écart entre les deux nombres
        val ratio = prop.map(s => {
            val abs = (s._2._1 - s._2._2).abs
            (s._1, abs)
        })

        // 2) classer en fonction du tuple
        // Je veux le plus grand ratio en premier
        val ordered = ratio.sortBy(t => t._2, false)

        // Alors je sais pas très bien comment faire alors j'aurais bien fait un collect...
        // ça va collect juste les stations et le nombre de visites,
        // donc beeeeeeaucoup moins de données que les logs
        // Mais globalement, juste récupérer le nom de la première station...

        val winner = ordered.max(..)

        // 3) si le nom de cette station est mon nom, alors j'affiche
        if (myName.equals(winner)) {
            println("C'est la station !!")
        }

    }


}

```

Conseil : un RDD = une transformation, pour des raisons de lisibilité.

Là je m'étais planté, c'est par tranche d'une heure et pas par date avec tranche d'une heure.

### Q1

```scala
def nbUsersByStationAndByhour(url : String, c : SparkContext) : RDD[((String, Int), Int)] = {
    val file = c.textFile(url)
    val rdd1 = file.map()

}
```

J. le fait d'une manière méthodique, en faisant bien un RDD par transformation.