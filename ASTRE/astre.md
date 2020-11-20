# Cours ASTRE

Lien vers le cours en ligne : https://pages.lip6.fr/Nathalie.Sznajder/cours2018.pdf
ou fichier local : `astre-cours2018.pdf`

*Les pages sont référencées par rapport au le pdf de 2020 (présent sur Moodle) et non dans le pdf de 2018, les numéros de page sont différents d'une année à l'autre.*

Ressources, photos prises en cours : https://drive.google.com/drive/folders/1H0s6mSeE0dLoQGDzdksHSb02ZC8qVYKX?usp=sharing

# Cours 1  2020-09-05

Diapo de cours associé : `cours2020-1.pdf`. Aussi sur Moodle.

Les méthodes formelles : preuves nécessaires (genre pour pas faire exploser une fusée toussa toussa)
- Problème des tests : impossible de tout vérifier.

`Prérequis : logique du 1er ordre (syntaxe, sémantique).`
Si c'est pas le cas : on pourra s'en sortir (cours de logique de L3, avec les pour tout, il existe...)
`Et les automates : des rappels seront faits.`

## I. Modélisation

**Système de transition** : Les états sont les petits ronds, les actions sont les flèches entre ces ronds. D'où la notion de système de transition.

**Propositions atomiques** : Très important pour le model checking : `propriétés vérifiées par les états` i.e. ce que l'état symbolise (exemple : explosion de la centrale nucléaire, 2 processus en SC en même temps, ...).

### Structure de Kripke (p.7)

Super important :

```
Définition: M = (Q,T, A, q0, AP, l) une sutructure de Kripke.
• Q : ensemble fini d’états
• A : alphabet d’actions (facultatif)
• T : relation de transitions entre états
• q0 : état initial
• AP : ensemble de propositions atomiques
• l : Q 2^AP, étiquetage des états
```

Relation de transition entre les états = les flèches entre les états.
Il peut y avoir plusieurs propositions atomiques par état.
(*Systèmes de transitions* -> go rechercher la définition et les propriétés)

Soit q un état (un rond). **Ses successeurs** sont les ronds (états) vers lesquels les flèches qui en partent pointent.
Et ça donne, en formule selon la slide (p.8 (ex p.12)) :
Soit M=(Q,T, A, q0, AP, l) une structure de Kripke.
Soit q un état (de la structure de Kripke ici décrite).
L'ensemble {q’∈ Q | il existe a ∈ A, (q,a,q’) ∈ T} est l’ensemble des successeurs de q.

Voir le temps que ça me prend de tout recopier : 18h20 là

On a `T ⊆ Q x Q`.

Je crois que q0 est aussi l'état du système au temps logique 0 (i.e. l'état initial)

**état puits** : ce sont des états qui pointent vers eux-mêmes.

### Dans l'exemple "pay" "soda" ... (p.9) :

Pay : état initial. `τ` (tau, que je note parfois juste t) est le nom d'une transition dont le nom n'a pas trop d'importance. (i.e. interne au système, mais on s'en fiche d'avoir son nom exact).

