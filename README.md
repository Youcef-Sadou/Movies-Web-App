# Movies-Web-App

### 1 Introduction

Ce projet est une application web qui permet aux utilisateurs d'obtenir un
résumé très utile concernant un grand nombre de films.

#### 1.1 Impératifs du sujet

Pour répondre à la problématique du sujet, les éléments suivants
doivent être réuni :
  - Nettoyage du jeu de données et de la structure.
  - Stocker l'ensemble de données dans la base de données et les agréger dans différentes collections.
  - Développement des requêtes graphQL après avoir créé la requête du résolveur et défini les types dans le schéma graphQL.
  - Visualiser les données obtenues par les requêtes graphQL demanière esthétique en utilisant d3.js.
Voici donc les 4 axes que nous avons cherché à développer pour ce projet.

### 2 Ensemble de données et structure

Nous avons donc choisi un ensemble de données contenant 5,000
films extraits d'Amazon recherche dans le cloud, vous pouvez trouver cet
ensemble de données ici http://chewbii.com/json-datasets/. Nous avons
donc choisi ce jeu de données principalement pour deux raisons :
  1) Le grand nombre d'articles.
  2) La richesse de chaque élément individuel (film) en termes d'attribut donc lors du téléchargement du fichier json, nous avons vu qu'il ne s'agit pas d'un tableau mais d'un tas d'objets sans virgule séparer un objet du suivant, voici le schéma des données brutes.

![](/screenshots/s1.png)

Nous avons donc utilisé des expressions régulières pour ajouter une
virgule entre les lignes pour obtenir un ensemble de données qui
ressemble beaucoup plus à un tableau, et nous avons également éliminé
les attributs “fields”, “id” et “type”, donc nous travaillons avec des
données pures.
Voici le schéma des données original :

![](/screenshots/s2.png)

Voici le schéma des données modifiées :

![](/screenshots/s3.png)

### 3 Stocker l'ensemble de données dans la base de données et les agréger

Pour stocker l'ensemble de données dans la base de données, et les
agréger nous devons d'abord lancer les conteneurs en exécutant la
commande suivant :
docker-compose -f stack.yml up -d
Pour lancer le conteneur mongo et le faire fonctionner, puis nous
importons les données dans notre base de données "movis" et "films "
collection à l'aide de la commande jq vue ci-dessous :

![](/screenshots/s4.png)

Comme nous pouvons le voir, tous les documents ont été importés avec
succès.
La prochaine étape consiste à agréger cette collection de films en
différentes collections que nous utiliserons pour demandes , par exemple
ci-dessous, vous pouvez voir la commande lancée dans mongo afin de
créer la collection de réalisateurs qui a un réalisateur et le nombre de
films que ce réalisateur a réalisés, ici nous utilisé le mot-clé spécial
$unwind car à la première tentative, les réalisateurs s'affichent sous la
forme d'un tableau de chaînes dans un document car plusieurs réalisateurs
travaillent souvent sur un même film et ce mot clé déstructure le tableau
donc ils sont comptés comme des éléments uniques.

![](/screenshots/s5.png)

Dans notre base de données movis, nous avons 7 collections comme
indiqué ci-dessous :

![](/screenshots/s6.png)


- Collection d'acteurs contenant le nom de l'acteur et le nombre de
films dans lesquels il a joué.
- Collection de réalisateurs contenant le nom du réalisateur et le
nombre de films qu'il a réalisés.
- Collection de genres qui contient le titre du film et le genre de ce
film (Action, Comédie ..).
- Collection de films qui est la collection originale à partir de
laquelle nous avons dérivé d'autres collections.
- Collection de classements qui contient le titre du film et la
popularité de ce film au moment de la création de la base de
données.
- Collection de notes qui contient le titre du film et la note qui lui a
été attribuée par les critiques de cinéma.
- Collection d'années qui contient le titre du film et l'année de sa
réalisation.

### 4 Ensemble de données et structure

Maintenant que toutes nos collections sont dans la base de données
mongo, nous pouvons commencer à développer les requêtes graphQL.

Nous nous connectons d'abord à la base de données movis dans notre
script resolvers.js:

![](/screenshots/s7.png)

Puis nous définissons une requête qui trouve le document de la collection
et renvoie le résultat :

![](/screenshots/s8.png)

Ensuite, ce que nous devons faire est de définir le type et la requête à
l'intérieur de notre schéma de modèle dans le dossier model.graphql
comme indiqué ci-dessous

![](/screenshots/s9.png)

ensuite nous lançons le serveur de node et allons sur le port 4000 pour
développer nos requêtes

![](/screenshots/s10.png)

comme vous pouvez le voir, voici quelques-unes des requêtes que nous
utilisons ci-dessous

![](/screenshots/s11.png)

### 5 Visualiser les données via d3.js

Maintenant que nos requêtes sont développées, il est temps de
développer notre front-end en utilisant d3.js

#### 5.1 Approche générale du développement

Nous avons choisi de visualiser nos données en nous basant sur la galerie d3 
et de les spécialiser.
Nous appelons la requête avec un appel asynchrone puis une fois nous 
recevons le résultat nous exécutons la fonction draw (pour la page des années nous 
appelons DrawYears par exemple).
Veuillez noter que nous avons parfois besoin de restructurer les données
pour dessiner nos graphiques.

![](/screenshots/s12.png)

#### 5.2 Pages de visualisation

Nous avons choisi de travailler sur 6 pages de visualisation. page qui
montre le nombre de films dans lesquels un acteur a joué, nous pouvons
trier cette page par le nom de l'acteur ou le nombre de films à l'aide d'un
graphique à barres.

![](/screenshots/s13.png)

Page qui montre combien de films un réalisateur a réalisé, nous
pouvons trier cette page par le nom du réalisateur ou le nombre de films à
l'aide d'un graphique à barres.

![](/screenshots/s14.png)

Une page qui montre le rang d'un film et le titre, on peut trier cette page
par le nom du film ou son rang à l'aide d'un histogramme.

![](/screenshots/s15.png)

Une page qui montre les différentes notes des films (de 0 à 10) et combien
de films ont reçu cette note à l'aide d'un histogramme.

![](/screenshots/s16.png)

Une page qui montre le nombre de films pour chaque année (de 1920 à
2018) à l'aide d'un graphique linéaire.

![](/screenshots/s17.png)

Une page qui affiche le genre (Horreur, Comédie...) des films à l'aide d'un
à l'aide d'un graphique à secteurs.

![](/screenshots/s18.png)

### 6 Objectif atteint ?

Si nous reprenons les 4 grandes problématiques que nous nous étions
fixées en introduction, on peut dire de manière raisonnable que nous
avons répondu au sujet.

En effet, nous avons nettoyé notre structure de données, nous avons
stocké l'ensemble de données dans la base de données et les avons
agrégés dans différentes collections nous avons développé les requêtes
graphQL après avoir créé la requête du résolveur et défini les types dans
le schéma graphql nous avons visualisé les données obtenues par les
requêtes graphQL de manière esthétique en utilisant d3.js.


