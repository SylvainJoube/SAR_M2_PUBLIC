# ARA

Pour l'instant, nous n'avons pas de cours numérique, c'est juste un polycopié. En plus de ces notes de cours, voir mes notes manuscrites.

Je n'ai pas encore le cours de 2020 (pas encore mis en ligne sur Moodle ni donné par mail) mais j'ai mis le cours de 2017 : `Cours 1/Protocole_diffussion_2017.pdf`.

# Cours du 06-09-2020

Aucune note manuscrite pour ce cours 1. (seulement pour le TD 1)

On aura un projet avec Jonathan sous PeerSim. Et rien d'étonnant, on aura aussi des TME.

Dans ces cours, par rapport à AR du S2, on va traiter le cas où il y a des fautes.

(p.9)
Le concensus en une phrase : tous les processus doivent se mettre d'accord sur une valeur commune.
**Processus correct** : processus qui ne tombe jamais en panne lors de l'exécution.
**validité** : (pour les Vi) : la valeur décidée est une des valeurs proposées initialement par un des processus.

**p.16**
reveive(m) est différent de deliver(m). Ça permettrait d'éviter certaines incohérences comme vu à la diapo p.13.

**p.24**
La garantie Best-effort est assez faible.

**p.27**
Validité = best effort

**p.28-29** : on est dans un système asynchrone. Chaque processus est supposé connaître tous les autres processus, tout le temps. (donc pas de latence du à un groupe dynamique etc. on la fait simple pour le moment...)

**p.40** : C'est pas représenté ici, mais il y a bien délivrance à l'application de chaque processus du message x = 1 avant le message x = 2.

**p.43** : ce n'est pas un broadcast FIFO.

**p.45** : seq# signifie "numéro de séquence".
En gros, là, il s'agit de délivrer le message dans l'ordre des numéros de séquence.