Soit un état donné (i.e. quand on est au niveau d'un rond, je rappelle que les ronds sont des états qui ont un nom et sont décrits par les propositions atomiques qui leur sont associées).

Plusieurs états peuvent avoir les mêmes propositions atomiques, et chaque état peut avoir un nombre quelconque de propositions atomiques réalisées. Exemple : {paid, drink}.

*Note, en complément : cet automate ne montre pas si le client a bien ce qu'il a demandé, juste qu'il a quelque chose.*

On a les relation suivantes (*recopié de mon cours manuscrit*) :

Q = {pay, select, soda, beer}
q0 = pay
A = {get_soda, get_beer, τ, insert_coin}
T = { (pay, insart_coin, select) , (select, τ, soda), (select, τ, beer), (beer, get_beer, pay), (soda, get_soda, pay)}
AP = {paid, drink}
l : étiquetage.
l(pay) = {} (ensemble vide)
l(soda) = {paid, drink}
l(beer) = {paid, drink}
l(select) = {paid}

### Exemple du circuit (p.10)

Voir les annotation sur mon cours.

La structure représentant un circuit est non-déterministe, et son état dépend de la valeur de x. Il y a deux états initiaux : x = 0 et x = 1.

La prochaine valeur de r est calculée : r = x ∧ r. (et logique)

Prochaine valeur de x : donnée en entrée.

Entrée boite xor = x ∧ r.

Entrée boite or : r ∧ x. (∧ = et)

### Sémaphore (p.11)

Cette diapo décrit l'activité d'un sémaphore. y est le compteur du sémaphore. Pareil que pour le circuit, l'évolution de cette structure est non déterministe.

### Structure de Kripke (p.12)

On suppose T est totale i.e. que (les relations de transitions entre états) est totale, que chaque état a un successeur. Quitte à ajouter des états puits, états dont un de leurs successeurs (ou bien le seul successeur) est eux-mêmes.

### Descriptifs de haut niveau (p.13)

Les structures de Kripke sont en général énormes, il est donc nécessaire d'utiliser un autre langage de haut niveau.

### Réseau de Petri (p.14)

*Réseau de pétri : pas utile pour ce cours, c'est juste pour la culture.*

Dans la slide "pétri / kripke" : les t sont les états, les p sont les positions de jetons. Ça s'appelle le graphe des marquages. "On tire une seule transition à la fois d'une manière asynchrone". Chaque état est un marquage, c'est à dire une description du système (c'est logique en fait). Deux événements ne peuvent pas se produire en même temps dans notre système asynchrone (probabilité nulle en asynchrone)

(p. 18) là, on fait bouger deux jetons d'une case à chaque fois, et on tente de décrire où ils sont. (on ne reverra plus des réseaux de Pétri, c'est hors programme)

### Exécutions et traces (p.15)

On regarde alors l'ensemble des exécutions d'une structure de Kripke. C'est exactement comme les exécutions des automates finis.

Dans les relatons de transition (nommé T) de la structure de Kripke, il a toujours un état pour passer d'un qi à qi+1. (i.e. c'est une suite infinie d'états successifs, comme on suppose T **T totale** à la p.12)

**l(...)** : c'est toujours la manière dont on nomme l'étiquetage. Exemple : l(r)=l(q0)l(q1)l(q2) avec r=q0q1q2 l'étiquetage des transitions. (cf. p.19)

Les structures de Kripke servent "simplement" à décrire les états les systèmes, pas à en spécifier l'implémentation.

### Arbre d’exécutions d’une structure de Kripke (p.16)

A une structure de Kripke, on associe un ensemble de traces... On peut aussi y associer un arbre. Chaque branche est un état. L'arbre est infini.

**Explosion combinatoire** : petits systèmes réunis en un seul : exploration exhaustive de la structure de Kripke pour en vérifier tous les états. Le gros obstacle de la vérification est l'explosion combinatoire (i.e. beaucoup trop d'états à vérifier). (p.18)

Composition d'automates : dispo d'exemple : exclusion mutuelle (p.19).

Solution de l'exercice en p.24 : voir poly, écriture manuscrite. (en théorie c'est bien recopié, et de toute façon c'est pas très difficile)

### Exercice (p.20)

*Décrire formellement la structure*

Q l'ensemble d'états finis :
Q = {s0, s1, s2, s3}

A l'alphabet d'actions
A = {α, β, γ, τ}

T l'ensemble des relations de transition entre états
T = { {s0, τ, s0}, {s0, α, s1}, ...} (voir sur ma feuille manuscrite)

q0 l'état initial :
q0 = {s0}

AP l'ensemble des propositions atomiques
AP = { {p1}, {p1, p2}, {p2}}

l les étiquetages des états
l(s0) = p1
l(s1) = {p1, p2}
l(s2) = {p2}
l(s3) = {}

*Donner une exécution, une trace d’exécution*

