# Running Wordpress på kubernetes
I dette project genemgåes hvordan man kan få Wordpress til at køre på Kubernetes.
Følgende tool vi blive brugt :
* docker
* Kind
* Helm 3
* kubectl

Der vil først være en gennem gang af de forskellige tools, med nogle så test opgaver for
låre og få lidt erfaring med at bruge disse tools.

Derefter vil der være en gennemgang af at installer Wordpress i Kind Kubernetes cluster, er 
vil der opså gennemgang af hvordan man builder sit eget image med egen udviklede PHP plugins.

Tilsidst laves der en Minikube med Wordpress, PHP plugins, Prometheus og Grafana med et kubectl dashboard.

## Lave et docker tools image
Lav et docker image som ind holder de forskellige tools der skal bruges.  
Følgende tools installes i et images genne docker.tools filen.
* bach
* kubectl
* Helm 3  

se vejledning [here](tools/readme.md)

## Kind
Kind er et tool til hurtigt at lave lokale kubernetes clustere.
https://kind.sigs.k8s.io/

se vejledning [here](kind/readme.md)

## Helm 3
Helm er en install/upgrade package manager til Kubernetes, man kan sammenligne den med f.eks windows installer, linux apk eller 
RPM packed manager, yum, Homebrew og Chocolatey.
https://helm.sh/docs/

se vejledning [here](helm/readme.md)


## Deploy Wordpress to Kubernetes
For at deploy Wordpress til kubernetes vil vi bruge et helm chart fra Bitnami. 

https://bitnami.com/stack/wordpress

se vejledning [here](wordpress/readme.md)

