# Correction ER2

## Exercice 2 

1) B, C
2) aucune s'ile ne partagent pas le même serveur, A et D s'ils partagent le même serveur.
3) C, D
4)
- A Le leader choisi est celui qui a l'historique le plus à jour, i.e. le plus gros/long
- pas C : on veut juste garantir la cohérence séquentielle des écritures, les lectures n'ont pas à passer par lui. Le leader est un noeud comme un autre sauf qu'il a la mission de séquentialiser les écritures. En Zookeeper, tout le monde héberge toutes les données.
5) B, classifiés en 4 famille, NoSQL n'est pas un langage
6) B, C, D
7) A
8) B
9) B, compromis entre AP et CP, D (Cassandra offre un typage des données)
10) A, C

## Exercice 3

### 1)

Dans Cassandra, la primary key est utilisée par la fonction de hash pour la répartition via la DHT. CONSISTENCY définit la nombre de réplicas auxquels on va s'adresser. Lors du read on attend les valeurs (et on met à jour les valeurs obsolètes si besoin, on prend la valeur ayant le timestamp le plus récent). Pour une écriture, on attend juste une majorité de ack de la part des réplicas. On attendra 3 réponses car notre facteur de réplication est 5 ici (code CQL).

### 2

Application du connecteur Cassandra-Spark

On peut soit utiliser du cassandra raw soit des types.

 ```scala
package exam

import org.apache.spark.SparkConf
import org.apache.spark.SparkContext

import com.datastax.spark.connector._

object Dixheures extends App {

    // sc variable globale à l'objet.
    val sc = new SparkContext(new SparkConf().setAppName("DixHeures")
        .setMaster(this.args(0))
        .set("spark.cassandra.connection.host", this.args(1)))
    
    // On renseigne les attributs de User dans la même ordre que dans CQL :
    case class User(iduser : String, mail : String, birthyear : Int)

    // Ici, author correspond à un[] idartist, mais c'est à nous de l'assurer
    // comme on a pas de clef étrangère en Cassandra
    case class Track(idtrack : String, title : String, duration : Int, author : String)

    case class Artist(idartist : String, name : String, biography : String)

    case class Favorite(iduser : String, idtrack : String)

    // Si on mettait pas de typage, on aurait une valeur de type Cassandra row
    def loadUsers() : RDD[User] = sc.cassandraTable("dixheures", "User")
    // sc.cassandraTable(keyspace, table)

    // En exam, on a le doit de ne pas tout recopier à chaque fois et simplement
    // dire ce qui change de loadUsers à loadTracks (etc.).
    def loadTracks() : RDD[Track] = sc.cassandraTable("dixheures", "Track")

    def loadArtists() : RDD[Artist] = sc.cassandraTable("dixheures", "Artist")

    def loadFavorites() : RDD[Favorite] = sc.cassandraTable("dixheures", "Favorite")

    // Fonctions pour :
    // lecture  = cassandraTable
    // écriture = saveToCassandra

    // Création du RDD à partir du SparkContext
    def addFavorite(f : Favorite) = sc.parallelize(Seq(f), 1).saveToCassandra(
        // keyspacenName, tableName, columns, writeConf
        "dixheures", "Favorite", AllColumns)
    // RDD[Favorite].
    // Le driver Cassandra fait automatiquement le lien entre ce RDD et les données à stocker dans la table.


    // Q4
    // Spark nous offre un mécanisme de jointure avec JOIN
    def getAverageListenerAgePerNameArtist() : RDD[(String, Double)] = {
        val favorites = loadFavorites() 
        val users = loadUsers()
        val tracks = loadTracks()
        val artists = loadArtists()

        // Pour le jointure, il faut associer un track avec l'ensemble des utilisateurs qui l'écoutent
        // Jointure entre Favorite et Track
        // Il faut transformer notre RDD[Favorite] en RDD[idtrack, iduser]

        val rdd_idtrack_artist = tracks.map(t => (t.idtrack, t.author))

        val rdd_login_track = favorites.map(f => (f.iduser, f.idtrack))

        val rdd_user_age = users.map(u => (u.iduser, curyear - u.birthyear))

        // Jointure : retourne un RDD[iduser, (idtrack, age)]
        val rdd_login_idtrack_age = rdd_login_track.join(rdd_user_age)

        // On veut un RDD[idtrack, age]
        val rdd_idtrack_age = rdd_login_idtrack_age.map(f => (_._2))

        val rdd_idtrack_idartist_age = rdd_idtrack_artist.join(rdd_idtrack_age)

        val rdd_idartist_age = rdd_idtrack_idartist_age.map(_._2)

        val rdd_idartist_name = artists.map(a => (a.idartist, a.name))

        val rdd_idartist_nameartist_age = rdd_idartist_name.join(rdd_idartist_age)

        val rdd_nameartist_age = rdd_idartist_nameartist_age.map(_._2)

        // Il est possible qu'il  y ait un petit ajustement à faire sur cet exercice
        // le refaire et comparer.

        // Calcul via Spark
        val rdd = rdd_nameartist_age
            .mapValues((_, 1))
            // On fait la somme de l'âge et la somme des coefficients
            .reduceByKey((c1, c2) => (c1._1 + c2._1, c1._2 + c2._2))
            .mapVales(c => c._1.toDouble / c._2.toDouble)

        return rdd
    }
}
 ```


