# Tutoriel Scala 

#### ---------------------------------------------------------------------------------------------------------------

# Contextes d’exécution

#### ---------------------------------------------------------------------------------------------------------------

### Le 23/01/2019

## Université : Paris 13 / Institut Galilée

## Master 2 : Exploration informatique des données et décisionnel (EID2)

## Année universitaire : 2018 / 2019

## Réalisé par : Achraf DAHBI

## Supervisé par : M. Pierre BOUDES



Bonjour et bienvenue dans mon tutoriel nommé Scala-ec. Je suis Achraf DAHBI
étudiant en Master 2 Exploration informatique des données et décisionnel (EID2) et
je vais essayer de vous expliquer dans ce tutoriel ce qu’un contexte d’exécution en
Scala, et comment et pourquoi gérer des contextes d’exécutions. Bonne lecture à
tous!

## D’abord pourquoi Scala?

Scala est un langage de programmation facile, élégant et puissant. C’est un langage
de programmation multi-paradigme.

- Utilisation de la programmation orientée objet.
- Sa syntaxe et son utilisation sont simples.
- L’utilisation du langage est portable, Windows, Linux, Mac OS ou autre.
- On peut faire tout ce qu'on l’on souhaite.

## Contexte d’exécution en informatique

Avant de répondre aux questions posées par le sujet, définissons d’abord ce qu’un
contexte d’exécution en informatique :

Un **contexte d’exécution** est l’ensemble minimal de données utilisé par une tâche
(qui peut être un processus ou un thread ) qui doit être sauvegardé pour permettre à
une tâche d'être interrompue et poursuivie ultérieurement à partir du même point. Le
concept de contexte prend tout son sens dans le cas de tâches pouvant être
interrompues, dans lesquelles, lors de l'interruption, le processeur enregistre le
contexte et continue à servir la routine de service d'interruption. Ainsi, plus le
contexte est petit, plus la latence est faible. Les données de contexte peuvent être
situées :

- Dans des registres de processeur.
- Dans la mémoire utilisée par la tâche.
- Dans des registres de contrôle utilisés par certains systèmes d'exploitation pour
gérer la tâche.

## Introduction

En programmant avec Scala, nous utilisons souvent des constructions parallèles
(future par exemple). Toutefois on trouve un paramètre implicite de type
ExecutionContext, quelque chose de similaire avec le concept de pool et de threads.
Dans ce tuto, je vais essayer d’expliquer exactement ce qu’un contexte d’exécution,
et quel est le but de l’introduire dans le langage Scala.

## Idée générale


L'idée de base est assez simple : nous avons un rappel qui va être exécuté à un
moment donné. Sur quel fil sera-t-il exécuté? Le courant? Un nouveau?
C'est au contexte d'exécution de décider. La valeur par défaut
(ExecutionContext.global) utilise les threads d'un pool global (avec un nombre de
threads déterminé par le nombre de cœurs de processeur dont nous disposions).
Dans d'autres circonstances, nous pouvons utiliser un contexte différent. Par
exemple, les acteurs Akka peuvent utiliser leur répartiteur comme contexte
d'exécution.

## Futur et contexte d’exécution

Afin de pouvoir utiliser un Futur (un objet fictif pour une valeur qui n'existe peut-être
pas encore) nous avons besoin d’un ExecutionContext. Il existe trois manières
d'utiliser le contexte d'exécution global :

Importation du contexte d'exécution global :

En utilisant une variable implicite :

Passer explicitement le contexte d'exécution :

Bien qu'il soit simplement possible d’importer
scala.concurrent.ExecutionContext.Implicits.global pour obtenir un implicit
ExecutionContext, les développeurs d'applications doivent soigneusement
déterminer où ils souhaitent définir une politique d'exécution. Idéalement, un
emplacement par application (ou par section de code liée logiquement) déterminera
quel contexte d’exécution faut utiliser. L'approche recommandée consiste à ajouter
(implicit ec : ExecutionContext) aux méthodes, ou aux paramètres du constructeur
de classe, qui nécessitent un ExecutionContext.

## Syntaxe du Contexte d’exécution en Scala

De la même manière que Java, Scala ExecutionContext permet de séparer la
logique applicative (c’est-à-dire ce que fait le code) de la logique d’exécution (c.-à-d.
Comment le code est exécuté).


En conséquence, on ne peut pas simplement importer le contexte d'exécution global
et s'en sortir. Au lieu de cela, nous devons comprendre quel contexte d’exécution est
nécessaire et pourquoi.

## Le contexte global d'exécution

Comme le contexte d'exécution global est celui par défaut et le plus facile à
configurer, examinons ce qu'il en est réellement.
La première chose à noter est que le ExecutionContext.global et le
ExecutionContext.Implicits.global sont exactement la même chose. Ensuite, il
appelle le fromExecutor constructeur mais ne fournit aucun exécuteur.

Il est défini dans la bibliothèque standard de Scala. C'est un spécial ForkJoinPool
qui utilise la méthode blocking pour gérer le code potentiellement bloquant afin de
lancer de nouveaux threads dans le pool. Il a également le potentiel de produire
beaucoup de fils et d'utiliser une tonne de mémoire, si vous n'êtes pas prudent.

## scala.concurrent.context.maxThreads

Lorsque la propriété scala.concurrent.context.maxThreads n'est pas définie, le
nombre de cœurs disponibles sur le système est défini par défaut. Plus important
encore, cela signifie qu'il est fort probable que les paramètres soient différents entre
l'environnement de développement et l'environnement de production. Ce qu'il faut
retenir en cas de problème de performance.

## Conclusion

En résumé, voici les principaux pièges à éviter :

- Utilisation de l'ExecutionContext global pour des tâches non liées au
processeur (par exemple, Préférer un ThreadPool dédié aux tâches liées à l'IO).
- Ne pas connaître les paramètres de parallélisme.
- On suppose que blocking fonctionne avec n'importe quel ExecutionContext.

## Références et annexes :

https://www.scala-lang.org/api/current/scala/concurrent/ExecutionContext.html
https://docs.scala-lang.org/overviews/core/futures.html#the-global-execution-
context


