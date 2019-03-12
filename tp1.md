## Connexion au serveur

Lancer Putty depuis le menu démarrer.
**Host Name :** clust-n7

Dans la console qui s'afffiche, renseigner votre identifiant et mot de passe habituel.

Spark se lance en exécutant la commande :

- `/etc/spark/bin/spark-shell` (pour l'interface en Scala, l'interface native)
- `/etc/spark/bin/pyspark` (pour l'interface en Python)
- `/etc/spark/bin/sparkR`  (pour l'interface en R)

Les données sont accessibles ......... . Les données originales viennent de.......... .

## Rappels sur le calcul distribué

## Exercice 1: lecture des données

Une session *Spark* est l'interface privilégiée de programmation. Elle est accessible par le mot-clé `spark`.

**Pour patienter:** refaire les exercices en Python et en R

## Exercice 2: le principe map-reduce

Le principe map-reduce est un sous-ensemble du calculs parallèle ou distribué. Il s'agit de décomposer un calcul long en:

1. une suite d'opérations sur un sous-ensemble des données, ne nécessitant pas de communication entre les processeurs de calcul (étape *map*)
2. la combinaison des résultats intermédiaires en un résultat final, selon le principe d'un accumulateur ; le résultat final est actualisé à chaque fois qu'un processeurs de l'étape intermédiaire termine son calcul (étape *reduce*).

Une analogie est le décompte des voix dans une élection, où l'on procède d'abord par un décompte par bureau de vote. Comme dans le cas d'une élection, la découpe du travail permet de revenir localement sur un sous-travail (ici, le décompte d'un bureau spécifique), sans compromettre le reste des opérations (le décompte dans les autres bureaux). Le principe *map-reduce* est dit "peu sensible aux erreurs" (EN: _fault-tolerant_): la panne d'un processeur/nœud ne compromet pas l'ensemble du calcul, et les calculs non effectués sont immédiatement transmis à d'autres processeurs/nœuds.

1. Trouvez deux exemples de calculs faciles à paralléliser avec le principe _map-reduce_ et un exemple de calcul difficile ou impossible à paralléliser sur ce principe. <!-- Facile: moyenne, somme, techniques de Monte Carlo. Difficile: inversion de matrice. Impossible: travelling salesman. Opposition entre "embarassingly parallel problems" et "inherently sequential problems"[^1]. -->
2. 
3. 
4.

[^1]: https://softwareengineering.stackexchange.com/questions/144787/what-kind-of-problems-does-mapreduce-solve ; https://stackoverflow.com/questions/806569/whats-the-opposite-of-embarrassingly-parallel ; https://en.wikipedia.org/wiki/Embarrassingly_parallel

**Pour patienter:** refaire les exercices en Python et en R

## Exercice 3: génération et transformation de données

3.1 Générez un milion d'entiers.
3.2 Calculez leur moyenne, directement puis de façon distribuée
3.3 Calculez leur variance, directement puis de façon distribuée
3.4 Vous pouvez 

## Exercice 4: applications

**Pour patienter:** refaire les exercices en Python et en R





## Pour approfondir/ réviser:

- Une série de billets introductifs en français:
    1. https://aseigneurin.github.io/2014/10/29/introduction-apache-spark.html
    2. https://aseigneurin.github.io/2014/11/01/initiation-mapreduce-avec-apache-spark.html
    3. https://aseigneurin.github.io/2014/11/06/mapreduce-et-manipulations-par-cles-avec-apache-spark.html
    
- "Quick start", sur le site officiel de Spark: https://spark.apache.org/docs/latest/quick-start.html

- Introduction à Scala: https://docs.scala-lang.org/tutorials/tour/tour-of-scala.html.html