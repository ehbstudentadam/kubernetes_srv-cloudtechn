# Kubernetes werkstuk

Het doel van dit werkstuk is om aan te tonen dat je de mogelijkheid bezit om de nieuwste technologieën te gaan aanwenden om een project futureproof te maken. Het project dat je op deze manier aanpakt is vrij naar eigen keuze (en mag eventueel een leeg dummyproject zijn), maar er zijn wel enkele basisvoorwaarden waar je project aan moet voldoen:

Het is de bedoeling dat je minstens gebruikt maakt van een database  
- [x] Je mag zelf kiezen wat voor soort database je zal gebruiken -> mysql
- [x] Zorg ervoor dat je databaseserver de principes die tijdens dit vak aan bod gekomen zijn toepast
  - Correcte beveiliging/permissions/...
  - Backups
  - Mogelijk onderhoud uitvoeren
  - ...

- [x] Splits je project op in de nodige containerizable delen (frontend, backend, database, api, ...)
- [x] Zorg ervoor dat je project op een Kubernetes-cluster zou kunnen gedeployed worden
  - Indien het praktisch onmogelijk is om je applicatie effectief naar de cloud te deployen, zorg ervoor dat deze werkt binnen minikube
  - Bewaar alle config-files en mogelijke commando's die je moet uitvoeren om je werkstuk op een blanco minikube install te deployen
- [x] Hoe rekening met de beveiliging van je project. We gaan ervan uit dat de codebase veilig is, dus focus je op de beveiliging van de implementatie
  - [x] Geen "wildcard" access tot je database van buitenaf
  - [x] Block alle poorten die niet in gebruik zijn
  - [x] SSL certificaat waar nodig (selfsigned of via letsencrypt is voldoende)
 

Naast de praktische implementatie vraag ik om ook onderstaande dingen voor te bereiden:

- [x] Indien je niet effectief naar de cloud hebt gedeployed
  - Bij welke cloud service provider had je je project kunnen deployen?
  - Welke producten zou je nodig hebben gehad om dit te doen?
  - Welke kosten zijn er verbonden aan het deployen van je applicatie?
- [x] Indien je had gewenst dat je project zichzelf zou herdeployen nadat je een nieuwe versie van de sourcecode zou pushen naar je repository, hoe zou je dit kunnen aanpakken?
  - Welke stappen worden er doorlopen?
  - Wat is een praktisch voorbeeld van een tool dat je daarbij zou kunnen helpen om dit te verwezenlijken?

