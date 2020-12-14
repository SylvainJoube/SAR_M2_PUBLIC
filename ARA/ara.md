# ARA

Luciana :
```
    L'examen réparti 1 portera sur la partie de Pierre Sens et la mienne :
    - Protocole de diffusion
    - Mémoire partagée
    - Détecteur de fautes
    - Paxos

    Sans documents.

    Les TDs que nous avons faits ont été extraits des anciens examen.  Les exercices de l'examen seront du même type que les TDs.
```


Pour l'instant, nous n'avons pas de cours numérique, c'est juste un polycopié. En plus de ces notes de cours, voir mes notes manuscrites.

Je n'ai pas encore le cours de 2020 (pas encore mis en ligne sur Moodle ni donné par mail) mais j'ai mis le cours de 2017 : `Cours 1/Protocole_diffussion_2017.pdf`.

# Cours du 06-10-2020

Toutes mes notes sont ici pour le cours 1, (aucune note manuscrite pour ce cours, seulement pour le TD 1)

On aura un projet avec Jonathan sous PeerSim. Et rien d'étonnant, on aura aussi des TME.

Dans ces cours, par rapport à AR du S2, on va traiter le cas où il y a des fautes.

(p.9)
Le concensus en une phrase : tous les processus doivent se mettre d'accord sur une valeur commune.
**Processus correct** : processus qui ne tombe jamais en panne lors de l'exécution.
**validité** : (pour les Vi) : la valeur décidée est une des valeurs proposées initialement par un des processus.

**p.16**
reveive(m) est différent de deliver(m). Ça permettrait d'éviter certaines incohérences comme vu à la diapo p.13.

**p.19**
```
Un groupe peut être :
• fermé : broadcast(m) ne peut être appelé que par un membre du groupe
• ouvert : broadcast(m) peut être appelé par un processus extérieur au groupe
```

Diffusion Best Effort, Fiable, Fiable Uniforme : à partir de p.23
Ordre FIFO, Causal, Total : à partir de p.23

**p.24**
La garantie Best-effort est assez faible.

**Diffusion Best-effort** (p.24) -> Garantie la délivrance d'un message à tous les processus corrects si l'émetteur est correct.

**Diffusion Fiable (Reliable Broadcast)** (p.26) ->
- si l'émetteur du message m est correct, alors tous les destinataires corrects délivrent le message m.
- si l'émetteur du message m est fautif, tous ou aucun processus corrects délivrent le message m.

**Diffusion Fiable Uniforme (Uniform Reliable Broadcast)** (p.33) -> 
- Si un message m est délivré par un processus (fautif ou correct), alors tout processus correct finit aussi par délivrer m.

**p.27**
Validité = best effort

**p.28-29** : on est dans un système asynchrone. Chaque processus est supposé connaître tous les autres processus, tout le temps. (donc pas de latence du à un groupe dynamique etc. on la fait simple pour le moment...)

**Ordre** (p.36)

**Ordre Total**
- Les messages sont délivrés dans le même ordre à tous leurs destinataires.

**Ordre FIFO**
- si un membre diffuse m1 puis m2, alors tout membre correct qui
délivre m2 délivre m1 avant m2.

**Ordre Causal**
- si broadcast(m1) précède causalement broadcast (m2), alors
tout processus correct qui délivre m2, délivre m1 avant m2.

**p.40** : C'est pas représenté ici, mais il y a bien délivrance à l'application de chaque processus du message x = 1 avant le message x = 2.

**Types de Diffusion Fiable (1)** (p.41) :
- `Diffusion FIFO`              = Diffusion fiable + Ordre FIFO
- `Diffusion Causal (CBCAST)`   = Diffusion fiable + Ordre Causal
- `Diffusion Atomique (ABCAST)` = Diffusion fiable + Ordre Total
- `Diffusion Atomique FIFO`     = Diffusion FIFO + Ordre Total
- `Diffusion Atomique Causal`   = Diffusion Causal + Ordre Total

**p.43** : ce n'est pas un broadcast FIFO.

**p.45** : seq# signifie "numéro de séquence".
En gros, là, il s'agit de délivrer le message dans l'ordre des numéros de séquence.