On choisit une trace arbitraire : s0 s0 s1 s2 s0 s1 s3 s3 ...
La trace de cette exécution commence par { {p1} {p1} {p1, p2} {p2} {p1} {p1, p2} ...}

*Dessiner l’arbre d’exécutions associé (3 premiers niveaux).*

Voir sur ma copie manuscrite.

## 2. Spécifications

On s'intéresse désormais à la partie 2. Spécifications.

**Connecteur temporels** : vont en plus des connecteurs logiques classiques (ou/et etc.)
**Logique du 1er ordre** : ensemble de variables, et relations entre elles : relations unaires (prédicats) etc. Les variables seraient les différents instants, chaque état visité représenterait un instant.

La logique temporelle ne qualifie pas l'écoulement du temps, elle s'attache à décrire les relations entre les états.

Il manque un requête(t) -> ...  veut dire : à chaque fois que j'ai une requête, j'obtiens (au bout d'un moment) une réponse.

Or, c'est très vite très embrouillé et difficile à comprendre.

**Modalité** *(p.26)* : symbole de logique placé avant une formule et qui en modifie la signification : exemple : may (est-ce que cette expression sera vraie un jour ? may ... be verified ...)

C'est pas un temps qui s'écoule, simplement un temps logique, lien entre les instants, peu importe le temps "réel" qui s'est écoulé entre les instants qui symbolise tous les futurs possibles.

**Logique de temps linéaire** : on regarde les traces d'exécution. La trace "perd" forcément ses choix car elle est réalisée, contrairement au temps arborescent.

### 2.2. Linear Temporal Logic (p.28)

On confronte une formule à une trace d'exécution. t = trace (trane infinie, ensemble de proposition satomiques vérifiées).  i|=phi  : est-ce que la trace (à l'instant) i vérifie la formule phi (i appartenant à la trace t).  t,i = t à la position i.
⊧ (que je note parfois |=) : "satisfait la formule"

**mot** : séquence (parfois infinie) de lettres dont chaque lettre est un ensemble
**t(i)** : ensemble des propositions atomiques vraies à un instant donné (i). Exemple sur le poly : (t la trace d'exécution) t = {p1} {p1, p2} ...
Les t(i) : t(0) = {p1}  t(1) = {p1, p2}  ...

Le ≣ du cours est une typo, il s'agit en fait du symbole d'équivalence à trois barres : ≡ (aussi nommé égalité sémantique)


t, i |= p ssi p app à t(i) :
t(0) = {p} t,0 != p ... (reprendre si le temps)

Xphi : X : next
Xp veut dire : p sera vrai à l'état suivant.
Selon le poly : `t, i ⊧ Xφ  <=>  t, i+1 ⊧ φ`

F : finally (un jour) : Fp  est vrai lorsque p est (au moins une fois) vrai plus tard dans la trace.
Selon le poly : `t, i ⊧ Fφ  <=> ∃ j ≥ i / t, j ⊧ φ`

Gp : globally : vrai jusqu'à la fin des temps.
Selon le poly : `t, i ⊧ Gφ <=> ∀ j ≥ i, t, j ⊧ φ`

U : until. `C1 until C2` signifie que tant que C2 n'est pas vrai, C1 doit l'être.

`t,i ⊧ φ1 U φ2 <=> ∃ j ≥ i / t,j ⊧ φ2 et ∀ i ≤ k < j, t,k ⊧ φ1`

### Résumé des formules vues précédemment (p.37)

Bon, pas grand chose à dire de plus, c'est un bon résumé.

### Quelques macros utiles (p.40)

`(Weak until) φ1Wφ2 ≡ Gφ1 ∨ φ1Uφ2` signifie simplement "soit φ1 est tout le temps vrai à partir de maintenant, soit φ1 est vrai, puis tout de suite après φ2 est au moins une fois vrai."

`(Release) φ1 R φ2 ≡ φ2 W (φ1∧φ2) ≡ Gφ2 ∨ φ2 U (φ1∧φ2)` :
`φ2 W (φ1∧φ2)` signifie : "soit φ2 vrai tout le temps à partir de maintenant, soit φ2 est vrai, puis tout de suite après on a au moins une fois φ1 et φ2."

`Gφ2 ∨ φ2 U (φ1∧φ2)` signifie : "soit on a tout le temps φ2 à partir de maintenant, soit (ou) on a : φ2 vrai tout le temps jusqu'à ce que (φ1∧φ2) soit vrai au moins une fois.  i.e. φ2 vrai jusqu'à ce qu'on ait à la fois φ2 et φ1 de vrai, au moins une fois."

*i.e. φ2 est désactivé par φ1 ?* <- à re-comprendre si j'ai le temps, je suis trop fatigué là

### LTL exmeples (p.41)

GF : a chaque instant, dans le futur on en trouvera un = infiniment souvent. Si mon processus est infiniment enabled => il sera infiniment scheduled.

FG : dans le futur, un processus sera continuellement enabled (à partir d'un instant, la propriété est toujours vraie). (appelé équité faible car on sert moins souvent le processus alors qu'il est enabled continuellement) (avec scheduled désactive enabled)

L'équité forte => l'équité faible : FG enabled => GF enabled => GF enabled.

## TD associé

### Exercice 1

ALors la prof a dit que la limitation principale de l'exercice est que les phrases en français peuvent être mal interprétées.

Alors j'ai une flemme monstrueuse de recopier ce qu'il y a sur mes photos ici (en plus, ça m'apporterait pas grand chose de juste recopier, et c'est super long), du coup les réponses sont sur mes photos. J'ai recopié certaines choses, mais ça n'est pas exhaustif (et j'ai pas tout compris aussi, des fois)

a) Fp : finally p

b) Gp globally p

