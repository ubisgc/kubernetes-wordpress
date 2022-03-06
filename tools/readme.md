# Lave et docker tools image
Images bygges på apline som er et minimal base image af linux : https://hub.docker.com/_/alpine

For bygge det nye tools image kør følgende komandore i powershell :

cd til project folder kubernetes-worpress

```
docker build -f ./tools/docker.tools -t mgc/tools .
```

Test det nye image med følgende
```
docker images
```
Du skal nu se et nyt image i liste over images

For at test det nye image:
cd til project folder kubernetes-worpress
```
docker run -it --rm --net host -v ${HOME}/.kube/:/root/.kube/ -v ${PWD}:/work -w /work mgc/tools bash
```
Hvad sker der i denne commando  

| Parameter                      | Description                                                                             |
|:-------------------------------|:----------------------------------------------------------------------------------------|
| -it                            | Start images i interactive bash shell                                                   |
| --name mgc-tools               | Gemmer denne image under et name incl de angivet parametre                              |
| --net host                     | Connecter det kørende image til hostens network                                         |
| -v ${HOME}/.kube/:/root/.kube/ | Mounter kube contex så man kan nå en kubernetes installation inde fra det kørende image |
| -v ${PWD}:/work                | Mounter current directory ind i det kørende image                                       | 

Når docker run er kørt står du i en bach promt i det kørende image
```
ls -l
```
Man ser nu nu deet directory som du stod i da du kørte docker run altså directory kubernete-wordpress.
```
helm
kubectl
```
Du kan se at de to tools findes i det kørende image
```
exit
```
Prøv nu at starte mgs-tools igen med denne command

```
docker start -i mgs-tools
```

Kør command i bash promt 
```
ls -l
```
Som du ser er de volume mount der blev angive ved docker run stadig er der
