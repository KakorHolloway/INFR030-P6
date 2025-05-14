# IINF300
## Connection au cluster Openshift
Pour vous connecter au cluster Openshift mis à disposition : 

### Pour Windows

Téléchargez sur votre machine le paquet OKD suivant : 
https://github.com/okd-project/okd/releases/download/4.15.0-0.okd-2024-03-10-010116/openshift-client-windows-4.15.0-0.okd-2024-03-10-010116.zip

Copiez le fichier oc.exe dans C:\Windows\System32

### Pour Linux 
Téléchargez sur votre machine le paquet OKD suivant : 
https://github.com/okd-project/okd/releases/download/4.15.0-0.okd-2024-03-10-010116/openshift-client-linux-4.15.0-0.okd-2024-03-10-010116.tar.gz

Dézippez et copiez le fichier oc dans /urs/bin
```
tar -xvf openshift-client-linux-4.15.0-0.okd-2024-03-10-010116.tar.gz
cp oc /usr/bin/
```

### Pour tous les OS

Allez sur l'url https://console-openshift-console.apps.openshift.kakor.ovh authentifiez-vous avec l'utilisateur ipi-gp-x (le x étant le numéro de groupe) en choisisant "KeystoneIDP".

Une fois authentifié (attention à ne pas recharger la page même si l'affichage prends du temps), allez en haut à droite de la page et sélectionnez l'option "Copy login Command" et réauthentifiez vous. 

Cliquez sur le lien Display Token et copiez dans votre terminal sur VScode la ligne de commande qui à été donné avec oc. 

Pour vérifier que vous êtes authentifiés, lancez la commande ```oc get pod```

## Exemples 1

Listez des pods
```
oc get pod 
oc get pod -n <nomdunamespace>
oc get pod
oc get pod oauth-openshift-5d6b6c6576-6xdsm -n openshift-authentication -o json 
```
Création d'un pod simple (https://kubernetes.io/docs/concepts/workloads/pods/ ):

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
    securityContext:
      allowPrivilegeEscalation: true
```

Pour déployer :
```
oc apply -f exemple1/pod.yaml
```

Afin de dégub un pod et de vérifier les logs :

```
oc logs <nomdupod> -n <namespace>
```

Pour supprimer :
```
oc delete pod <nomdupod>
oc delete -f exemple1/pod.yaml
```

## Exemple 2 : Exposer son application 

D'abord on va vérifier qu'en local, nginx s'exécute bien :

```
oc exec -it nginx -- /bin/sh 
....
exit
```

Mise en place d'un label obligatoire sur le pod nginx pour le service :
```
labels:
  app: nginx
```

Création du service pour que le selector corresponde au label app: nginx :

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  selector:
    app: nginx # label du pod cible
  ports:
    - protocol: TCP
      port: 80 # port sur le pod à adresser
      targetPort: 80 # port d'accès au service
```
Pour créer le service :
```
oc apply -f exemple2/service.yaml
oc get service #pour confirmer
oc get endpoints # pour vérifier que le service est bien connecté à un port
```
### Exercice 1 :
Créez un pod nginx, ajoutez le label "test: infr030" 

Associez un service à ce pod nommé nginx-svc. 

Créez un pod à partir de l'image harbor.kakor.ovh/public/curl:latest 
(n'oubliez pas de mettre un sleep 3600 pour que le conteneur ne s'éteigne pas, regardez la documentation sur les command sur kubernetes.io afin de mettre ce sleep 3600)

Pour vous connecter au pod la commande c'est :
```
oc exec -it <nomdupod> -- /bin/sh #ou /bin/bash
oc rsh <nomdupod>
# exit ou Ctrl + D pour quitter
```
Testez à partir de ce pod l'accès au service nginx-svc

### Exercice 2

A partir de l'exemple 3 et de l'exercice 1.

Créez l'ingress qui va vous permettre d'accéder à votre service nginx via le nom :
groupe-<numerodevotregroupe>.apps.openshift.kakor.ovh

Vérifiez le fonctionnement de votre ingress en modifiant la page d'accueil de votre pod nginx

## Exemple 4 : Créer des configsmaps et des secrets

### Exercice 3

A partir de votre pod nginx, avec son service et l'ingress que vous aviez configurés lors de la dernière séance, créez une configmap qui va contenir un fichier "index.html" que vous monterez sur le pod nginx. 

Si vous avez un fichier html complexe, vous pouvez utiliser la commande ```oc create configmap --from-file....```

Pour vous aider voici la documentation Kubernetes vous permettant de monter la configmap :
https://kubernetes.io/docs/concepts/configuration/configmap/

Correction avec commande configmap :
oc create configmap index-cm --from-file=index.html=index.html --from-file=image.png=image.png --dry-run=client -o yaml > cm.yaml

### Les secrets

Les secrets sont des objets Kube qui ont pour vocation de stocker des info sensibles. 

Il en existe 3 types principaux :
- Generic (stocker de la valeur standard)
- tls (pour les certificats)
- dockerconfigjson

## Exemple 5 Les deployments

(cf dossier deployment pour un exemple avec httpd)

### Exercice 4

Mettez en place un nouveau deployment, celui-ci doit se nommer mariadb. 
Ce deployment doit posséder 1 replicas uniquement et utiliser l'image ```mariadb:10.5``` . 
Montez sur ce deployment un secret nommé "mysql-password" qui permettra de stocker le mot de passe de la base de donné du deployment via la variable d'environnement MYSQL_ROOT_PASSWORD

Une fois que le pod créé est en Running, modifiez le deployment pour utiliser la version mariadb:10.6. 

Une fois en Runnning mettez l'image mariadb:18.58. Puis revenez à la version précédente du deployment sans modifier le fichier yaml. 