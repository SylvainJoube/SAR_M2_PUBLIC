# ARA - fiche résumé de cours

Trucs à re-réviser si le temps : 
- exo 6 TD 1 (2020-10-06)
- question 1.6 TD 3 (2020-10-20)

## Protocole_diffusion_2020 - cours 1

**p.19**
```
Un groupe peut être :
• fermé : broadcast(m) ne peut être appelé que par un membre du groupe
• ouvert : broadcast(m) peut être appelé par un processus extérieur au groupe
```

Diffusion Best Effort, Fiable, Fiable Uniforme : à partir de p.23
Ordre FIFO, Causal, Total : à partir de p.23

En résumé :

**Diffusion Best-effort** (p.24) -> Garantie la délivrance d'un message à tous les processus corrects si l'émetteur est correct.

**Diffusion Fiable (Reliable Broadcast)** (p.26) ->
- si l'émetteur du message m est correct, alors tous les destinataires corrects délivrent le message m.
- si l'émetteur du message m est fautif, tous ou aucun processus corrects délivrent le message m.

**Diffusion Fiable Uniforme (Uniform Reliable Broadcast)** (p.33) -> 
- Si un message m est délivré par un processus (fautif ou correct), alors tout processus correct finit aussi par délivrer m.

**Ordre** (p.36)

**Ordre Total**
- Les messages sont délivrés dans le même ordre à tous leurs
destinataires.

**Ordre FIFO**
- si un membre diffuse m1 puis m2, alors tout membre correct qui
délivre m2 délivre m1 avant m2.

**Ordre Causal**
- si broadcast(m1) précède causalement broadcast (m2), alors
tout processus correct qui délivre m2, délivre m1 avant m2.

**Types de Diffusion Fiable (1)** (p.41) :
- `Diffusion FIFO`              = Diffusion fiable + Ordre FIFO
- `Diffusion Causal (CBCAST)`   = Diffusion fiable + Ordre Causal
- `Diffusion Atomique (ABCAST)` = Diffusion fiable + Ordre Total
- `Diffusion Atomique FIFO`     = Diffusion FIFO + Ordre Total
- `Diffusion Atomique Causal`   = Diffusion Causal + Ordre Total




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



**Complétude (completeness)** - *p.16*
- **Forte** : *Il existe un instant à partir duquel tout processus défaillant* est
suspecté par **tous** les processus corrects
- **Faible** : *Il existe un instant à partir duquel tout processus défaillant* est
suspecté par **un** processus correct

**Justesse (accuracy)** - *p.16*
- **Forte** : aucun processus correct n’est suspecté
- **Faible** : il existe au moins un processus correct qui n’est jamais suspecté
- **Finalement forte** : *forte au bout d'un moment* -> il existe un instant à partir duquel tout processus correct n’est plus suspecté par aucun processus correct
- **Finalement faible** : *faible au bout d'un moment* -> il existe un instant à partir duquel au moins un processus correct n’est suspecté par aucun processus correct

**Classes de détecteurs de fautes** - *p.17*
- P (Perfect) -> Complétude forte + justesse forte
- S (Strong) -> Complétude forte + justesse faible
- Q -> Complétude faible + justesse forte
- W -> Complétude faible + justesse faible
- Diamond signifie "au bout d'un moment"


Cours 3 `Shared_memory_2020` (aussi noté cours 2, après celui de Jonathan)

**MRSW <=> multi-reader, single-writer**

**SRSW <=> single-reader, single-writer**


