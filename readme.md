# Running Wordpress på kubernetes
I dette project genemgåes hvordan man kan få Wordpress til at køre på Kubernetes.
Følgende tool vi blive brugt :
* docker
* Kind
* Helm 3
* kubectl

Der vil først være en gennem gang af de forskellige tools, med nogle få test opgaver for
lære og få lidt erfaring med at bruge disse tools.

Derefter vil der være en gennemgang af at installer Wordpress i Kind Kubernetes cluster, der 
vil der opså være en gennemgang af hvordan man builder sit eget image med egen udviklede PHP plugins.

Tilsidst laves der en Minikube med Wordpress, PHP plugins, Prometheus og Grafana med et kubectl dashboard.

## Lave et docker tools image
Lav et docker image som ind holder de forskellige tools der skal bruges.  
Følgende tools installes i et images genne docker.tools filen.
* bash
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

## CI/CD pipeline
CI/CD er en metode til hyppigt at levere apps til kunder ved at introducere automatisering i appudvikling. 
De vigtigste begreber, der tilskrives CI/CD, er kontinuerlig integration, kontinuerlig levering og kontinuerlig implementering.

Pipiline vil blive udviklet i Gitub actions, som vil blive afviklet når udvikler levere code til deres GitHub repositories.
Der vil køre pipelined når følgende envents sker i Github
* Push til branch
* Create pull request
* Push til Main

Der vi være pipelines på følgende artifacter.
* Repositories som indeholder Wordpress plugins
* Repositories(Plugin release repro) som indeholder de packede plugins(zip fil for hvert plugin), der kunne f.eks være en branch for hver verson af systemet.

Hvad kommer en pipeline i et repro med Wordpress plugin til at bestå af
* pack af plugin til zip files
* Docker build, nyt docker image med plugin installeret
* push docker image til docker hub
* Start en kinde
* Install Wordpress via helm chart, med image fra docker hub
* Test plugin ved at bruge Selenium 
* Hvis push to main og test ok, push pakkede pluign til til repro (Plugin release repro)

Hvad kommer Repositories(Plugin release repro)til at bestå af
* Docker build, nyt docker image med alle plugins installeret
* push docker image til docker hub
* Start en kinde
* Install Wordpress via helm chart, med image fra docker hub
* Integration test af alle plugins ved at bruge Selenium
* Hvis pull request og test ok, approved push til main
* Hvis push to main og test ok, push new release to docker hub