p.46 : le processus a donc un compteur de messages qu'il attend de la part du processus 2, et du coup quand il reçoit un message à l'identifiant trop élevé, il le met en attente et il attend de recevoir le message portant le numéro attendu. Et comme ça, tous les messages sont reçus dans l'ordre. Je suppose que ça nécessite juste une liste de mesages en attente, et deux compteurs sur l'émetteur (le dernier numéro que j'ai envoyé à p) et un sur p : le next attendu.

**(p.52)**
Je crois *(à vérifier)* que chaque message est délivré lorsqu'il y a une différence de 1 entre l'horloge vectorielle locale et l'horloge vectorielle associée au message reçu.C'est à dire que le message est mis en attente jusqu'à ce que j'ai reçu tous les messages qui le précèdent causalement : le (1) signifie avoir reçu tous les broadcasts de l'émetteur du message (que ce message porte l'estampille que j'ai en local (pour le processus émetteur) + 1) et (2) signifie que je n'ai pas d'autres messages précédemment causalement cet envoi, et qui doivent dont arriver avant lui.  C'est d'ailleurs ce qui est expliqué sur le poly, en fait.

**p.63**
**\ token.liste do** : et pas dans la liste du token

p.66 : on suppose le processus s1 a le token.

p.71 : N = temporaire, D = définitif
A chaque fois, on prend le numéro le plus grand, pour chaque date de message reçue. Lorsqu'un message est noté définitif, et qu'il est au début de la file, le processus sait qu'il peut délivrer le message à l'application. Puis, (p.72) tous les messages sont délivrés à l'application à l'étape 4.


## TD associé (du 06-09-2020)

Une partie de mes notes sont manuscrites, rangées dans ma pochette `ARA`.

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
pend = delv = void
ack'm[] = void

Unif_real_broadcast(m)
{
    // sauvegarde du message à chaque fois
    pend U= {m}
    - estampiller m avec send(m) et seq#(m)
    - envoyer m à tous les processus (appartenant à) correct ;
}

// Le ⊆ signifie contenu dans (C souligné)

upon recv <m> from q :
{
    ack'm[m] U= {q}

    if (m ∉ pend and m ∉ delv (delivered))
        pend U= {m}
        envoyer m à tous les processus corrects sauf p. // c'est comme si c'était un ACK : on rediffuse le message.

    if (correct ⊆ ack'm[m]) {
        deliv U= {m}
        pend = pend \ {m}
        Unif_real_deliver(m);
    }
}

upon <crash, q>
    correct = correct \ {q}
    for all m ∈ pend
    if ( (m ∉ deliv) && correct ⊆ ack'm[m]) {
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




# Cours 3 - 2020-10-27

Durant ce cours, on va alterner entre cours et TD. TD Mémoire partagée, ARA Octobre 2020. Cours "Shared memory and Consistency Models".

### TD 1.1.

La variable timestamp[i] ne peut pas être bornée. On peu avoir, sur P1 : timestamp[1] = 1 -> SC -> timestamp[1] = 0. et sur P2 timestamp[2] = 2 etc.

### TD 1.2.

1. On est certain que i a choisi un ticket. Pas de concurrence entre i et k pour le ticket. k v aensuite choisir le max (peut-être i, peut-être quelque chose, mais n tout cas la valeur choisie sera plus importante que i).

2. Non, i et k peuvent avoir la même valeur.

max
int k = i;
for j = 1 to n do {
    if (timestamp[k] < timestamp[i])
        j = k;
    return (timestamp[k]);
}

### 1.3

P1 et P2 vont sortie de la fonction avec la même valeur : 3. La problème est qu'on sauvegarde l'indice et non la valeur.

### 1.4

En fait cette fonction ne marche pas. Das ce cas là, on doit trouver un scénario montrant que deux processus vont entrer simultanément en SC.
On a beosin de 3 processus : P1, P2 et P3. (id = 1, 2 et 3) On suppose que P2 et P3 exécutent la fonction et sortent avec la même valeur (on a vu à la question précédente que c'est possible.)

- P2 et P3 exécutent la fonction max et sortent avec timestamp[2] = timestamp[3] = 0.
- P2 fait timestamp[2]=1+max et entre en SC (phase 3 donc). On a donc, pour les timestamp : 001000 (pour 0123456).
- P1 exécute phase 1 et perd la main après la ligne 5 (k=j).
- P2 sort de la SC -> timestamp[2] = 0
- P3 rentre en SC.
- P1 fait : P1 = 1+max() -> timestamp[1] = 1 + max = 1. On a donc le vecteur 
- Comme id(P1) < id(Px) pour tout x <= N, x != 1.
- P1 rentre en SC, alors que P3 y est déjà.

Le problème est quon sauvegarde l'indice.

(voir photos 2020-10-27 le matin)

### 1.5

i.e. proposer un autre cod epour la fonction max.

On peut garder dans une variable locale la valeur. Comme ça, même si la valeur est changée, on a sauvegardé la précédente.

```java
max()
MAX = 0;
for j = 1 to N {
    temps = timestamp[j];
    if (MAX < temps)
        MAX = temps;
    return (MAX);
}
```

On reprend le cours pour povoir faire la question 1.6. (on recommence de la page 6);
p.24 : MESW : multiple readers, single writer.
On reprend désormais le TD (le cours est fini, 62 dispos présentées)

### 1.6

Ici, on applique la fonction f au registre, on affecte la valeur au egistre et on renvoie la valeur précédente, et tout ça d'une manière atomique ,sans être interrompu. Il faut alors coder la conftion entrer SC et sortir SC.

On a un registre `R/W r;`
et `shared read-modify-write ticket = 0;`
`shared read-modify valid = 0; // le prochain à rentrer`

Il va donc faloir implémenter d'une manière atmique le alid et le ticket.

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

## Exercice 2

### 2.1

On peut considérer la séquence suivante : P2 w(x, 1) ; P1 r(x)=1 ; P1 w(y, 2) ; P3 w(y, 4) ; P3 read(x)=1 ; P1 read(y=4) ; P2 write(x, 3). C'est donc une exécution séquentielle.
(redondance via une photo du tableau)

### 2.2

On a :
write x1 -> write x3
voir photo, ça me fatugue trop de recopier du tableau.


### 2.3

On a une dépendance causale, car tous les read respectent l'ordre causal des write (et on a auasi causal -> PRAM). Mais ce n'est pas séquentiel car on ne peut pas réussir à extraire une séquence cohérente de cette exécution : pas de séquence valable.
Par exemple : w(y,2) w(y,4) mais read(?) : pas possible pour P3 de lire y=2.
Ou alors w(y,4) w(y,2) read(?) : pas possible pour P2 de lire y=4. Une règle est qu'on ne peut pas intervertir deux évènements sur un même processus, mais on peut par exemple intervertir deux read dans deux processus distincts.


### 2.4

```java
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

## Exercice 3

### 3.1

boo MRSW registre R' = 0;
```java
local variable  previous = 0;
boo Write(R, val) {
    if (previous != val) {
        R' = val;
        previous = R';
        // Si on a déjà la même valeur, je n'écris pas.
    }
    return OK;
}

bo Read(R) {
    val = R';
    return (val);
}

```

### 3.2

Non, pas possible, car il n'y a plus seulement 2 valeurs.



## 2020-11-17 Cours 6


GST : global stabilization time.

<> : Diamond, signifie "au bout d'un moment".

