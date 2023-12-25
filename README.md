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
- [ ] Hoe rekening met de beveiliging van je project. We gaan ervan uit dat de codebase veilig is, dus focus je op de beveiliging van de implementatie
  - [ ] Geen "wildcard" access tot je database van buitenaf
  - [ ] Block alle poorten die niet in gebruik zijn
  - [ ] SSL certificaat waar nodig (selfsigned of via letsencrypt is voldoende)
 

Naast de praktische implementatie vraag ik om ook onderstaande dingen voor te bereiden:

- [x] Indien je niet effectief naar de cloud hebt gedeployed
  - Bij welke cloud service provider had je je project kunnen deployen?
  - Welke producten zou je nodig hebben gehad om dit te doen?
  - Welke kosten zijn er verbonden aan het deployen van je applicatie?
- [ ] Indien je had gewenst dat je project zichzelf zou herdeployen nadat je een nieuwe versie van de sourcecode zou pushen naar je repository, hoe zou je dit kunnen aanpakken?
  - Welke stappen worden er doorlopen?
  - Wat is een praktisch voorbeeld van een tool dat je daarbij zou kunnen helpen om dit te verwezenlijken?

## Recources

### Tutorials
youtube [kubernetes tutorial](https://www.youtube.com/watch?v=s_o8dwzRlu4)

### Project files
I have containerised my webapplication for this course. The application is a petstore webapp with a mysql database. I had connected a phpmyadmin container as well to manage the database. More information can be found on my [github repository](https://github.com/ehbstudentadam/petstore_docker)


#### Problems
- My docker compos points to a dockerfile that builds an image, which kubernetes is not capable of doing.
  - build the Dockerfile and push to dockerhub repository [tutorial](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- The exposed port needed to be changed from 6868  to 8080 in that image
- Use of loadbalance service instead of nodeport


### Other
I came accross this [guide](https://kubernetes.io/docs/tasks/configure-pod-container/translate-compose-kubernetes/) to Translate a Docker Compose file with [Kompose](http://kompose.io)
- It's shit

I have successfully depolyed the containers to Azure Kubernetes with my Azure For Students subscription using the `azure-kub.yaml` file.The kubernetes cluster in use is a DEV/Test preset config, the Production Standard did not allow me to configure a cluster (Presumably not in subscription)

### Commands
```
minikube start

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
kubectl apply -f .\azure-kub.yaml

kubectl get all

kubectl logs {pod-name} -f

minikube service {loadbalancer-name}

kubectl describe pod

kubectl get pod x --show-labels

kubectl get node -o wide
minikube ip

minikube stop
```