c) Xp next p (vrai après l'instant 0)

d) G(p -> Gp) globally (p implique globally p) : dès que p est vraie, elle le devient globalement.

e) G(request -> F grant) et non GF(request U grant) parce que ça autoriserait request à toujours être vraie sans jamais grant.

**A REVOIR à revoir parce que j'ai pas compris :**
f) G(request -> X(¬request U grant)) : globalement (infiniment souvent) une requête doit être suivie de (soit jamais de requête, soit pas de requête jusqu'à ce que grant). Lorsqu'il n'y a pas de requête pendante : à l'instant suivant, il n'y a pas de requête et pas de grant. Lorsqu'il y a une requête : -- ouais, revoir ça j'ai pas compris.

g) G(F cash_withdraw -> pin_ok) : globally (tout le temps) lorsqu'à un moment dans le futur on retire de l'argent, ça implique qu'on a entré un bon code pin.

h) *ext-ce que ça c'est juste ?? G((crit1 -> ¬crit2) ∧ (crit2 -> ¬crit1))*
G(¬(crit1 ∧ crit2)) : globalement, on a jamais crit 1 et crit2
G(¬crit1 ∨ ¬crit2)  : globalement, on a toujours non crit1 ou non crit2
¬F(crit1 ∧ crit2)   : on a jamais au bout d'un moment crit1 et crit2

i) G(ask_crit -> F(acc_crit))

j) G(green -> ¬X(red)) : je pense que `¬X(red)` et `X(¬red)` sont équivalents ici.

k) G(red -> F(green))

**A REVOIR Reprendre ces deux derniers exemples (l et m) pour mieux comprendre.**
l) G(green -> ¬red U (orange ∧ F red)) : hé béh, fallait le trouver celui là

m) G(green -> ¬red U (orange ∧ orange U red))
ou G(green -> F(orange ∧ F red))

### Execrice 2

