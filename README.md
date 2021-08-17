
# Kubernetes Learning

## Create web server

 1. Create App.js
 2. Create Dockerfile
 3. Run container Image
    `docker run --name kubia-container -p 8080:8080 -d kubia`
    
## Build Docker Image

1. Access application
	`curl localhost:8080`
2. Running shell inside existing container
	`docker exec -it kubia-container bash`
3. Stop and remove container
	```bash
	docker stop kubia-container # Stop main process running in container but contianer still exists.
	docker rm kubia-container # Truly remove container
	```

## Push Image to DockerHub

To allow image run over any machine, push image to external image registry.
1. First tag the image
	`docker tag kubia legend001/kubia`
2. Push Image
	`docker push legend001/kubia`

## Kubernetes Bhaisahab!!

Big software - Monoliths
Independent running component - Microservices
	
-  Microservice decouple from each other can be developed, deployed, updated and scaled individually.
-  But with great number (of microservices) comes great difficulty to keep whole system running smoothly.
-  It's harder to know how to put components in place for efficient resource utilization and keep hardware course down
- We need :
	- automation
	- automatic config
	- supervisiona 
	- failure handling

Here Kubernetes entered, it helps developers to deploy apps themselves and Operations team to monitore the application.

## Running local single node with Kubernetes cluster with Minikube

1. Start Kubernetes cluster with minikube
	`minikube start`
2. To check if cluster is up?
	`kubectl cluster-info` 
3. Deploy node app
	`kubectl create deployment kubia --image=legend001/kubia --port=8080`
4. Pods contain one or more container(which are not standalone kubeneters object), to list pod
	`kubectl get pods`
5. Access web application, each pod have own IP addr which internal to cluster and to access it we have to expose it through `Service object`. Here we'll create special service type `LoadBalancer`, using it we can connect to pod through Load Balancer public IP.
	a. Creating a service object
		`kubectl expose deploy kubia --type=LoadBalancer --name kubia-http`
	b. List services
		`kubectl get svc`

Minikube not support LoadBalancer Services, so there will be no external IP.






## Notes

* Learning and Following from : https://github.com/knrt10/kubernetes-basicLearning 

* Chapter-wise hands-on and scenario based stuff to learn sec side of kubernetes, looking to dive in sometime soon. Learning exploitation first is my way to understand stuff. 
https://github.com/PacktPublishing/Learn-Kubernetes-Security


