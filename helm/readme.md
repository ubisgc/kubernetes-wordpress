# Helm 3
Helm kan instaler alle typer Kubernetes objecter, og den kan sammle det i en packed kaldet et chart,
så man kan sige at den automatiser det at køre, alle de Kubectl apply -f .. , det skal køre for at
installer et system på Kubernetes.
Oplysninger om hvad Helm skal installer er beskrevet i et chart som består at template og value filer i yaml format som ligger i en
fast lagt fil struktur.
Helm bruger Go template, nginx-chart folder  er der en sub folder templates som indholder en masse yaml filer, i yaml filer ser 
man en masse content spots f.eks fra service.yaml {{ .Values.service.nodePort }}, dette spot vil blive udfylde med det som står i 
value.yaml filen unser service: of nodeport: alså værdien 30950. Ved at køre helm template commandoen kan få liste alle filer
efter at replacment er sket.
```
helm template mgc-nginx helm/nginx-chart
```


Charts er en packet med alle de oplysninger der skal bruges for at installer en givet system på Kubernetes, de kan gemmes i
et repro, som typisk bare er et git repository.

Et af disse repros udstilles af Bitnami som er en orgaisation som levere deployments af opensource produkter til
forskeliig platforme.
https://bitnami.com/

Helm clienten bruges fra ens lokale maskine, så først logger man ind i det Kubernets context altså den Kubernetes server
man vil installer i og derefter køre man sin Helm command.

## Install Nginx web server
Dette afsnit beskriver hvordan vi kan installer Nginx i Kubernete(Kind), ved at bruger de forskellige tools og tekninger 
der er blevet gennem gået i de tidliger afsnit.  
Opgave tager udgang punkt i denn block.  
https://www.containiq.com/post/helm-charts  

Chartet som installere kan findes Bitnamis git
https://github.com/bitnami/charts/tree/master/bitnami/nginx


Start mgc-tools i en powershell terminal
cd to kubernetes-worpress folderen
```
docker start -i mgs-tools
```
Start en ny powershell terminal og lave en Kubernetes server med Kind
cd to kubernetes-worpress folderen
```
kind create cluster --name mgc-nginx  --config=/helm/kind/config.yaml
``` 
Skift til mgc-tools powershell terminalen og kør helm komandoen.
```
kubectl create namespace mgc-namespace
helm install mgc-nginx --namespace mgc-namespace helm/nginx-chart
kubectl get pods -n mgc-namespace
```
Der køre nu en nginx pod i Kubernetes i namespace mgc-namespace

Test fra en browser, http://127.0.0.1:30950 

Hvis man ser i Kind config kan man se at der er en extraPortMappings
```
extraPortMappings:
- containerPort: 30950
hostPort: 30950
listenAddress: "127.0.0.1"
protocol: TCP
```
og i helm values.yaml er opsætning til kunernetes servicen 
```
service:
  type: NodePort
  port: 80
  nodePort: 30950
```
Dette er en meget simpel og statisk måde at exporte servicen til hostens network normalt vil man bruge en Ingres controler
men mere om det senere

```
helm list -n mgc-namespace
```
For at helm kan list vise de releases af charted som er installlerde, skal man angive det namepsace hvor de er deployet.
Helm list viser nu den en release der finde og man kan se status og revision. Status er deployed
og revision er 1, hvis man kørte en helm upgrade for at deploy en ny version, så ville man have 2 revision.

helm history angry-bird
```
helm history mgc-nginx -n mgc-namespace
```
Vise history og hvis helm fejler vil der i description stå en fejl meddelse 
```
helm rollback mgc-nginx  1 -n mgc-namespace
helm history mgc-nginx -n mgc-namespace
```
Vil lave en rolle back til tidligere version, i dette til fælde giver det ingen mening da der kun er en revison.

Prøv at gå inde i value.yaml file og ændre  replicaCount: 1 til  replicaCount: 3
```
helm upgrade mgc-nginx --namespace mgc-namespace helm/nginx-chart
kubectl get pods -n mgc-namespace
```
Du har nu 3 pods kørende
```
helm history mgc-nginx -n mgc-namespace
helm rollback mgc-nginx  1 -n mgc-namespace
kubectl get pods -n mgc-namespace
helm history mgc-nginx -n mgc-namespace
```
Du kan nu se at pods terminere og man ender med igen kun at have 1 pod, og i history kan man se at der er lavet rolle back
til revision 1.

```
helm uninstall mgc-nginx -n mgc-namespace
helm history mgc-nginx -n mgc-namespace
```
Test fra en browser, http://127.0.0.1:30950  
Servicen er du fjernet og al hvad charted oprettede af objecter er slettede.

I powershell terminalen som er i hosten
```
kind delete cluster  --name mgc-nginx 
kind get clusters
``` 