Les réponses sont sur mes photos. (c'est très simple)

-- fin des notes de la séance du 05-10-2020 --

# Cours 2 - 2020-10-12

Suite du TD 1 (LTL)

### Exercice 3

(a) G(Fp ^ Fq) et GFp ^ GFq
Dans le cas de G(Fp ^ Fq) on doit avoir, quel que soit l'endroit das lequel on est dans la trace : Finally p et q (p et q se produisent infiniment souvent).

G(Fp ^ Fq) st identique à GFp ^ GFq

Ssi pour toute trace t : t ⊧ (phi1) ssi t ⊧ (phi2)

Soit t (app à) (2^AP)^(omega)  (omega) signifie "un nombre infiniment grand" i.e. "répéter infiniment".

t (satisfait) G(Fp et Fq)
<=> t, 0 (sat) G(Fp et Fq)
<=> pour tout j >= 0, t,j (sat) Fp et Fq (par définition de G)
<=> pour tout j >= 0 t,j (sat) Fp et t,j (sat) Fq
<=> pour tout j >= 0 il existe kp >= j tel que t, kp (sat) p ET il existe kp >= j tel que t, kp (sat) q
<=> pour tout j >= 0 

-> reprendre mes notes de cours à partir de mon enregistrement.

### Exercice 4

`{q}{q}{p}{p}{r}{q,r}q{p}{p}{r}`

#### (a)

1.
Gp∨G¬p
t ⊭ Gp OU G¬p car t,0 ⊭ Gp car t,0 ⊭ p.
Donc t,0 ⊭ Gp donc t ⊭ Gp

t,0 ⊭ G¬p car t,2 SAT p donc t,2 ⊭ ¬p.
donc t,0 ⊭ G¬p donc t ⊭ G¬p.

2. Fp ET FNNp
t SAT Fp ET FNNp car
- t SAT Fp car t,2 SAT p donc t,0 SAT Fp
- t SAT FNNp car t,0 NSAT p donc t,0 SAT NNp donc t,0 SAT FNNp


3. F(p AND Xq)

avec les éléments qu'on a, t NSAT F(p AND Xq)
car t,2 SAT p mais t,3 NSAT q donc t,2 NSAT p AND Xq
t,3 SAT q mais t,4 NSAT q donc t,3 NSAT p AND q
t,7 SAT q mais t,8 NSAT q
t,8 SAT p mais t,9 NSAT q.
et pour toutes les autres positions t,j NSAT p.

4. Fp AND Xq
t SAT Fp AND Xq
t,0 SAT Fp AND Xq
car
- t,0 SAT Fp car t,2 SAT p.
- t,0 SAT Xq car t,1 SAT q.

(Xq singifie que q doit être vrai à la position 1 de ma trace, car en l'absence d'indication temporelle, les formules sont à interpréter à la position 0 de la trace).

5.
Rappel : (φ1 -> φ2) ≡ (¬ φ1 v φ2)  (le non n'est pas en facteur) (-> se lit "implique")

(G(p→q))→Gr : soit je satisfais NN G(p->q), soit je satisfais Gr.

t SAT (G(p→q))→Gr
<=> t,0 SAT NN (G(p->q)) OU Gr
<=> t,0 SAT NN(G(p->q)) OU t,0 SAT Gr
(on a t,0 NSAT Gr car t,0 NSAT r)

t,0 SAT NN(G(p->q)) <=> s,0 SAT NN(G(NN p OU q))

La trace ne satisfait pas Gr (car t,0 NSAT Gr car t,0 NSAT r)

2) t,2 SAT p ET NNq
donc t,2 SAT NN(NNp OU q)
donc t,2 SAT NN(p->q)
donc t,2 NSAT p->q
donc t,0 NSAT G(p->q)
donc t,0 SAT NN(G(p->q))







2.Fp∧F¬p3.F(p∧Xq)4.Fp∧Xq5.  (G(p→q))→Gr(b)  Pour chacune des traces ci dessus, proposez deux traces mod`eles de cette formule.

## Cours 2 (toujours 2020-10-12)

Toujours polycopié 1, page 43. La négation de XPHI peut être mise à l'intérieur.

NN F PHI EQUIV G NN PHI
NN G PHI EQUIV F NN PHI

On peut montrer ça, en exercice, il ne sera cependant pas corrigé.

Les exercices de la diapo 44 ont été faits en TD.

**Polycopié 2 maintenant.**

Computation Tree Logic : logique CTL, sert à illustrer a possibilité.

p.2 Le distributeur de droite est non déterministe, il choisit entre : soit que bière, soit que soda.

Une structure de K. vérifie une formule LTL signifie que la structure de K vérifie toute trace vérifiant la formule LTL.

Deux structures de K sont équivalentes lorsqu'elles vérifient les mêmes formules LTL. Cependant, les deux automates (qui sont pourtant différents) sont équivalents en LTL.

p.3 M est une structure de K totale.
A : all, E exists (et c'est aussi le symbole existe à l'envers et pour tout à l'envers)



Exercice p.15
S(EXp) = {1, 2, 3, 5, 6}

"l'ensemble des états qui satisfont EXp"

-- fin du cours 2020-10-12 --



# Cours 3 - 2020-10-19

p.16
E p U q :
En français : il existe une exécution telle que p soit vrai jusqu'à ce que q soit vrai au moins une fois.
Avec ce qui a été vu précédemment :


Pour la prochaine fois (cours 4 du 26) lire le tuto sur la modélisation de processus asynchrones.
Pour le 02/11/2020, envoyer à la prof un petit compte rendu avec les modélisations de l'exo 1 et 2, les corrections... On peut le faire à 2 (mais bon ^^). Du coup en résumé, c'est faire l'exo 1 et 2 + rédiger un compte rendu en décrivant ce qu'on a fait.

# Cours 4 - 2020-10-25

Ressources :
*Spot: a platform for LTL and ω-automata manipulation* : https://spot.lrde.epita.fr/

*LTL 2 BA : fast translation from LTL formulae to Büchi automata* : http://www.lsv.fr/~gastin/ltl2ba/

Vérifier les automates qu'on va avoir en TP.

-> finir de (re) visionner le cours 2020-10-26 d'ASTRE à partir de 3h11min.



# Cours 2020-11-16

p.5 WCET : respect de la durée d'exécution. Worst case execution time
Faute : extérieur, 

P.36 environ : la criticité mixte ne sera pas au programme de l'exam, comme il y a trop de dépendances à des notions antérieures. 

Ce qui peut tomber à l'examen : tous les raisonnements autour du cours : "pour tel ordonnancement, c'est quoi une défaillance ?" "par rapport à tel test d'ordonnançabilité, est-ce qu'on a de la robustesse passive ou non ?" Sachant que les hypothèses nous seront rappelées le jour de l'examen. Il faut savoir ce que c'est qu'EDF et RMF (politiques d'ordonnancement), les tests d'ordennançabilité associés et savoir répondre à des questions telles : "voici la faute qui s'applique sur le système, quelles peuvent être les défaillances... ?" Assez souvent, c'est de la logique. On devrait être capable de dérouler purement le raisonnement logique parce que la tolérance aux fautes c'est essentiellement "comment ue erreur se propage".

Pour réviser pour l'examen, le prof va nous donner des pages de transparents de cours de SFTR. Il va nous donner des dispos de cours qui présentent le cours d'une autre manière (il nous donnera les pages intéressantes), le but étant bien de ne pas nous laisser avec un livre de 400 pages en nous disant "débrouillez vous". le prof va essayer de nous faire ça pour la semaine pro (2020-11-23 du coup). Le prof mettre aussi des ressources "pour aller plus loin" mais ça ne sera pas à l'exam. Le prof nous mettra des exercices et des annales, à un moment.

Le prof part du principe qu'on a bien compris CTL. Du coup bien réviser CTL pour le prochain cours. Bien aller voir les prérequis qui seront mis en ligne sur Moodle.

-> Envoyer à Mme. Encrenaz la vidéo du cours que j'ai faite. (ré-encoder toussa toussa)


31min vidéo 11-14-06.mkv