**p.46** : le processus a donc un compteur de messages qu'il attend de la part du processus 2, et du coup quand il reçoit un message à l'identifiant trop élevé, il le met en attente et il attend de recevoir le message portant le numéro attendu. Et comme ça, tous les messages sont reçus dans l'ordre. Je suppose que ça nécessite juste une liste de messages en attente, et deux compteurs sur l'émetteur (le dernier numéro que j'ai envoyé à p) et un sur p : le next attendu.

**(p.52)** : Je crois *(à vérifier)* que chaque message est délivré lorsqu'il y a une différence de 1 entre l'horloge vectorielle locale et l'horloge vectorielle associée au message reçu.C'est à dire que le message est mis en attente jusqu'à ce que j'ai reçu tous les messages qui le précèdent causalement : le (1) signifie avoir reçu tous les broadcasts de l'émetteur du message (que ce message porte l'estampille que j'ai en local (pour le processus émetteur) + 1) et (2) signifie que je n'ai pas d'autres messages précédemment causalement cet envoi, et qui doivent dont arriver avant lui.  C'est d'ailleurs ce qui est expliqué sur le poly, en fait.

**p.63**
**\ token.liste do** : et pas dans la liste du token (`\` = `privé de`)

**p.66** : on suppose le processus s1 a le token.

**p.71** : N = temporaire, D = définitif
A chaque fois, on prend le numéro le plus grand, pour chaque date de message reçue. Lorsqu'un message est noté définitif, et qu'il est au début de la file, le processus sait qu'il peut délivrer le message à l'application. Puis, (p.72) tous les messages sont délivrés à l'application à l'étape 4.


## TD associé (du 06-10-2020)

-> **Une partie de mes notes sont manuscrites, rangées dans ma pochette `ARA`.**

### Exercice 1

1.1. C'est FIFO : b1 -> b2 (-> c'est "précède causalement") émis par P1.
Mais ce n'est pas causal, car b1 -> d1 -> b3 et b1 -> b3 mais P2 ne délivre pas m1 avant m3.
Pas total : P3 et P1, on a d1 d2 d3 mais sur P2 on a d3 d1 d2. *(rappel : causal = tout les messages sont reçus dans le même ordre, indépendamment de leur ordre d'émission)*

### Exercice 2 -> Voir sur ma copie manuscrite.

### Exercice 3

#### 3.1

Avantage de la réplication : bah c'est répliqué. Inconvénient : bah ça coûte cher.

#### 3.2

Au début, on a a = 0.
a + 10 signifie que C1 demande d'actualiser la valeur de a, en l'incrémentant de 10.

ABCAST / CBCAST (causalité) : ordre total (atomic broadcast)  / causalement ordonné (qui respecte la causalité des messages)

**CBCAST (ordre causal)**
Les relations de causalité sont les suivantes :
- a*3 <=> a + 10 i.e. les deux événements sont indémendants.
- a*3 -> a*2
- a+10 -> a*2

Ce qu'on peut avoir (ça ne garantit pas que les serveurs auront la même valeur) :
```
On peut donc juste intervertir l'ordre de (a*3) et de (a+10), comme ces deux événements précèdent causalement a*2.
((a+10)*3)*2 = 60
((a*3)+10)*2 = 20
```

Dans le cas de l'ordre ABCAST (total) : l'ordre dans lequel ils reçoivent les messages n'est pas connu, ce qui est garanti est que tous les serveurs auront reçu les messages dans le même ordre, et donc qu'ils auront la même valeur. Les relations de causalité n'influent en rien sur l'ordre de réception. Tous les scénarios sont donc possibles, ils sont dénombrés ici :
```
((a*3)+10)*2
((a*3)*2)+10
((a*2)*3)+10
((a*2)+10)*3
((a+10)*2)*3
((a+10)*3)*2
```

### Exercice 5

#### Question 5.1

Comme vu en cours, l'algo de diffusion fiable envoie N * (N - 1) messages par broadcast : Un processus qui fait un broadcast envoie aux N processus (dont lui-même). Un processus qui reçoit un message regarde s'il l'a déjà reçu. S'il ne l'a pas déjà reçu, il l'envoie à tout le monde sauf lui (N-1) processus.

**Détecteur parfait**
**Complétude forte** : Toute faute sera détectée par un détecteur (i.e. un processus en panne finira par être 'suspecté').
**Justesse forte** : aucun processus correct ne sera jamais suspecté.

Le détecteur parait suppose qu'il ne peut pas se tromper.

Si un processus `q` tombe en panne :
```
upon<crash, q>
    correct'p = correct'p \ {q}
```

(ici, le 'p signifie p en indice, je ne savais pas comment le noter)

*Question typique d'examen :*
```
Real_broadcast(m)
    send<m> to ∀ i in correct and i != p;

upon recv<m> from q
    if m ∉ from[q]        (n'appartient pas à)
        from[q] U= {m};   (union égal à, on ajoute m à l'ensemble)
    if q ∉ correct
        send<m> to ∀ i ∈ correct and (i != p)

upon<crash, q>
    correct = correct \ {q};
    for all <m> ∈ from[q]
        send<m> to ∀ i ∈ correct and (i != p)
```

#### Question 5.2.

On prend x le nombre de messages reçus par q avant la panne de p. i.e. tous les messages sauvegardés dans from[q]. Le nombre de messages dans from[q] est x.

On suppose que lorsque p tombe en panne, il y a k processus fautifs. La taille de correct est donc PI - k. (PI représente l'ensemble de tous les processus)
Le nombre de messages envoyés par un seul processus lors de la détection de la faute de p sera donc x * (N - 1 - k). (i.e. envoi à tous les corrects sauf lui-même)

Si on considère que tous les processus détectent la faute de p, on aura N - k corrects. Donc le nombre total de messages envoyés sera (N - k) * (N - 1 - k) * x. Cela ne se passera que lorsque la faute sera détectée.

#### Question 5.3.

Avantage : pas de faute => moins de renvoi de messages
Inconvénient : il est nécessaire de sauvegarder tous les messages dans la variable from des autres processus. Parce qu'on ne sait pas quand un processus va tomber en panne.
Dans l'algo vu en cours, en cas de panne, chaque processus envoie (N - 1) * x messages. La différence avec l'algo vu à cet exercice est faible.

#### Question 5.4.

<>P = diamond P.
Ça veut dire qu'un processus peut se tromper (ajoute q à son détecteur, puis l'enlève à un moment, quand il se rend compte que q n'est en fait pas en panne). "Tout processus qui tombe en panne sera suspecté au bout d'un moment". Le diamond P est partiellement synchrone.

Par la complétude forte, ça marchera quand-même avec <>P au lieu de P. On renverra les messages du processus fautif aux autres processus. Mais ça marchera aussi si P est toujours actif (pas en panne) mais ça ne sera vraiment pas efficace en terme de nombre de message. Cet algo sera juste moins performant, c'est tout.

### Exercice 6

*A REVOIR*
*Reprendre cet exercice à tête reposée, je suis trop fatigué là*

#### 6.1.

Non. Comme l'émetteur est fautif, il est impossible de garantir que ses messages seront bien délivrés.

#### 6.2.

Pour chaque ack envoyé, il faut incrémenter le compteur de confirmations qu'on a eu pour ce message.

```
correct = PI
pend = void   // messages en attente de livraison
delv = void   // messages livrés
ack[] = void  // initialement noté ack'm[m] ('m => m en indice) mais je trouvais pas ça clair, j'ai transformé en ack[m] simplement.

Unif_real_broadcast(m)
{
    // sauvegarde du message à chaque fois
    // Le message est en attente de livraison
    pend U= {m}
    - estampiller m avec send(m) et seq#(m)  (celui qui l'a envoyé et ne numéro de séquence local au processus) ;
    - envoyer m à tous les processus (appartenant à) correct ;
}

// Le ⊆ signifie contenu dans (C souligné)

upon recv <m> from q :
{
    ack[m] U= {q}

    if (m ∉ pend and m ∉ delv (delivered))
        pend U= {m}
        envoyer m à tous les processus corrects sauf p.
        // -> c'est comme si c'était un ACK : on rediffuse le message.

    // Si tous les processus corrects ont répondu
    if (correct ⊆ ack[m]) {
        deliv U= {m}
        pend = pend \ {m}
        Unif_real_deliver(m);
    }
}

upon <crash, q>
    correct = correct \ {q}
    for all m ∈ pend

    // Je livre tous les messages queje peux livrer
    if ( (m ∉ deliv) && correct ⊆ ack[m]) {
        delv U= {m}
        pend = pend \ {m}
        Unif_real_deliver(m);
    }

```

Si j'ai reçu un ack de tous les autres processus corrects, je peux délivrer le message. J'ai pu recevoir des ACK de processus fautifs, mais là il faut recevoir des ACK de tous les processus corrects.

(fin de la séance du 06-09-2020)
-- quelques ressources en ligne, de l'année 2017 : http://master.informatique.sorbonne-universite.fr/2017/ARA --





# Cours 2 - 2020-10-20

Par Jonathan Lejuene

Pour le TP, il serait bien de définir un message par couche protocolaire.

Voir les photos de cours que j'ai prises.(dans le dossier externe au git : `ARA c2 2020-10-20 Jonathan`)




# Cours 3 mémoire partagée - 2020-10-27

Ce cours est aussi appelé "cours 2" comme le cours de Jonathan compte pour la 2ème partie du programme, après l'ER1.

Durant ce cours, on va alterner entre cours et TD. TD Mémoire partagée, ARA Octobre 2020. Cours "Shared memory and Consistency Models".

Manifestement, je n'ai pas pris de notes de cours manuscrites. Tout est sur le poly numérique, donc.

**Notes prises depuis le poly, pour m'aider à comprendre**

**Registre Safe** - *p.19*
- `read` et `write` ne se chevauchent pas => `read` retourne la dernière valeur écrite
- `read` et `write` se chevauchent => `read` retourne une valeur arbitraire autorisée par le registre (pas forcément une valeur déjà écrite, totalement non-déterministe)

**Registre Regular**
- Est un registre Safe
- read et write concurrents : retourne soit la dernière valeur écrite par une écriture non concurrente, soit une valeur écrite par une écriture concurrente. (i.e. soit la dernière valeur écrite d'un bloc fini, soit une valeur qui est en train d'être écrite)

**Registre Atomique**
- Est un registre Regular
- est linéarisable
- i.e. les écritures et lectures concurrentes sont linéarisables et on peut en tirer une exécution totalement ordonnée (ordre total => un peut tout classer, pas d'ex-æquo)


**p.24-26** : Comment construire un MRSW à partir de plusieurs registres SRSW. Tous ces registres sont supposés Regular. Les registres S ne sont accessibles que par un seul processus, il est donc nécessaire d'avoir autant de registres que de processus pour permettre un accès concurrent en lecture. Un registre SRSW Ri est associé à un unique processus Pi. On suppose que le processus P0 est le seul à écrire, il va donc répliquer les écriture et écrire dans tous les registres à la fois.

Mais cela pose un problème de linéarisation. La valeur vaut 0. P0 met longtemps à écrire. P1 lit 1 entièrement, puis survient une lecture de P2 qui lit 0. On suppose que les lectures de P1 et P2 se suivent sans être concurrentes. Cette exécution n'est donc pas linéarisable, et ça nous embête. (p.26)

Solution : Pi doit soit choisir la valeur contenue dans Ri et une valeur déjà lue par un processus. On initialise un tableau de registres SRSW de taille `N*N` (N le nombre de processus et donc de registres) : `LastRead_val[N,N]`. La case [i,j] représente la connaissance qu'à Pj de la dernière valeur lue par Pi. Avant de retourner une valeur, un lecteur Pi écrit dans sa case `LastRead_val[i, k]` avec k ∈ [1, N]. *(je pense que ça commence à 1 et pas à 0)* Globalement, on se débrouille pour que la dernière valeur lue par chaque processus soit connue de tous les autres processus. Et comme une lecture ne se termine que lorsque tous les registres ont été mis à jour, on peut toujours linéariser.

**Sequential Consistency - Cohérence séquentielle** - *p.47*
- Assimilable à une exécution séquentielle
- i.e. possible de projeter toutes les opérations de tous les processus sur une même ligne
- Tout enchevêtrement est possible, mais tous les processus voient le même.
- Même si deux opérations ne sont pas concurrentes, elles peuvent être interchangées : si temporellement, o1 vient avant o2, o1 peut venir après o2 dans l'exécution séquentielle (à partir du moment où elles sont sur deux processus distincts, on ne peut pas réordonner des opérations sur un même processus).
- -> Globalement, ça veut dire qu'on s'arrange pour que ça passe bien et que tout soit cohérent (entre valeurs lues et valeurs écrites).

**Causal Consistency - Cohérence Causale** - *p.50*
- On définit la relation de causalité globale comme suit :
- En local : l'ordre causal sur un même processus
- Entre processus : une opération `write` précède causalement un `read` lorsque la valeur du `read` retourne la valeur écrite par le `write`. i.e. il faut que tous les read respectent l'ordre causal des write.
- La causalité globale est issue de ces deux règles. Ces règles sont transitives (comme l'ordre causal vu l'année dernière en AR).

**PRAM Consistency - Cohérence PRAM** - *p.54*
- Seule la causalité locale doit être respectée :
- Sur un même processus, si w1 -> w2, alors ces deux écritures doivent rester dans cet ordre, pour tous les processus.
- Sur deux processus distincts, seule la causalité locale doit être respectée.


## TD associé (Cours 3 mémoire partagée) - 2020-10-27

### Exercice 1

#### 1.1.

La variable timestamp[i] ne peut pas être bornée. On peut avoir, sur P1 : timestamp[1] = 1 -> SC -> timestamp[1] = 0. et sur P2 timestamp[2] = 2 etc.


#### 1.2.

1. On est certain que i a choisi un ticket. Pas de concurrence entre i et k pour le ticket. k va ensuite choisir le max (peut-être i, peut-être quelque chose, mais en tout cas la valeur choisie sera plus importante que i).

2. Non, i et k peuvent avoir la même valeur. Je suppose que ça peut se produire lorsque les deux processus en même temps en recherche de ticket mais que i est plus rapide que k à s'exécuter. (parce que l'attribution de ticket n'est pas une opération atomique protégée, plusieurs processus peuvent y avoir accès en concurrence). Par contre, on ne peut pas avoir `timestamp[k] < timestamp[i]` comme il n'y a que deux processus qui choisissent leur ticket en même temps.

Notes modifiées :
```
function max {
    // i = identifiant du processus courant 
    int k = i;
    for j = 1 to n do {
        if (timestamp[k] < timestamp[j]) // nouveau max
            k = j;
        return (timestamp[k]);
    }
}
```


#### 1.3

P1 et P2 vont sortie de la fonction avec la même valeur : 3 (dans l'exemple au tableau, pas grave si j'ai pas la réf). Le problème est qu'on sauvegarde l'indice et non la valeur.


#### 1.4

En fait cette fonction ne marche pas. Dans ce cas là, on doit trouver un scénario montrant que deux processus vont entrer simultanément en SC.
On a besoin de 3 processus : P1, P2 et P3. (id = 1, 2 et 3) On suppose que P2 et P3 exécutent la fonction et sortent avec la même valeur (on a vu à la question précédente que c'est possible.)

- P2 et P3 exécutent la fonction max et sortent avec `timestamp[2] = timestamp[3] = 0`.
- P2 fait `timestamp[2] = (1 + max) = 1` et entre en SC (phase 3 donc). On a donc, pour le vecteur de timestamp : `010`
- P1 exécute phase 1 et perd la main après la ligne 4 (k=j), donc avant d'accéder à la valeur `timestamp[k]` avec `k = 2` (là, à cet instant, `timestamp[k = 2] = 1`).
- P2 sort de la SC -> timestamp[2] = 0
- P3 rentre en SC.
- P1 fait : `timestamp[1] = (1 + max) = 1`. On a donc le vecteur `101`
- Comme `∀ x ≤ N, x ≠ 1 : id(P1) < id(Px)`, P1 rentre en SC, alors que P3 y est déjà.

Le problème est qu'on sauvegarde l'indice. *Je pense pas que : le problème est surtout qu'on attribut un ticket d'une manière non atomique ?*

*(photos 2020-10-27 le matin -> okay, bien recopié)*


#### 1.5

i.e. proposer un autre code pour la fonction max.

On peut garder dans une variable locale la valeur. Comme ça, même si la valeur est changée, on a sauvegardé la précédente.

```
function max {
    MAX = 0;
    for j = 1 to N {
        temps = timestamp[j];
        if (MAX < temps)
            MAX = temps;
        return (MAX);
    }
}
```

On reprend le cours pour pouvoir faire la question 1.6. (on recommence de la page 6);
p.24 : MRSW : multiple readers, single writer.
On reprend désormais le TD (le cours est fini, 62 dispos présentées)


#### 1.6

Ici, on applique la fonction f au registre, on affecte la valeur au registre et on renvoie la valeur précédente, et tout ça d'une manière atomique, sans être interrompu. Il faut alors coder la fonction entrer SC et sortir SC.

On a trois registres :
- `R/W r`
- `shared read-modify-write ticket = 0`
- `shared read-modify valid = 0` : le prochain à rentrer

Il va donc falloir implémenter d'une manière atomique le valid et le ticket.

```
int(register r) {
    r = (r + 1) % n;
}

EntrySC() {
    temp = read-and-modify(ticket, inc);
    while (temp != valid); // on attend
}

ExitSC() {
    read-and-modify(valid, inc);
}
```

### Exercice 2


#### 2.1

On peut considérer la séquence suivante : P2 w(x, 1) ; P1 r(x)=1 ; P1 w(y, 2) ; P3 w(y, 4) ; P3 read(x)=1 ; P1 read(y=4) ; P2 write(x, 3). C'est donc une exécution séquentielle.
*(redondance via une photo du tableau)*


#### 2.2

On a :
```
write(x, 1) -> write(x, 3)
write(x, 1) -> write(y, 2)
write(x, 3) -> write(y, 4)
```

Et par transitivité : `write(x, 1) -> write(y, 4)`

*ok : voir photo, ça me fatugue trop de recopier du tableau.*



-> Reprendre d'ici

#### 2.3

On a une dépendance causale, car tous les read respectent l'ordre causal des write (et on a aussi causal -> PRAM). Mais ce n'est pas séquentiel car on ne peut pas réussir à extraire une séquence cohérente de cette exécution : pas de séquence valable.
Par exemple : w(y,2) w(y,4) mais read(?) : pas possible pour P3 de lire y=2.
Ou alors w(y,4) w(y,2) read(?) : pas possible pour P2 de lire y=4. Une règle est qu'on ne peut pas intervertir deux évènements sur un même processus, mais on peut par exemple intervertir deux read dans deux processus distincts.


#### 2.4

```
int x; // variable locale

operation(op, val) {
    if (op == write) {
        total_order_broadast(op, id);
    } else {
        return x;
    }
}

upon deviver msg<write, val, id> {
    x = val;
    if (id = idi) {  // (idi = id de i)
        return OK;
    }
}
```



### Exercice 3

### 3.1

boo MRSW registre R' = 0;

```
local variable  previous = 0;

boo Write(R, val) {
    if (previous != val) {
        R' = val;
        previous = R';
        // Si on a déjà la même valeur, je n'écris pas.
    }
    return OK;
}

boo Read(R) {
    val = R';
    return (val);
}
```

### 3.2

Non, pas possible, car il n'y a plus seulement 2 valeurs.





# Cours 4 - Consensus et détection de fautes - Pierre Sens - 

Là, par rapport à AR, on traite les fautes. On s'intéresse ici au consensus. C'est un problème d'accord.


**Canaux**

- **Fiable (reliable)** -> si p execute send (m) vers q et q est correct , alors q recevra m
- **Quasi fiable (quasi reliable)** -> si p execute send (m) vers q et p et q sont corrects, alors q recevra m
- **Equitable (fair lossy)** -> si un processus correct envoie un message m à q une infinité de fois, alors q recevra m

p.14 : bi-source, ça veut dire "dans les deux sens", envoyer comme recevoir.

En anglais :
- Justesse = Accuracy
- Complétude = Completness

-> reprendre à partir de la page 16 - 45min00 de la vidéo.

**Complétude (completeness)** - *p.16*
- **Forte** : *Il existe un instant à partir duquel tout processus défaillant* est
suspecté par **tous** les processus corrects
- **Faible** : *Il existe un instant à partir duquel tout processus défaillant* est
suspecté par **un** processus correct

**Justesse (accuracy)** - *p.16*
- **Forte** : aucun processus correct n’est suspecté
- **Faible** : il existe au moins un processus correct qui n’est jamais suspecté
- **Finalement forte** : *forte au bout d'un moment* -> il existe un instant à partir duquel tout processus
correct n’est plus suspecté par aucun processus correct
- **Finalement faible** : *faible au bout d'un moment* -> il existe un instant à partir duquel au moins un processus correct n’est suspecté par aucun processus correct

**Classes de détecteurs de fautes** - *p.17*
- P (Perfect) -> Complétude forte + justesse forte
- S (Strong) -> Complétude forte + justesse faible
- Q -> Complétude faible + justesse forte
- W -> Complétude faible + justesse faible
- Diamond signifie "au bout d'un moment"

On ne va s'intéresser qu'aux détecteurs P et S, car les complétudes faibles et fortes sont équivalentes (p.18)

p.21 : entourés en rouge : les quorums, sigma s'appelle le détecteur quorum.


# TD Détecteur de fautes – élection de leader

### Q1

Omega ocmme vu en cours.

**L'algo, tel que je le comprends :**
- Init : je pense que le leader est le processus 1. Pour tous les processus avant moi (i.e. qui ont un id inférieur au mien), je mets le délai d'attente de chaque processus à la vaeur par défaut.
- Tâche 1 (répéter périodiquement) : si je pense être le leader, j'envoie que je suis le leader à tous les processus qui me suivent.
- Tâche 2 (si je pense que le leader est avant moi) et (si je n'ai pas reçu de message de celui que je pense être le leader depuis trop longtemps) : je passe au processus suivant que je considère comme être le leader.
- Tâche 3 (lorsque je reçois "je suis le leader" depuis un processus venant avant le processus que je pense être le leader) : je le considère comme étant le leader, et j'incrémente son timeout (parce que je me suis donc trompé)

### Q2

- Task 4 : retourner le processus trusted(i), il sera le même pour tous les processus corrects, au bout d'un moment.

### Q3

Si aucun processus ne tombe en panne, le processus leader sera le processus 1. Les temps max de transmission seront connus et 1 restera pour toujours le leader.

### Q4

Oui, sans problème. P1 met du temps à envoyer un message à P2 et P3 mais pas à P4. P4 pense que le leader est 1, mais P2 qui se pense leader envoie "je suis le leader" à P3 et P4. P4 l'ignore (j(=2) > trusted(j)(=1)) mais P3 pense que c'est P2 le leader. A ce stade, il y a donc bien 2 leaders (puis P2 et P3 reçoivent le heartbeat de P1, incrémentent leur timeout pour P1 et considèrent que P1 est le leader).

### Q5

Comme dit dans ma Q4, c'est l'incrément du timeout d'un processus lors de la détection d'une erreur qui va permettre, au final, de borner le délai de réception d'un message et donc de ne plus faire d'erreur.

### Q6

Perso j'essayerais bien par l'absurde : On suppose que  `∀ t : ∃ t' > t, ∃ pi ∈ correct, leader(t') != pleader`

i.e. à n'importe quel moment, il existe un instant dans le futur où tous les processus ne sont pas d'accord entre eux.

--

Au bout d'un moment, il y a des bornes sur le délai de transmission (lorsque le GST est atteint, le global stabilisation time). (i.e. delta suite croissante majorée, à valeurs entières, donc au bout d'un moment, delta se stabilise à la valeur de sa borne supérieure).

Donc au bout d'un moment, chaque processus correct ne fait plus aucune faute sur aucun autre processus correct. Donc le leader reste toujours le même et ne change plus pour chaque processus. La Task 2 de l'algorithme n'est donc plus jamais vérifiée (plus aucune de fautes et toujours le même leader).

Soient Px et Py deux processus qui n'ont pas le même leader. Comme Task2 n'est jamais réalisée, cela veut dire qu'un processus L1 envoie périodiquement à P1 et qu'un autre processus L2 envoie à P2. On suppose l'id de L1 inférieur à l'id de L2. Il y a donc deux leaders, qui envoient leurs messages à tous les processus. Dans la Task3, le processus P2 reçoit donc les messages de L1 et comme id(L1) < trusted(=L2) il devrait changer de leader. On a donc une contradiction car les canaux sont fiables et qu'aucun message n'est perdu. Donc la formule est vraie.

On a aussi que tous les processus d'id inférieur au leader sont fautifs (s'ils ne l'étaient pas, ils seraient leader, comme le prouve la démonstration ci-dessus).


### Q6

S :
- complétude forte : tout processus fautif est au bout d'un moment suspecté par tous les processus corrects
- justesse faible : au bout d'un moment, il y a un processus qui n'est jamais suspecté

<>S = au bout d'un moment S (et pour toujours), on a bien tous les processus fautifs sont suspectés par tous les processus corrects, et il y a un processus correct non suspecté : le leader. Donc oui, on a un détecteur de défaillance <>S.


### Q7

L'algo précédent est équivalent à un <>S. On a la complétude forte (tout processus fautif est suspecté au bout d'un moment par tous les processus corrects) et la justesse faible (il y a un processus correct qui n'est, au bout d'un moment, plus jamais suspecté, le leader). Ce qu'on veut ici pour implémenter un détecteur <>P, c'est implémenter la justesse forte : aucun processus correct n'est suspecté, au bout d'un moment.

Du coup, a mon avis, c'est la même chose que l'autre algo avec en plus la détection des processus fautifs par le leader. (et le broadcast par le leader des processus fautifs aux autre). L'autre algo donne tous les processus corrects 

ça donnerait :

// le leader est le premier processus correct

```C++
Every process pi, i = 1, ..., n executes :
trustedi <- 1     // je pense que le leader est 1
suspectedi <- {}  // aucun processus suspecté

∀ j ∈ {1, ..., n} : Δij <- default timeout

cobegin
// Task_1 : Message envoyé par le leader
|| Task_1 : repeat periodically

    if trustedi == i then  
        send(I-AM-THE-LEADER, suspectedi) tp p(i+1), ..., p(n)
    else
        send(I-AM-ALIVE to ptrustedi)

// Task_2 : détection de la panne d'un processus avant moi 
|| Task_2 : when (trustedi < i) and (did not receive (I-AM-THE-LEADER, suspected_trustedi) from ptrustedi during the last Δi,trustedi time units)

    trustedi <- trustedi + 1
    if (trustedi = i)
        suspectedi = {p1, ..., pi-1}

// Détection d'une erreur : message arrivé trop tard
|| Task_3 : when (received (I-AM-THE-LEADER, suspected_trustedi) from pj) and (pj <= trustedi)

    suspectedi = suspected_trustedi

    if (j < trustedi)
        trustedi <- j
        Δij <- Δij + 1


// Détection des processus en panne par le leader
|| Task_4 : when (trustedi == i) and (did not receive I-AM-ALIVE from pj, j ∈ {1, ..., n}, j!=i  during the last Δi,j time units) and (pj ∉ suspectedi) and (j > i)
    suspectedi <- suspectedi + {pj}

// Détection d'une erreur : processus pas en panne en fait
|| Task_5 : when (trustedi == i) and (received I-AM-ALIVE from pj) and (pj ∈ suspectedi)
    suspectedi <- suspectedi - {pj}
    Δij <- Δij + 1

```

Les tâches 1, 2 et 3 sont identiques qu'avec l'algo précédent (avec en plus diffusion et mise à jour des suspects), les tâches 4 et 5 servent au leader à détecter les processus réellement en panne.


# TD Consensus M2 ARA - Coordinateur tournant (Chandra Toueg 1996)

### Q1

**Diffusion fiable** : Soit un message m envoyé par un processus.
- Si l'émetteur est correct, tous les processus correctes finissent par délivrer le message m.
- Si l'émetteur est fautif, soit tous les processus corrects finissent par délivrer m, soit aucun processus correct ne délivre m.

**Consensus**
- Validité : La valeur choisie est une valeur proposée
- Accord : Tous les processus corrects décident la même valeur
- Terminaison : Tous les processus corrects finissent par décider
- Intégrité : Tout processus correct décide au plus une fois

### Q2

Les canaux sont fiables, donc un message sera bien délivré dès lors qu'il est envoyé. Le risque ici, est d'avoir un processus qui n'envoie qu'à une partie des processus puis crash. Donc il faut rediffuser tout message qui est reçu.

On implémente juste une diffusion fiable, pas une diffusion fiable uniforme.

```
Init :
// Liste des messages reçus sous la forme : (provenance, contenu)
messages = {}
correct = {p1, ..., pn}
i = mon identifiant

receive : upon reception of (sender, message)
    if not ((sender, content) in messages) and (sender != pi)
        for all pj in correct and pj != pi
            send(sender, message) to pj
        add (sender, content) to messages
        real_deliver(sender, message)

broadcast(message)
    for all pj in correct
        send(pi, message)
```

Ou, fait d'une manière plus simple :
```
receive : upon reception of message
    if (message received first time) and (sender != this)
        send message to all
    real_deliver(message)

broadcast(message)
    send message to all
```

### Q3

Initialisation :
- estimatep : la valeur que p pense être la bonne
- statep : l'état (décidé ou non décidé) de p
- rp : numéro de ronde dans laquelle est le processus p
- tsp : (timestamp) numéro de ronde où j'ai reçu estimate d'un coordinateur (numéro de la ronde où j'ai mis à jour ma valeur)

Répéter toujours :
- incrément du numéro de ronde
- le coordinateur cp est associé au numéro de ronde (module le nb de processus)

Phase 1 :
- p envoie son numéro de ronde, sa valeur et (je sais pas) au coordinateur

Phase 2 : (seulement si je suis le coordinateur)
- attente d'une majorité de réponses
- on prend la valeur d'estimate associée à la plus grande valeur "ts" reçue
- broadcast de (sender (=this), round, estimate) à tous les processus y compris soi-même

Phase 3 :
- attendre de recevoir la valeur du coordinateur de cette round ou que je suspecte le coordinateur
- si message reçu : je mets à jour mon estimation, ts = n°round, evoi d'un acquittement au coordinateur
- si message pas reçu :j'envoie un nack au coordinateur

Phase 4 :
- si je suis le coordinateur :
- - j'attends une majorité de ack ou de nack des autres processus
- - si j'ai reçu une majorité de ack : je broadcast ma valeur estimate et round

### Q3 et Q4

Faits sur papier, mais questions :
- t = 2 parce qu'on est à la round 2 et pas à la round 1 comme pour la question 3
- dans la phase 2, on peut choisir n'importe quelle valeur à partir du moment où on prend une des valeurs associées au "ts" le plus grand, donc pour la q3 : v1, v2 ou v3 et pour la q4 : v1 ou v3 ?

### Q5

Il n'est pas possible que deux coordinateurs diffusent une décision, comme 

Si une majorité suspecte le coordinateur : le coordinateur passe son tour (majorité de nack) et du coup tous les processus corrects vont à la ronde suivante.

Il peut y avoir plusieurs broadcasts en même temps, mais la valeur retenue restera la même.

3h17

### Q6

Un accord uniforme est défini comme suit : si un processus (correct ou fautif) délivre un message, c'est qu'il y a eu une majorité de processus qui ont cette valeur en `estimate`. Si le broadcast n'a pas été complètement fait : pas grave, le prochain coordinateur s'occupera de délivrer le même message (avec un numéro de ronde incrémenté, mais c'est transparent au regard de la valeur renvoyée). Cet algo implémente bien un accord uniforme.

### Q7

Le message `nack` permet de débloquer le coordinateur s'il est bloqué en attente de `(n+1)/2` réponses des autres processus. (s'il a été faussement suspecté par une majorité de processus)

### Q8

a) La terminaison est assurée par le fait que la diffusion fiable assure qu'un message envoyé sera, au bout d'un moment, ben reçu. Si l'émetteur tombe en panne avant d'avoir transmis à tous les autres processus, la diffusion fiable assure que tous ou aucun des processus corrects recevra le message : si aucun reçoit, on tombe dans l'hypothèse de pas plus de (n-1)/2 fautes (et l'algo continue avec la valeur du processus fautif sauvegardée dans au moins la moitié des autres processus corrects, d'où l'accord uniforme), si tout les corrects le reçoivent, la terminaison est assurée.

b) (complétude forte : à terme, tout processus fautif est suspecté par tous les autres processus corrects).
- Soit on a un processus qui n'est pas coordinateur : il peut être bloqué en phase 3, dans l'attente de celui qu'il pense être le coordinateur. Mais comme on a un <>S, au bout d'un moment si le coordinateur attendu est en panne il sera suspecté et on se débloque.
- Soit le processus est un coordinateur : il peut être en attente en phase 2, mais on a supposé qu'il n'y avait pas plus de (n-1)/2 fautes, tous les processus corrects exécutent la phase 1 et les canaux sont fiables (pas de message perdu), donc la phase 2 ne peut pas être bloquante. La phase 3 est commune avec les processus n'étant pas coordinateurs. La phase 4 n'est pas non plus bloquante : tous les processus corrects sont tenus de répondre au coordinateur, et comme par hypothèse, il n'y a pas plus de (n-1)/2 processus fautifs, le coordinateur est forcément débloqué.
- Enfin, pour tout processus, la terminaison est assurée (montré en a)) donc le `while state = undecided` s'arrête un jour car `state` passe à `decided` un jour.

c) (justesse faible : à partir d'un moment, il existe un processus correct qui n'est plus jamais suspecté) Pour que l'algorithme termine et que les processus décident, il faut un R-Broadcast (un seul suffit car les canaux sont fiables et que les valeurs `estimate` des R-Broadcasts sont toujours identiques (seul l'identité du coordiateur et le numéro de la ronde peuvent changer)). Cela arrive forcément : soit c un processus qui n'est plus jamais suspecté (à partir d'un instant donné). S'il y a déjà eu un R-Broadcast, on a gagné. S'il n'y en a pas encore eu, il y a donc au moins (par hypothèse) `(n-1)/2` processus corrects qui vont répondre à c et aucun ne le suspecte. Il va donc en phase 4 recevoir une majorité (absolue) de `ack` et donc il va émettre un R-Broadcast et l'algo va bien finir.

D'où la terminaison.

### Q9

Le prof a dit qu'osef de la question 9.


# Cours Paxos

reprendre de 1h30

**p.25** Pour la vivacité, on est obligé d'attendre `n-f` réponses (les byzantins peuvent juste ne pas répondre par exemple et simuler une panne franche). Et dans ces `n-f`, on veut soit une majorité de bonne réponses, donc, si tous les byzantins sont dans les répondants, il nous faut `(f byzantins) + (f + 1 non byzantins)`. Donc on veut `nb_réponses >= 2f + 1`. Et `nb_réponses = n - f` pour la vivacité. Donc on retrouve le résultat du cours :  `n - f >= 2f + 1` => `n >= 3f + 1` si on veut une majorité de processus non byzantins à chaque fois. (équivaut à `f < n/3`)


**p.29** Proof contient la preuve des valeurs précédemment acceptées (la preuve que "AcceptNum + AcceptVal" est correct et pas inventé), i.e. les ack signées des autres processus qui ont envoyé les messages menant au couple "AcceptNum + AcceptVal", i.e. à cette décision.

la signature par le processus de son message, prouvant que c'est bien lui qui l'a envoyé et qu'il n'a pas été modifié sur le réseau.

**p.30** format : (message de type ack, numéro du scrutin actuel, numéro du scrutin ayant conduit à ma valeur val, val : valeur stockée (valeurs acceptées par chacun), proof : preuve qui permet de valides ces valeurs là).

S contient l'ensemble des ack qui ont conduit à l'envoi de ce message, donc la preuve que le message est bien valide.


### Q10

Non, la valeur proposée doit être la même, car pour une ronde donnée, pour qu'un processus propose une valeur il faut qu'il ait reçu une majorité (> n/2) de valeurs identiques, ce qui n'est possible qu'une seule fois (comme on a n processus au total).

### Q11

Je pense qu'il faut juste remonter l'algorithme.

Un processus p décide v à la ronde k => il a reçu au moins (f + 1) valeurs v de type Propose => il y a au mois (f + 1) processus qui ont envoyé (P, k, v) => (f + 1) processus ont reçu (R, k, v) d'une majorité de processus (>= n/2 + 1) => donc à cette ronde, il y a au moins n/2 + 1 processus qui ont la valeur v stockée. Le processus q qui apparaît à la ronde suivante, exécute l'algo, et reçoit donc une majorité de valeurs identiques.

Réponse meilleure :

- p a décidé à la ronde k
- => il a reçu un message d'au moins (f + 1) processus de type (P, k, v) avec le même v
- => (f + 1) processus ont reçu une majorité (> n/2) de messages de type (R, k, v) avec v identique
- ça veut dire qu'il y a une majorité de processus qui ont la même valeur à la ronde k ((n/2 + 1) processus au minimum). Et à partir du moment où une majorité de processus ont la même valeur, cette valeur ne peut plus changer : pour que la valeur change, il faudrait qu'il y a à une ronde suivante une majorité de valeurs différentes proposées, ce qui est impossible : le seul moyen de changer sa valeur est de le faire l.16, mais cette valeur doit être une des valeurs proposées. Or il n'y a qu'une seule valeur proposée, identique, donc et les processus qui ont une valeur différente prennent cette valeur. Donc tout processus qui arrive à a phase k+1 recevra une majorité de valeurs identiques, et du coup décidera cette valeur.

### Q12

Une valeur aléatoire est choisie lorsqu'un processus n'a pas reçu une majorité de valeurs identiques de (n - f) processus, donc quand il n'y a pas plus de (n/2 + f) valeurs identiques pour les processus, à une ronde k donnée.

Pour que l'algo ne se termine pas, il faudrait qu'on ait un processus p sur lequel :
- jamais (f + 1) messages de type (P, k, v) reçus avec le même v
- donc jamais 
...


















Okay chouette chouette, j'espère que tout va bien de ton côté en tout cas ! :)

## 2020-11-17 Cours 6


GST : global stabilization time.

<> : Diamond, signifie "au bout d'un moment", "finalement".



## Examen Réparti 1

L'examen réparti 1 aura lieu le 15 décembre, à la place d'un cours, et à la place du créneau du 1er décembre, il y aura un cours.


# Projet 2020-2021

Mail Célia (encadrante) : celia.mahamdi@lip6.fr

Il nous conseille de nous mettre en binôme pour se répartir le travail.
20% de la note finale en ARA.

On est libre de faire le rapport comme on veut. Du moment que c'est synthétique et bien fait. On est totalement libre pour la partie 3. Si on prend quelque chose sur Internet, il faut absolument citer ses sources exactes.

Faut bien défricher le sujet avant de se lancer sur le code, bien réfléchir à ce qu'il y a à coder.