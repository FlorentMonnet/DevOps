# TP 1

Pour build l'image de postgres : docker build -t postgres-image . 
Le -t permet de nommer notre l'image et donc de l'utiliser plus facilement par la suite.

Pour créer un network : 
```
docker network create app-network
```

Pour lancer le container contenant postgre : 
```
docker run -d -e POSTGRES_DB='tp1' -e POSTGRES_USER='root' -e POSTGRES_PASSWORD='root' --name postgres-app --rm -p 5050:5432 --network=app-network  postgres-image
```
On peut aussi lancer un container avec adminer qui nous permet de visiualiser notre base de données.

Le -d permet de lancer le container en arrière plan.

Le -e permet de passer des variables d'environnements, affecter les variables au moment du docker run permet de lancer deux postgres avec des users, bases de données différentes.

Le -v permet la persistence de nos données. En effet, si on redemarre notre container, on aura toujours les données que l'on a précédément inséré grace à cet argument.

Le --network permet d'assigner un réseau au container et donc lui permettre de communiquer avec d'autres containers. Dans notre cas, c'est nécessaire pour que le java communique avec la BDD.

Pour lancer le docker de l'API Backend : 

```
docker run -d --rm --name java-api --network app-network -p 8080:8080 java-image
```

## Pourquoi on a besoin de faire un build multistage ?

On évite de télecharger les dépendances sur le container qui lance l'application et ça nous permet de réduire le nombre de commandes manuelles.

## Explication du Dockerfile:

```
# Build
FROM maven:3.6.3-jdk-11 AS myapp-build  ---> importe l'image de base de maven
ENV MYAPP_HOME /opt/myapp ---> initialise une variable d'environnement
WORKDIR $MYAPP_HOME ---> Définit la variable d'env comme espace de travail par défaut
COPY pom.xml . ---> copie le fichier pom de notre dossier vers l'espace de travail
RUN mvn dependency:go-offline ---> permet d'éviter le retéléchargement des dépendances quand elles ont déjà été dl
COPY src ./src copie les sources
RUN mvn package -DskipTests ---> on lance maven pour build

# Run
FROM openjdk:11-jre ---> importe l'image de base de maven
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar
ENTRYPOINT java -jar myapp.jar ---> execute le jar généré au dessus
```

## Pourquoi a-t-on besoin d'un reverse proxy ?

Cela sert d'intermediaire entre le client et les ressources d'un serveur, cela permet de gérer les requetes (autoriser,interdir,rediriger).

Dans notre cas, cela permet de rediriger des urls vers nos applis, par exemple le /api vers l'api java, le / vers un fichier html.

## Pourquoi un docker-compose est-il tellement important ?

Cela permet de gerer tous ses containers au sein d'un seul fichier, cela réduit le nombre de commandes à executer(un seul docker-compose up -d au lieu d'un docker run par container). 

Une fois en place, cela permet de lancer, arrêter, relancer tous les containers en même temps, etc. Cela réduit donc les erreurs potentielles.

## Pourquoi est-ce qu'on met nos images sur Docker hub ?

Cela permet de les utiliser dans d'autres applis, par exemple, on pourra utiliser nos images dans Github actions. 

Cela permet aussi de garder un suivi de l'évolution de nos images en utilisant des tags différents à chaque push. En cas de problème, il sera donc possible de revenir sur une image fonctionnelle aisément. 
