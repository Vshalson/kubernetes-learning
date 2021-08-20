
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
Using Minikube, get the IP and Port through which we can access the service.
`minikube service kubia-http`

## Horizontal scalling the application

Get pod details by deployment
1. Get pod details by deployment
	`kubectl get deploy`
2. Scale up number of replicas of pod
	`kubectl scale deploy kubia --replicas=3`
	- Check result from step 1.
	`kubectl get pods`
3. Display the Pod IP and Pods Node
	`kubectl get pods -o wide`
4. Access Dashboard using Minikube
	`minikube dashboard`
5. Yaml descriptor for pod
	Create file kubia-manual.yaml
	```yaml
	apiVersion: v1
	kind: Pod
	metadata:
	name: kubia-manual
	spec:
	containers:
	- image: legend001/kubia
		name: kubia
		ports:
		- containerPort: 8080
		protocol: TCP
	```
	Type resource of Pod, single container on kubia image and listen on port 8080
6. Create pod from yaml file
	`kubectl create -f kubia-manual.yaml`
	Here `kubectl create -f` used to create any resource from yaml or json

7. Forward a local Network to a port in POD, used when you want to talk to specific pod without going through service(for debugging or other reason)
	`kubia port-forward kubia-manual 8888:8888`

## Labels

Organize Pods and all other kubernetes object through labels. Labels are key-value pair attached to resource.
	Specify label when creating pod ++ {metadata: |-> labels: |-> creation_method: manual env:prod}
	

```bash
# List all labels
kubcetl get po --show-labels

# List specific labels using -L
kubectl get po -L creation_method,env

# Adding labels to exisitng pods
kubectl label po kubia-manual creation_method=manual

# Modifying labels to existing pods(Use --overwrite flag)
kubectl label po kubia-manual-v2 env=debug --overwrite

# List pods using label selector (focus on manual)
kubectl get po -l creation_method=manual

# List pods don't have specific label (here `env` label)
kubectl get po -l '!env`
```

## Namespace to group resources
Labels organize pods & objects in group but each object have multiple label, those can overlap, Namespaces enable to separate resources that don't belong together into nonoverlapping groups.

1. List namespaces

	`kubectl get ns`
2. List pods under specific namespace

	`kubectl get po -n kube-system`
3. Create namespace
	First create a file : custom-namespace.yaml
	```yaml
	apiVersion: v1
	kind: Namespace
	metadata:
		name: custom-namespace
	```
	`kubectl create -f custom-namespace.yaml`
4. Manage object in namespace

	`kubectl create -f kubia-manual.yaml -n custom-namespace`
5. Thought on namespace 
	Namespace allow to isolate objects into groups which allow to operate on those specific namespace but it's not compulsory to provide network isolation.. you can achieve using some networking solution.
6. Deleting pods
	```bash
	# Delete pod by name
	kubectl delete po kubia-manual
	# Delete pod by label , label selector and namespace
	kubectl delete po kubia-manual
	kubectl delete po -l creation_method=manual
	kubectl delete ns custom-namespace
	# Delete all pods
	kubectl delete po --all
	# But above comamnd doesn't delete all resource as `kubectl run` launch `ReplicationController`  that create pods as soon as any pod deleted.
	# Delete everything
	kubectl delete all --all
	```









## Notes

* Learning and Following from : https://github.com/knrt10/kubernetes-basicLearning 

* Chapter-wise hands-on and scenario based stuff to learn sec side of kubernetes, looking to dive in sometime soon. Learning exploitation first is my way to understand stuff. 
https://github.com/PacktPublishing/Learn-Kubernetes-Security


