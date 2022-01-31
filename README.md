# DevOps

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
Pour lancer le container contenant adminer : 
```
docker run -d -e POSTGRES_DB='tp1' -e POSTGRES_USER='root' -e POSTGRES_PASSWORD='root' --name postgres-app --rm -p 5050:5432 --network=app-network  -v /data:/var/lib/postgresql/data postgres-image
```
Le -d permet de lancer le container en arrière plan.
Le -e permet de passer des variables d'environnements, affecter les variables au moment du docker run permet de lancer deux postgres avec des users, bases de données différentes.
Le -v permet la persistence de nos données. En effet, si on redemarre notre container, on aura toujours les données que l'on a précédément inséré grace à cet argument.
