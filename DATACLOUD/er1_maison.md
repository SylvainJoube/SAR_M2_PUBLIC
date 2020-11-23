# DATACLOUD : mes réponses en Datacloud + correction

## Exercice 1

### Q1

Volume, Vélocité, Variété.

### Q2

Un partitioneur sert à répartir les données entre les différents noeuds ?
Exemple : Une base de données dont une partie des clefs-valeurs sont stockées dans ds noeuds distincts.

### Q3

HDFS privilégie le débit, comme son but premier est de stocker des données massives.

### Q4

DataNode -> repérer via l'absence de heartbeat (10mins) et réparer : il localise les autres copies des blocks qui étaient présents sur le DataNode, et les copie pour en avoir une quantoté répliquée suffisante.

NameNode -> ça dépend de l'archi qu'on utilise (un Standby NameNode peut prendre immédiatement le relai si configuré) et sinon... bah je sais pas trop, c'est pas ouf vu que c'est ue leader quoi et que tous les échanges avec l'extérieur passent par lui...

### Q5

Alors là, je ne sais pas du tout comment YARN gère une application, et comment il l'empèche d'utiliser des conteneurs qui ne lui appartiennent pas. Je suppose qu'il crée un système de fichiers propre à l'application en lui faisant une sandbox, mais je sais pas trop...

### Q6

Curryfication : passer d'une fonction à n arguments à n fonctions à 1 argument. *Selon le cours :  Transformation d’une fonction à plusieurs arguments en une fonction à un argument qui retourne une fonction sur le reste des arguments.*

### Q7

La fonction f n'est pas pure car son résultat dépend d'une variable du contexte de l'objet. (mais si on avait val à la place de var, je pense qu'elle serait pure)



## Exercice 2

### Q1

lines : RDD[String]
links : RDD[(String, Iterable[String])]
ranks : RDD[(String, Double)]
(join : RDD[(String, (Itarable[String], Double))])
values : RDD[Iterable[String], Double]
tmp : RDD[(String, Double)]

### Q2

Le contenu de la variable links va être accédé de nombreuses fois, il est donc judicieux de pouvoir y accéder rapidement, via la mémoire vive.

### Q3

Soit : 3 partitions pour stocker le fichier (et paralléliser les tâches), soit 9 partitions s'il y a une réplication x3, mais je sais pas trop en détail comment ça fonctionne...

### Q4

WTF c'est quoi cette question OMD. Je pense que la correction me sera bien nécessaire pour le coup...

Je suppose que le groupBuKey garde le même nombre de partitions.
Alors réponse sur la feuille manuscrite.

## Exercice 3

### Q1

```scala
def nbUsersByStationAndByhour(url : String, c : SparkContext) : RDD[((String, String), Int)] = {
    val file = c.textFile(url) // On va dire ça marche...
    // 1) split en lignes
    val lines = file.flatMap(t => t.split("\n"))
    
    // 2) Slpit en tranche horaire et station ()
    val hr = lines.map( l => {
        val sp = l.split(" ")
        val heure = sp.apply(0)
        val station = sp.apply(2)
        val sh = heure.split("_")
        val keyh = sh(0) + "_" + sh(1) + "_" + sh(2) + "_" + sh(3)
        
        ((station, keyh), 1)
    })

    // Agrégation en fonction de la clef (station, heure)
    val nb = hr.reduceByKey(_+_)

    return nb
}
```

### Q2

J'en ai fichtrement aucune idée... Je suppose que ça doit être un paramètre à passer au map ?

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
        if (pass.chatAt(0).equals("t")) {
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