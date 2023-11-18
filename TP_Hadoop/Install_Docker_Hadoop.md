**Sommaire**


# Installation de **Hadoop** via _Docker_

Les étapes pour installer **Hadoop** via _Docker_ sont largement adaptées de la page de [Farah MHAMDI](https://hub.docker.com/repository/docker/farahmhamdi/hadoop/), elles-mêmes reposant sur le projet [github de Kai LIU](https://github.com/kiwenlau/Hadoop-cluster-docker).

---
## Installation de *Docker* et des nœuds

Pour installer *Docker*, merci de suivre les [consignes disponibles ici](https://docs.docker.com/desktop/), en fonction de votre système d'exploitation (lisez les _System requirements_ pour vérifier que votre machine est adaptée). Si votre machine est trop ancienne, ou avec peu d'espace disque ou mémoire RAM, il y a de bonnes chances que l'installation ne fonctionne pas. Si c'est le cas, 

 - soit vous pouvez travailler avec votre voisin,    
 - soit vous pouvez aller directement à la seconde partie du TP, et réaliser les exercices en local (sans **Hadoop**).


Nous allons utiliser tout au long de ce TP trois contenaires représentant respectivement un nœud maître (le _Namenode_) et deux nœuds esclaves (les _Datanodes_).

1. Depuis un _Terminal_, téléchargez l'image docker depuis [_dockerhub_](https://hub.docker.com) (volume à télécharger : 1.76 GB!) :
```shell
docker pull farahmhamdi/hadoop:latest
```
Ce container contient une distribution _Linux/Ubuntu_, et les librairies nécessaires pour utiliser **Hadoop**. Ce container ne contient pas _Python_, mais nous verrons comment l'installer _a posteriori_.

2. Créez les trois contenaires à partir de l'image téléchargée. Pour cela:

     a. Créez un réseau qui permettra de relier les trois contenaires:
     ```shell
     docker network create --driver=bridge hadoop
     ```   
     b. Créez et lancez les trois contenaires (les instructions `-p` permettent de faire un _mapping_ entre les ports de la machine hôte et ceux du contenaire):
     ```shell
     docker run -itd --net=hadoop -p 50070:50070 -p 8088:8088 -p 7077:7077 -p 16010:16010 --name hadoop-master --hostname hadoop-master farahmhamdi/hadoop:latest

     docker run -itd -p 8040:8042 --net=hadoop --name hadoop-slave1 --hostname hadoop-slave1 farahmhamdi/hadoop:latest

     docker run -itd -p 8041:8042 --net=hadoop --name hadoop-slave2 --hostname hadoop-slave2 farahmhamdi/hadoop:latest
     ```     
   **Remarque** Sur certaines machines, la première ligne de commande ne s’exécute pas correctement. L'erreur provient sans doute du port `50070` que doit déjà être utilisé par une autre application installée sur votre machine. Vous pouvez alors supprimer ce port de la première ligne de commande :
   ```shell
   docker run -itd --net=hadoop -p 8088:8088 -p 7077:7077 -p 16010:16010 --name hadoop-master --hostname hadoop-master farahmhamdi/hadoop:latest
   ```

---
## Préparation au TP

1. Entrez dans le contenaire `hadoop-master` pour commencer à l'utiliser
 ```shell
 docker exec -it hadoop-master bash
 ```
 Le résultat de cette exécution sera le suivant:
 ```shell
 root@hadoop-master:~#
 ```
 Il s'agit du ```shell``` ou du ```bash``` (_Linux/Ubuntu_) du nœud maître. 
Exécuter la commande suivante :
Ce script shell est conçu pour démarrer les services Hadoop associés au système de fichiers distribué HDFS et au gestionnaire de ressources YARN
 ```shell
./start-hadoop.sh
 ```
 La commande ls, qui liste les fichiers et dossiers du dossier en cours, doit faire état des fichiers suivants : un fichier purchases.txt qui sera utilisé lors de la seconde partie du TP.
 ```shell
ls -all
 ```

**Remarque** Ces étapes de configuration ne doivent être réalisées qu'une seule fois. Pour relancer le cluster (une fois qu'on a fermer et relancer son ordinateur p. ex.), il suffira 

  1. de lancer la commande suivante :
   ```shell
   docker start hadoop-master hadoop-slave1 hadoop-slave2
   ```

Vous pouvez alors entrer dans le _Namenode_ :
```shell
docker exec -it hadoop-master bash
```
