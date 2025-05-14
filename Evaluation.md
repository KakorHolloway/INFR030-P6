# Evaluation INFR030

## Introduction 

Le but de cet évaluation est de permettre de confirmer que vous avez pu apprendre les différentes notions vu dans les 5 exemples et exercices. 

Pour se faire il faudra prouver que vous êtes capables de déployer la solution demandée sur mon cluster Openshift. 

Vous avez le droit à ce cours. Une page fonctionnelle et les bons éléments sur mon cluster me permettra de vous noter par groupe. 

## Consignes

Le but de cet exercice sera de créer un environnement avec un phpmyadmin et un mysql. 

De fait vous devrez faire ces éléments :

- Créez un deployment avec l'image phpmyadmin 
- Créez un deployment d'un seul réplicat avec MySQL
- Créez le service mysql qui permettra une connexion au deployment MySQL
- Créez un secret sur le deployment mysql pour avoir en plus le mot de pase root et le mot de passe user
- Ajoutez à ce dernier les variables d'environnement pour créer une database et un user standard (aide sur le nom des valeurs dans https://hub.docker.com/_/mariadb) 
- Créez le service phpmyadmin 
- Créez l'ingress lié à phpmyadmin et testez de vous connecter au site 

Envoyez une capture d'écran du site en fin de TP qui montre que vous arrivez bien à accéder à la db depuis phpmyadmin. 

