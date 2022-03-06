# Install Wordpress
Det helm char der bruger her kommer fra git https://github.com/bitnami/charts, det er clonet til
local filsystem, hvor efter Wordpress chart er kopieret ind i kunernetes-wordpress projectet, så
det er nu en local kopi af chartet der arbjedes på.

## Under søg chartes
Start mgc-tools i en powershell terminal
cd to kubernetes-worpress folderen
```
docker start -i mgs-tools
```

Start med at se i /wordpress-chart/Chart.yaml, er kan man se forskellig oplysninger om hvad 
charted indeholder.
* Wordpress 13.0.22
* mariadb, måske ikke lige den vi gerne vil bruge men lad os se på det senere
* keywords, php

I msg-tools kør
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm dependency update wordpress/wordpress-chart
helm template mgc-wordpress wordpress/wordpress-chart
```
| Command | Description                                                     |
|:--------|:----------------------------------------------------------------|
| helm repo add      | Add bitnami to local repro                                      |
|helm dependency update| Updatere de depentencyes der står i /wordpress-chart/Chart.yaml |
|helm template| lave en gennerer alle vil med de værdier der står i value.yaml  |

I output fra helm template markere --- starten på en fil og kind: angiver hvile kubernetes object der oprette med
den konkrete fil.
Læs /wordpress-chart/README.md, efter at have læse dette er der en ændring og der er at aktivere Ingres.  
Som du kan se er der en del filer og chartet er rimelig komplex, men lade os forsætte og se hvad der sker.


## Install
### Klargør Kind Kubernetes 
```
kind create cluster --name mgc-wordpress  --config=./wordpress/kind/config.yaml
``` 
### Install nginx som Ingress controler
``` 
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
``` 
Ingress controler er en proxy som modtager alle request som kommer indtil clusteret og sender dem til de 
services som skal behandle requstet.

### Install wordpress
I filen /wordpress-chart/value.yaml ændre vi
```
ingress:
## @param ingress.enabled Enable ingress record generation for WordPress
##
enabled: false <-- true
```
```
kubectl create namespace mgc-wordpress
helm install mgc-wordpress --namespace mgc-wordpress --set wordpressUsername=admin --set wordpressPassword=adminpw --set mariadb.auth.rootPassword=rootpw wordpress/wordpress-chart
```
Svar
```
** Please be patient while the chart is being deployed **

Your WordPress site can be accessed through the following DNS name from within your cluster:

    mgc-wordpress.mgc-wordpress.svc.cluster.local (port 80)

To access your WordPress site from outside the cluster follow the steps below:

1. Get the WordPress URL and associate WordPress hostname to your cluster external IP:

   export CLUSTER_IP=$(minikube ip) # On Minikube. Use: `kubectl cluster-info` on others K8s clusters
   echo "WordPress URL: http://wordpress.local/"
   echo "$CLUSTER_IP  wordpress.local" | sudo tee -a /etc/hosts

2. Open a browser and access WordPress using the obtained URL.

3. Login with the following credentials below to see your blog:

echo Username: admin
echo Password: $(kubectl get secret --namespace mgc-wordpress mgc-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)
```
Set host file på windos til wordpress.local, husk man skal være admin
```
C:\Windows\System32\drivers\etc\hosts
127.0.0.1 wordpress.local
```
Test i browser WordPress URL: http://wordpress.local/
* http://wordpress.local/
* http://wordpress.local/wp-admin

Forskellige comands til at hjælpe ved debug
```
helm upgrade mgc-wordpress --namespace mgc-wordpress --set wordpressUsername=admin --set wordpressPassword=adminpw --set mariadb.auth.password=rootpw --set mariadb.auth.rootPassword=secretpassword wordpress/wordpress-chart
kubectl get pods -n mgc-wordpress
kubectl describe pods -n mgc-wordpress
kubectl logs mgc-wordpress-mariadb-0 -n mgc-wordpress
kubectl logs ingress-nginx-controller-59cbb6ccb6-pgt8q -n ingress-nginx
kubectl exec --stdin --tty <pods-name> -- /bin/bash
kubectl exec --stdin --tty mgc-wordpress-mariadb-0 -- /bin/bash
kubectl exec --stdin --tty mgc-wordpress-69d55795d8-hfccx -n mgc-wordpress -- /bin/bash
```