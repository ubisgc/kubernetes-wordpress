# Kind

## Instal Kind
```
curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.11.1/kind-windows-amd64
Move-Item .\kind-windows-amd64.exe c:\some-dir-in-your-PATH\kind.exe
```
Tilføje kind.exe til windows $PATH


## Test Kind install
### Operet et simplet cluster
```
kind create cluster --name mgc-kind
```
Hvis du ikke har mgc-tools startede, så start den nu

```
docker start -i mgs-tools
kubectl cluster-info --context kind-mgc-kind
```
Som du ser kan vi du se cluster-info på det Kind cluster der kør på din lokale maskine, det skyldes at dit mgs-tools 
køre med denne parameter -v ${HOME}/.kube/:/root/.kube/

```
kubectl get nodes
```
Som du ser har du en node i dit kubernetes cluster, så det svare til at have en Minikube.
```
kubectl get pods --all-namespaces
```
Som du ser her er det et kubernetes cluster med alle de kendte pods kørende.

På den lokale maskine kør commando docker ps som viser alle kørende containere
```
docker ps
```
Man kan mgc-tools og mgc-kind-control-plane, hvor den sidste er kubernetes.

### Operet et multi node cluster
```
kind create cluster --name mgc-kind-multi --config=./kind/multi-node-cluster/config.yaml
kind get clusters
```

Som du kan se har du nu to kubernetes clustere 
* mgc-kind 
* mgc-kind-multi    

Skift nu til dit mgc-tools og prøv denne command
```
kubectl get nodes --context kind-mgc-kind-multi
```
--context angiver hvilke af de to kubernetes clustre du har kørende, som du vil snakke med og som 
du kan se har du nu 3 noder i dit nye cluster.   

På din lokale maskine kør
```
docker ps
```
Som du kan se har du nu fået 3 nye docker processer
* mgc-kind-multi-worker
* mgc-kind-multi-worker-2
* mgc-kind-multi-control-plane  
Så hver node i dit cluster er blevt et container som er connectet i et cluster.

### Stop og Start af cluster
I Kind kan man ikke stoppe og starte clusteret, i heller ikke med docker stop start det virker ikke,
så man kan du bruge Kind til så hurtige test setup og til at øve sig på forskellige ting.


### Delete cluster
```
kind delete cluster --name mgc-kind
kind delete cluster --name mgc-kind-multi
kind get clusters
```
No kind clusters found.


