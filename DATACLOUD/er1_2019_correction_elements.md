# Corrections DataCloud 2020-11-24

Dans ce document, il y a des élements de correction du TME 1 de DataCloud, et la correction de l'annale de 2019, ER1. Attention, cette correction peut comporter des erreurs de recopie.


## Elements de correction TME-1 Map-Reduce

### Q6

170Mo => 2 maps => 1 map qui traite 128Mo et un map qui traite 42Mo.

### Q7

150Mo + 20Mp => 3 maps
- 1 map qui traite 128 Mo fich1
- 1 map qui traite 22 Mo fich1
- 1 map qui traite 20 Mo fich2

### Q9

Conclusion => par défait nbsplit = nbbloc (par défait 128 Mo)

### Q10

Il y a 140 Mo à traiter.

attendu :
- 1 map qui fait 128 Mo
- 1 map qui fait 12 Mo

Mais en réalité, il n'y a qu'un seul map.

Pour chaque fichier, on découte selon la taille du split et d'il reste un repliqua dont la taille est inférieure à 10% de la taille du split, on ajoute ce reste au dernier split créé.


# Correction Examen Réparti 1 - 2019/2020 1ère session

## Exercice 1



### Q1

```
Donnez les 3 V fondamentaux du Big Data
```

Volume, Vélocité, Variété.



### Q2

```
A quoi sert un partitioneur 1 ? Donnez un exemple.
```

- Un partitionneur est la politique de distribution des données à la sortie d'un shuffle.

- Le hachage de chaque clef qui va ensuite définir la partition sur laquelle la les données vont (respectivement) aller. (la fonction de hachage est supposée bien répartir)



### Q3

```
Le HDFS privilège-t-il le débit ou la latence ? Justifiez
```

- Débit : nombre de données que le système est capable d'émettre par unité de temps.
- Lattence : rapidité d'accès.

L'accès aux données est faite séquentiellement, on s'attaque à des données massives, pas à des données en particulier. D'où la lecture séquentielle qui fonctionne bien du coup. (s'il est implémenté (pas sur en 2020), le seek dans un fichier n'est absolument pas optimisé)



### Q4

DataNode -> repérer via l'absence de heartbeat (10mins) et réparer : il localise les autres copies des blocks qui étaient présents sur le DataNode, et les copie pour en avoir une quantité répliquée suffisante.

NameNode -> ça dépend de l'archi qu'on utilise (un Standby NameNode peut prendre immédiatement le relai si configuré) et sinon... bah je sais pas trop, c'est pas ouf vu que c'est ue leader quoi et que tous les échanges avec l'extérieur passent par lui...

Si le namenode crash et qu'on en a pas un autre, comment on fait ? (le namenode, c'est la vision globale, celui qui gère les accès, les métadata) Attention, les données (métadata) sont sur les DataNode, donc les données ne sont pas pardues. Zookeeper peut repérer que le NameNode ets down, et du coup en redémarer un nouveau (et restaurer depuis sa fsimage, image du filesystem).

Detécter : manuellement ou Zookeeper, réparer : recovery via fsimage.



### Q5

```
Dans YARN quel mécanisme empêche une application d’utiliser des conteneurs qui ne lui appar-
tiennent pas.
```

Chaque conteneur d'une application communique avec une clef. Un conteneur doit avoir une cef en commun avec un autre conteneur pour qu'ils puisset échanger.



### Q6

```
Définir la curryfication.
```

Selon le cours : Transformation d’une fonction à plusieurs arguments en une fonction à un argument qui retourne une fonction sur le reste des arguments.



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

Soit : 3 partitions pour stocker le fichier (et paralléliser les tâches). Et on pas 9 partitions ! La réplication, c'est une boite noire de notre point de vue.



### Q4

Voir les images associées.






## Exercice 3

Conseil : un RDD = une transformation, pour des raisons de lisibilité.

### Q1


```scala
def nbUsersByStationAndByhour(path : String, c : SparkContext) : RDD[((String, Int), Int)] = {
    val file = sc.textFile(path)
    val rdd1 = file.map(_.split(" "))
    val rdd2 = rdd1.map( x => ((x(2), x(0).split("_")(3).toInt), 1) )
    return rdd2.reduceByKey(_+_)
}
```


### Q2

```scala
val nb_total_station = 42 // le nombre total de stations du réseau
def nbUsersByStationAndByhour2(path : String, c : SparkContext) : RDD[((String, Int), Int)] = {
    val file = sc.textFile(path)
    val rdd1 = file.map(_.split(" "))
    val rdd2 = rdd1.map( x => ((x(2), x(0).split("_")(3).toInt), 1) )
    return rdd2.reduceByKey( new StationPartitioner(nb_total_station)._+_ )
}

class StationPartitioner(val numStation : Int) extends Partitioner {
    override def numPartitions : Int = numStation
    override def getPartition(key : Any) : Int = {
        key match {
            case (station : String, heure : Int) => station.hashCode() % numStation
            case _ => 0
        }
    }
}
```



### Q3

```scala
def proportionTicketCarteByStation(path : String, sc : SparkContext) : RDD[(String, (Double, Double))] = {

    val file = sc.textFile(path)      // RDD[String]

    val rdd1 = file.map(_.split(" ")) // RDD[Array[String]]

    val rdd2 = rdd1.map(x => (x(2), x(1)))

    val rdd3 = rdd2.mapValues(badge =>
        if (badge.contains('t')) (1,0) else (0,1))

    val rdd4 = rdd3.reduceByKey( (c1, c2) => (c1._1 + c2._1, c1._2 + c2._2) )

    val rdd5 = rdd4.mapValues( c =>
        (c._1.toDouble / (c._1+c._2).toDouble,
         c._2.toDouble / (c._1+c._2).toDouble) )

    val rdd6 = rdd5.mapValues( c => (c._1*100.0, c._2*100.0))

    return rdd6
}
// Jonathan préfèce cette solution car la somme des valeurs est faite dans le shuffle,
// alors ue dans la solution alternative suivante tout se fait dans une partition.
// (et que du coup on ne profite pas du shuffle pour faire les calculs)
```

Autre manière de faire moins opti :

```scala
// Autre manière de faire moins opti :
def proportionTicketCarteByStation2(path : String, sc : SparkContext) : RDD[(String, (Double, Double))] = {

    val file = sc.textFile(path)      // RDD[String]

    val rdd1 = file.map(_.split(" ")) // RDD[Array[String]]

    val rdd2 = rdd1.map(x => (x(2), x(1)))

    val rddticket = rdd2.filter(_._2.startsWith("t")).mapValues(s => 1.0)
    val rddcarte = rdd2.filter(!_._2.startsWith("t")).mapValues(s => 1.0)
    val rddgroup = rddticket.cogroup(rddcarte)

    val rdd5 = rddgroup.mapValues( c =>
        (c._1.reduce(_+_) / (c._1.size+c._2.size).toDouble,
         c._2.reduce(_+_) / (c._1.size+c._2.size).toDouble) )

    val rdd6 = rdd5.mapValues( c => (c._1*100.0, c._2*100.0))

    return rdd6
}
```




### Q4

```scala
val sc = new SparkContext(new SparkConf().satAppName("exam"))
val proportion = proportionTicketCarteByStation("fichier", sc)
val res = proportion.mapValues(c => Math.abs(c._1-c._2)).sortBy(_._2, false)
println(res.first())
```



### Q5

`Alors il faudra que je reprenne cette partie, ça me prend pas mal de temps de recopier et comme j'ai pas bien suivi et pas bien compris cette partie, je reprendrai ça plus tard. (si je le reprend un jour...)`