-> CI/CD Pipeline using Github Actions [guide here](https://medium.com/@paul.jaworski/deploy-a-spring-boot-application-to-an-azure-kubernetes-cluster-using-github-actions-56bfd5181e18)

-> CI/CD Pipeline

## Documentation

### Tutorials
youtube [kubernetes tutorial](https://www.youtube.com/watch?v=s_o8dwzRlu4)

### Project files
I have containerised my webapplication for this course. The application is a petstore webapp with a mysql database. I had connected a phpmyadmin container as well to manage the database. More information can be found on my [github repository](https://github.com/ehbstudentadam/petstore_docker)

I have successfully depolyed the containers to Azure Kubernetes with my Azure For Students subscription using the `azure-kub.yaml` file. The kubernetes cluster in use is a DEV/Test preset config, the Production Standard did not allow me to configure a cluster (Presumably not in subscription)

#### Problems
- My docker compos points to a dockerfile that builds an image, which kubernetes is not capable of doing.
  - build the Dockerfile and push to dockerhub repository [tutorial](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
  - docker image [here](https://hub.docker.com/repository/docker/alendoc/java-petstore/general)
- The exposed port needed to be changed from 6868  to 8080 in that image
- Use of loadbalance service instead of nodeport
- SSL cert configurations
- Ingress affinity because of sessionproblems

I came accross this [guide](https://kubernetes.io/docs/tasks/configure-pod-container/translate-compose-kubernetes/) to Translate a Docker Compose file with [Kompose](http://kompose.io)
- It's shit


## Configuration

Start minikube  
`minikube start`

Enable ingress  
`minikube addons enable ingress`

Custom Resource Definitions (CRDs) required by cert-manager need to be installed. Cert-manager uses CRDs to define and extend resources like ClusterIssuer. You can do this by applying the cert-manager manifests that include the necessary CRDs. Here's how you can install cert-manager:

Create the cert-manager namespace  
`kubectl create namespace cert-manager`

Install the cert-manager CRDs  
`kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.12.7/cert-manager.crds.yaml`

Install cert-manager  
`kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.12.7/cert-manager.crds.yaml`

Apply configurations  
`kubectl apply -f .\complete-deployment.yaml`

Wait untill everyting is pulled and deployed  
`kubectl get all`

Change hostfile in windows
1. Open notepad.exe as administrator
2. Open `C:\Windows\System32\drivers\etc\hosts`
3. Add line `127.0.0.1 student.ehb.petstore.com` and save

Open ingress tunnel  
`minikube tunnel`

Browse to `https://student.ehb.petstore.com/`

##
```
kubectl delete -f .\petstore-app.yaml
kubectl delete -f .\mysql-database.yaml
kubectl delete -f .\secrets.yaml
kubectl delete deployments --all
kubectl delete services --all
kubectl delete pods --all
kubectl delete daemonset --all

kubectl apply -f .\secrets.yaml
kubectl apply -f .\mysql-database.yaml
kubectl apply -f .\petstore-app.yaml

kubectl delete -f .\azure-kub.yaml
kubectl delete -f .\cert.yaml
kubectl apply -f .\azure-kub.yaml
kubectl apply -f .\cert.yaml

kubectl delete -f .\complete-deployment.yaml
kubectl apply -f .\complete-deployment.yaml

kubectl get all

kubectl logs {pod-name} -f

minikube service {loadbalancer-name}

kubectl describe pod

kubectl get pod x --show-labels

kubectl get node -o wide
minikube ip
```

To remove all Ingress controllers in Minikube, you can follow these steps:
```
minikube stop
minikube delete
minikube start
```

##
Om ervoor te zorgen dat je project zichzelf opnieuw implementeert (herdeployt) nadat je een nieuwe versie van de sourcecode hebt gepusht naar je repository, kun je een Continuous Integration (CI) en Continuous Deployment (CD) pipeline instellen. Hier zijn de algemene stappen die je zou kunnen volgen:

1. Versionering van je Sourcecode:  
Zorg ervoor dat je je sourcecode versieert. Dit kan worden gedaan met behulp van versiebeheerstools zoals Git. Bij elke nieuwe versie of wijziging, verhoog je de versienummering.

2. CI/CD-tool selecteren:  
Kies een CI/CD-tool die geschikt is voor je Kubernetes- en Minikube-omgeving. Populaire tools zijn Jenkins, GitLab CI/CD, Travis CI, CircleCI, etc.

3. Pipeline-configuratie:  
Configureer je CI/CD-pipeline om automatisch te worden geactiveerd wanneer er wijzigingen in de repository worden gedetecteerd. Je zou triggers kunnen instellen op basis van nieuwe commits in de Git-repository.

4. Build-fase:  
In de build-fase bouw je de nieuwe versie van je Docker-image. Dit kan worden gedaan met behulp van de Dockerfile in je project.

5. Test-fase:  
Voer alle nodige tests uit om de kwaliteit en stabiliteit van de nieuwe build te waarborgen. Dit kan unit tests, integratietests, en andere tests omvatten die relevant zijn voor je applicatie.

6. Push naar Container Registry:  
Nadat de build en tests succesvol zijn uitgevoerd, push je het nieuwe Docker-image naar een container registry, bijvoorbeeld Docker Hub of een private registry.

7. Update Kubernetes-configuratie:  
Bij het pushen van de nieuwe versie, kun je de Kubernetes-configuratie bijwerken met het nieuwe Docker-image tagnummer.

8. Herdeploy naar Kubernetes:  
Automatiseer de implementatie naar Kubernetes. Dit kan worden gedaan door kubectl-commando's te gebruiken in je CI/CD-script om de nieuwe versie naar Minikube te implementeren.

Hier is een vereenvoudigd voorbeeld van een CI/CD-script (bijvoorbeeld in een Jenkinsfile voor Jenkins):
```
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                script {
                    // Bouw Docker-image
                    sh 'docker build -t my-petstore:latest .'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    // Voer tests uit
                    sh 'npm test'
                }
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    // Push naar container registry
                    sh 'docker push my-registry/my-petstore:latest'
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                script {
                    // Update Kubernetes-configuratie en herdeploy
                    sh 'kubectl set image deployment/petstore-webapp petstore-webapp=my-registry/my-petstore:latest'
                }
            }
        }
    }
}

```