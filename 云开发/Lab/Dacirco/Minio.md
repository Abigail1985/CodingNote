

## 1 Introduction

Le but de ce TP est de se familiariser avec MinIO - MinIO, Multi-Cloud Object Storage (https://min.io/) - un système de stockage à base d'objets pour le Cloud. Pour ce faire, une instance a été déployée sur chacun des bancs des salles B27 et B30. Vous pouvez accéder à votre instance grâce aux informations suivantes:

- URL: http://10.room.bench.12:9000
- Access key: minio
- Secret key: pass4minio

Le cluster Minio utilise les 4 serveurs de votre banc pour offrir un service hautement disponible. Sur chacun des 4 serveurs, il y a 4 disques (en réalité, 4 partitions Minio sur un même disque). Si un serveur est défaillant ou si 4 disques (partitions) sont hors d'usage, votre cluster Minio continuera à assurer des opérations de lecture et d'écriture.

Pour les besoins du TP, nous considérerons cependant que le premier serveur (**minio1**) ne peut pas être défaillant. Ce serveur **minio1**hébergera la porte d'entrée de votre cluster et vous utiliserez également ce serveur pour le travail de développement.

## 2 Installation de mc

MinIO fournit également un client pour interagir avec votre cluster depuis la ligne de commande.

Configurez l'outil **mc** fourni par MinIO pour référencer votre cluster:

```
> mc config host add minio <URL> <Access key> <Secret key>
```

## 3 Installation de Prometheus

Prometheus (https://prometheus.io/) est un logiciel libre de supervision des systèmes cloud. Vous allez l'utiliser pour enregistrer certaines métriques de votre cluster. MinIO s'interface directement avec Promotheus pour y stocker des données qui sont ensuite affichées via l'interface web.

Prometheus a déjà été téléchargé dans le répertoire `/home/user/prometheus` mais n'a pas été configuré. Pour interfacer MinIO avec Promotheus, vous allez utiliser la procédure ci-dessous qui est tirée de la [documentation officielle](https://docs.min.io/docs/how-to-monitor-minio-using-prometheus.html).

Récupérez les credentials avec:

```
> mc admin prometheus generate minio
```

Vous obtiendrez une réponse similaire à ceci:

```
scrape_configs:
- job_name: minio-job
  bearer_token: eyJhbGciOiJIUzUxMiIsInR5cCI ... to be continued ...
  metrics_path: /minio/v2/metrics/cluster
  scheme: http
  static_configs:
  - targets: ['10.ROOM.BENCH.12:9000']
```

Copiez cette sortie à partir de job-name dans le fichier `/home/user/prometheus/prometheus.yml` dans la section`scrape_configs`.

Ouvrez un nouveau terminal pour lancer prometheus dans un shell qui ne devra pas être tué avant la fin du TP.

```
> cd ~/prometheus
> ./prometheus --config.file=prometheus.yml
```

## 4 Découverte de l'interface Web de MinIO

L'interface web de votre cluster MinIO est accessible sur le port 9001.

Connectez-vous à l'interface web de MinIO et parcourez les différents menus pour découvrir les possibilités qui vous sont offertes.

Une fois que vous avez pris en main l'interface, vous allez expérimenter la création d'un bucket et d'objets.

Dans votre terminal, générez un fichier d'exactement 1Go contenant des données aléatoires. Pour ce faire, vous pouvez utiliser la commande suivante:

```
> head -c 1G </dev/urandom >test_file
```

Créez maintenant un bucket sur votre instance MinIO via l'interface web ou la ligne de commande et ajoutez le fichier que vous venez de générer. Regardez dans le **dashboard** les données de contrôle. En particulier, les données de trafic réseau sont celles qui vont nous intéresser par la suite.

Regardez maintenant les informations dont dispose MinIO sur votre objet. La seule métadonnée dont dispose le fichier que vous venez d'ajouter est son type `content-type:application/octet-stream`.

Un des intérêts des systèmes de stockage à base d'objet est de pouvoir ajouter des métadonnées qui vont ajouter de l'information au sujet de l'objet (rappelez-vous : les métadonnées sont parfois plus importantes que les données elles-mêmes). Vous allez créer un nouvel objet, toujours à partir du fichier que vous avez généré, mais en y adjoignant des métadonnées. 

Parcourez les différentes commandes disponibles avec `mc`. Vous pouvez par exemple voir la configuration de votre instance grâce à :

```
> mc admin info minio/
```

Retrouvez et listez le contenu du bucket que vous avez initialement créé puis affichez les métadonnées de votre objet. Vous devriez obtenir les mêmes informations que via l'interface web.

Toujours grâce à l'outil `mc`, copiez votre fichier local `test_file` sur votre instance de MinIO en y ajoutant les métadonnées de géolocalisation suivantes : 

- Latitude : 48.120391837162224
- Longitude: -1.628658659220642

Vérifiez que les métadonnées de votre objet ont bien été renseignées.

## 5 Évaluation des performances

Vous allez maintenant évaluer plus finement les performances en écriture de votre instance MinIO. Pour ce faire, vous avez une structure de code Python `minio_perf.py` (`www.cloud.rennes.enst-bretagne.fr/minio_files/minio_perf.py`) à votre disposition qu'il vous faudra compléter.

### 5.1 Prérequis

```
> python3 -m pip install --upgrade pip
> python3 -m pip install --upgrade Pillow
> python3 -m pip install matplotlib
> python3 -m pip install seaborn
> python3 -m pip install minio
```

Dans un premier temps, remplissez les informations de connection de votre instance de MinIO et vérifiez que vous parvenez bien à lancer le script. Ce script va uploader un object de taille fixe généré aléatoirement (ce que vous avez fait manuellement un peu plus tôt) et estimer la bande passante en écriture obtenue.

Modifiez le script Python de façon à itérer sur plusieurs tailles de fichier. L'idéal serait de tester au minimum une dizaine de tailles de fichier dans l'intervalle [1Ko; 10Go].

Un seul transfert par taille de fichier n'est pas suffisant pour avoir une évaluation précise de la capacité de votre instance MinIO a absorber des données. Modifiez à nouveau le script Python de façon à faire 10 transferts indépendants par taille de fichier. Vous en extrairez une bande passante en écriture moyenne ainsi qu'un écart type que vous stockerez dans des listes. Pour ces calculs, regardez du côté des fonctions `mean()` et `std()` de la bibliothèque Python `numpy`. À la fin de l'exécution de votre benchmark, vous devriez obtenir trois listes de même taille : les tailles de fichiers transférés, les bandes passantes moyennes obtenues pour chaque taille et les écart-types correspondant. La fonction `plot_bandwidth()` incluse dans le script prend ces trois listes en paramètre et trace la courbe d'évolution de la bande passante en fonction de la taille du transfert. Appelez cette fonction pour visualiser les performances de votre instance de MinIO en écriture.

Analysez les résultats du graphique que vous venez d'obtenir. Comparez notamment la vitesse d'écriture aux caractéristiques techniques de votre cluster. Comment expliquez-vous les éventuelles différences et les variations en fonction de la taille du fichier?

## 6 Résilience

L'un des intérêts des systèmes de stockage objet est la sécurité qu'ils apportent en redondant les données sur différents nœuds. Vous allez vérifier que votre cluster implémente bien cette fonctionnalité.

Générez un fichier de 1Go avec la commande du début de TP. Calculez en le hash (**md5sum**) et placez le dans un bucket.

Puis déconnecter un des serveurs MinIO (par exemple en désactivant son interface réseau). La technique d'erasure coding devrait permettre de reconstruire et récupérer malgré tout l'intégralité du fichier et d'en vérifier l'intégrité avec la somme de hashage.

Author: François Tessier

Created: 2021-11-30 Tue 13:36

[Validate](https://validator.w3.org/check?uri=referer)