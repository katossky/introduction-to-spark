## Connexion au serveur

Lancer Putty depuis le menu démarrer.
**Host Name :** clust-n7

Dans la console qui s'afffiche, renseigner votre identifiant et mot de passe habituel.

Spark se lance en exécutant la commande :

- `/etc/spark/bin/spark-shell` (pour l'interface en Scala, l'interface native)
- `/etc/spark/bin/pyspark` (pour l'interface en Python)
- `/etc/spark/bin/sparkR`  (pour l'interface en R)

Pour ce TP nous travaillerons avec l'historique des vols de ligne aux États-Unis en Janvier 2018. Les données peuvent être téléchargées [ici](<!-- LIEN -->).

## Rappels sur le calcul distribué

## Exercice 1: lecture des données

Une session *Spark* est l'interface privilégiée de programmation. Elle est accessible par le mot-clé `spark`.

<!-- filter -->

**Pour patienter:** refaire les exercices en Python et en R

## Exercice 2: le principe map-reduce

Le principe _map-reduce_ est un sous-ensemble du calculs parallèle ou distribué. Il s'agit de décomposer un calcul long en:

1. une suite d'opérations sur un sous-ensemble des données, ne nécessitant pas de communication entre les processeurs de calcul (étape *map*)
2. la combinaison des résultats intermédiaires en un résultat final, selon le principe d'un accumulateur ; le résultat final est actualisé à chaque fois qu'un processeurs de l'étape intermédiaire termine son calcul (étape *reduce*).

Une analogie est le décompte des voix dans une élection, où l'on procède d'abord par un décompte par bureau de vote. Comme dans le cas d'une élection, la découpe du travail permet de revenir localement sur un sous-travail (ici, le décompte d'un bureau spécifique), sans compromettre le reste des opérations (le décompte dans les autres bureaux). Le principe *map-reduce* est dit "peu sensible aux erreurs" (EN: _fault-tolerant_): la panne d'un processeur/nœud ne compromet pas l'ensemble du calcul, et les calculs non effectués sont immédiatement transmis à d'autres processeurs/nœuds.

**Q.2.1.** Trouvez deux exemples de calculs faciles à paralléliser avec le principe _map-reduce_ et un exemple de calcul difficile ou impossible à paralléliser sur ce principe. <!-- Facile: moyenne, somme, techniques de Monte Carlo. Difficile: inversion de matrice. Impossible: travelling salesman. Opposition entre "embarassingly parallel problems" et "inherently sequential problems"[^1]. -->

La méthode `count()` est elle-aussi une opération _map-reduce_. `dataFlight.count()` est équivalent à:

```{scala}
dataFlight
  .map(flight => 1)
  .reduce( (accumulator, value) => accumulator + value ) // syntaxe équivalente: .reduce(_+_)
```

**Q.2.2.** Pourquoi ce code produit-il le même résultat que `count`? Expliquez la syntaxe `flight => 1` et `(accumulator, value) => accumulator + value`. Comment appelle-t-on ce type d'objet en programmation?

<!-- Au fur et à mesure que les différentes sous-tâches ont fini leur exécution, `accumulator` se rapproche du résultat attendu. (En réalité l'opération `reduce` est le plus souvent commutative puisque le résultat final doit être le même quel que soit l'ordre d'exécution des tâches du `map`. La distinction formelle entre `accumulator` et `value` est donc plus pédagogique qu'autre chose.) -->

**Q.2.3.** Changez une ligne du code précédent pour calculer la distance totale parcourrue par des avions de ligne au mois de janvier 2018.

**Q.2.4.** Que fait la fonction suivante? Et le code qui suit?

```{scala}
def myFunction( a:Double, b:Double ) : Double = if(b > a) b else a

dataFlight
  .map(flight => flight.ARR_DELAY)
  .reduce( myFunction )
```
<!-- Il est possible d'utiliser des fonctions nommées dans l'étape reduce. -->

**Q.2.5.** L'étape `map` peut renvoyer un n-uplet (EN: _tupple_) et l'opération `reduce` porter sur le n-uplet retourné par chaque processeur / nœud. Que fait le code suivant?

```{scala}
dataFlight
  .map(flight => (flight.ARR_DELAY, flight.FL_DATE))
  .reduce( (a, b) if(a._1 > b_1) a else b )
```

**Remarque:** `a._1` permet d'accéder au premier élément du n-upplet `a`.

[^1]: https://softwareengineering.stackexchange.com/questions/144787/what-kind-of-problems-does-mapreduce-solve ; https://stackoverflow.com/questions/806569/whats-the-opposite-of-embarrassingly-parallel ; https://en.wikipedia.org/wiki/Embarrassingly_parallel

**Pour patienter:** refaire les exercices en Python et en R


<!--
dataFligth
  .map(flight => flight.DEP_TIME)
  .sort()
  .reduce((a,b) => {println(b);return 1})
-->

<!-- oordre d'exécution-->
<!-- flatMap : reprgorammer la fonciton filter ? -->

## Exercice 3: transformation de données, mise en cache, arbitrage map-reduce

<!-- exécution sur quel processeur / noeud -->
<!-- montrer la duplication -->
<!-- montrer le choix de lieu de stockage -->
<!-- les différentes possibilités -->
<!-- temps d'exécution -->

3.1 Créez un nombre aléatoire entre 0 et 1 pour chaque vol de la base de donnée.

3.2 Calculez la moyenne de ces nombre, de façon locale

3.3 Calculez leur moyenne, de façon distribuée selon le schéma _map-reduce_. (Réfléchissez à comment aggréger les sous-calculs avec `reduce`.) <!-- Solution facile: 2 variables. Solutions difficile: map renvoie un tupple. -->

3.4 Combien de temps avez vous gagné? Pourquoi le résultat est-il différent? <!-- Spark pratique l'évaluation retardée (EN: _lazy evaluation_): les expressions sont gardées en forme littérale jusqu'à ce qu'une étape `reduce` soit appelée (`count` compte comme `reduce`). Du coup, la génération aléatoire est effectuée plusieurs fois. -->

3.5 Il est possible de forcer l'évaluation d'un résultat intermédiaire avec les méthodes `cache()` et `persist()`. Cela est utile quand votre flux de donnees (EN: _data flow_) possède des "branches", c-à-d lorsqu'une étape de pré-traitement est réutilisée par plusieurs traitements en aval. En ne modifiant qu'une seule ligne de code, appliquez ce principe au calcul de moyenne précédent.

3.6 Répétez l'opération pour le calcul de la variance. Combien de temps avez vous gagné?

3.7 La somme peut être réalisée soit dans l'étape _map_ (sur un seul processeur, donc séquentiellement) soit dans l'étape _reduce_ (parallélisé mais besoin de temps pour additionner). Essayer plusieurs façon de découper le data frame en évaluant le temps d'exécution.


## Pour approfondir/ réviser:

- Une série de billets introductifs en français:
    1. https://aseigneurin.github.io/2014/10/29/introduction-apache-spark.html
    2. https://aseigneurin.github.io/2014/11/01/initiation-mapreduce-avec-apache-spark.html
    3. https://aseigneurin.github.io/2014/11/06/mapreduce-et-manipulations-par-cles-avec-apache-spark.html
    
- "Quick start", sur le site officiel de Spark: https://spark.apache.org/docs/latest/quick-start.html

- Introduction à Scala: https://docs.scala-lang.org/tutorials/tour/tour-of-scala.html