### 5

Dénormaliser permettrait d'augmenter les performances, et permettrait de faire moins de join.

Proposition : réunir piste et auteur. (ex : supprimer Auteur et le mettre dans Track)

On pourrait juste maintenir un compteur sur la table Artiste, i.e. la moyenne par artiste (à chaque fois qu'un utilisateur rajoute un favori). On rajoute une 4ème colonne à Artiste : l'âge moyen de ses fans. Du coup pour recalculer la moyenne à partir de la moyenne déjà existante, on fait : `(moyenne_âge * nb_personnes + âge) / (nb_personnes + 1)`.

### 6

Pour penser la structure, il faut bien comprendre la requête majoritaire qu'on veut faire. Ici, c'est le classement de la musique la plus écoutée à la moins écoutée. A la fin, on veut faire un group by idtrack. On voudrait avoir la somme des écoutes directement dans la ligne. La rowkey va être le idtrack. Une ColumnFamily est une colonne physique (cf stockage par colonne en HBase). On pourrait très bien avoir une seule colonne : le temps cumulé d'écoute, pour chaque track, sauf qu'on cherche à garder iduser selon l'énoncé : on cherche à connaître ele temps cumulé associé à un track et un utilisateur donné. `idtrack` peut être la rowkey, et iduser peut être une colonne. Une seule `columnFamily` dans laquelle on pourra rajouter des colonnes à la volée (on ne peut pas ajouter/supprimer de ColumnFamily, mais on peut avec les colonnes). On aurait alors pour chaque track, par user le temps cumulé d'écoute (ajout de colonne à la volée, représentant les iduser). Et si la colonne (iduser) existe déjà pour un idtrack, on ajoute le temps d'écoute, comme une bête table de hashage.
- rowkey = idtrack
- columnFamily = une seule
- columns = idusers
- valeurs = temps cumul]]é d'écoute pour un (idtrack, iduser)

-> il faut toujours de poser la question de savoir par rapport à quoi on regroupe. L'agencement d'une table doit dépendre de la manière dot on l'interroge après.

On peut voir le NoSQL comme une table de hachage distribuée et persistante.


Et pour avoir le temps cumulé d'écoute, il suffit de faire un reduce de chaque ligne.

### 7

`Mutation` est un descripteur de requête d'écriture pour interroger HBase.

```scala

import org.apache.spark.SparkConf
import org.apache.spark.StreamingContext
import org.apache.spark.SparkContext
import org.apache.spark.streaming.Miutes
import org.apache.hadoop.hbase.client.Increment
import org.apache.hadoop.hbase.util.Bytes
import hbase.SparkHbase._

object Stream extends App {
    val conf = new SparkConf().setAppName("Stream Listening")
    val sc = new SparkContext(conf)

    // Toutes les 5 minutes, produire un RDD des écoutes qui se sont passées
    // les 5 dernières minutes.
    val ssc = new StreamingContext(sc, Minutes(5))

    val stream1 = ssc.socketTextStream("listenstat.dixheures.com", 532)

    // iduser, idtrack, temps d'écoute pour cette écoute
    val stream2 = stream1
    .map(s => s.split(" "))
    .map(a => (a(0), a(1), a(3).toInt - a(2).toInt))

    // Transformer chaque élément (tuple) en incrément
    // 1) instancier l'incrément pour un row donné
    // 2) faire addColumn (cf. page 7 du sujet)
    val stream3 = stream2
    // rowkey = idtrack = t._1
    // toBytes car tout est 
    .map(t => new Increment(Bytes.toBytes(t._2))
                // addColumn(columnFamily, qualifier, valeur)
                .addColumn(Bytes.toBytes("cf"), Bytes.toBytes(t._1), t_.3))
    // Un Increment est un DStream de requête)
    // On choisit de nommer notre (seule) columnFamily en cf

    // On cherche à transformer un DStream en RDD
    // Rappel : un DStream est un ensemble de RDD dans le temps
    // on va donc utiliser forEachRDD :

    // saveAsHbaseTable(tableName, quorumzookeeper)
    stream3.foreachRDD(rdd => rdd.saveAsHbaseTable("ListeningStat", "zoo1:zoo2:zoo3"))
    // on pouvait laisser le quorum vide

    ssc.start()
    ssc.awaitTermination()
}

```

### 8

Le but de l'exercice est de montrer l'interopérabilité (en lecture) entre hbase et cassandra.

On ajoute dans le code de l'objet `object Dixheures` précédemment défini :


```scala
def trackRanking() : RDD[(String,Long)] = {
    val table_listening = sc.hbaseTableRDD("ListeningStat", new Scan(), "zoo1;zoo2;zoo3")
    
    val rdd_idtrack_cumullistening = table_listening.map(c =>
            (Bytes.toString(c._1.get), c._2.getFamilyMap(Bytes.toBytes("cf")).values().asScala
                                          .map(Bytes.toLong(_)).reduce(_+_))
            )
    val rdd_idtrack_cumullistening_sorted= rdd_idtrack_cumullistening.sortBy(_._2, false)
   
    val rdd_idtrack_titre = loadTracks().map(t=> (t.idtrack,t.title));
    return rdd_idtrack_titre.join(rdd_idtrack_cumullistening_sorted).map(_._2)
}
```

Pas le droit aux documents pour l'exam, on aura une annexe.
