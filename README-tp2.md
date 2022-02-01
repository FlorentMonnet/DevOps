# TP 2

## Que fait la commande : 'mvn clean verify' ?

Cette commande permet de lancer les tests d'intégrations et les tests unitaires spécifiés dans le fichier de conf **pom.xml**.

## Qu'est-ce que 'testcontainers' dans le fichier 'pom.xml' ?

Une librairie qui permet de lancer des containers docker au moment de nos tests.

## Pourquoi on utilise des variables sécurisés ?

On utilise ces variables pour éviter que nos données soient récupérées par une personne malveillante. Les secrets de github sont donc la anière manière de gérer les tokens, usernames de notre pipeline.

## Pourquoi le 'needs: build-and-test-backend' est présent ?

Si ce n'était pas le cas, à chaque commit sur les branches main et develop, on aurait nos images qui seraient publiées même si on arrive pas à build notre java, ou si les tests ne passent pas. 

En rajoutant cette ligne, le job est exécuté que si  build-and-test-backend est réussi.

test