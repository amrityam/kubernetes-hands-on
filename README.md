# kubernetes-hands-on

**Prerequisites:**
* Docker
* kubernetes-cli (kubectl) : https://kubernetes.io/docs/tasks/tools/install-kubectl/
* minikube : https://minikube.sigs.k8s.io/docs/start/ 
* Virtualbox version 5.2+ ([or other minikube compatible hypervisors][minikube-hypervisors])

As an alternative if you are using Windows/Mac system, you can install docker desktop which provides native kubernetes support.

**References:**

Install K8 through Docker desktop for Windows: https://birthday.play-with-docker.com/kubernetes-docker-desktop/

Install K8 through Docker desktop for Mac: https://medium.com/backbase/kubernetes-in-local-the-easy-way-f8ef2b98be68


**demo-voting-app Architecture**

![arc_diagram!](/images/voting-app-architecture-diagram.png)


![K8_diagram!](/images/voting-app-pods-services.png)

**Deployment Architecture**

![K8_deployment_diagram!](/images/voting-app-deployment.png)

* A front-end web app in Python which lets you vote between two options
* A Redis queue which collects new votes
* A .NET Core worker which consumes votes and stores them in Postgres database
* A Postgres DB which store the vote count from worker
* A Node.js webapp which shows the results of the voting in real time

## Run the appplication in Kubernetes ##
------------------------------------------
Clone this repo:
git clone https://github.com/amrityam/kubernetes-hands-on.git

First create the demo-voting-app namespace:
```
kubectl create namespace demo-voting-app
```

Run the following command to create the deployments and services objects:
```
cd kubernetes-hands-on\demo-voting-app\k8-specifications

kubectl create -f deployments\voting-app-deployment.yaml
kubectl create -f services\voting-app-service.yaml

kubectl create -f deployments\redis-deployment.yaml
kubectl create -f services\redis-service.yaml

kubectl create -f deployments\postgres-deployment.yaml
kubectl create -f services\postgres-service.yaml

kubectl create -f deployments\worker-app-deployment.yaml

kubectl create -f deployments\result-app-deployment.yaml
kubectl create -f services\result-app-service.yaml


```
Now validate all the deployments and pods are running fine by using below command.
```
kubectl get deployments,pods,svc --namespace demo-voting-app
```
![validate_pods_deployments!](/images/validate_pods_deployments.png)

The voting-app is now available on port 30004 and the result-app one is running on port 30005.


![voting-app!](/images/voting-app.png)

![result-app!](/images/result-app.png)

## Scale up the deployments:
```
kubectl scale deployments voting-app-deploy --replicas=3 --namespace demo-voting-app
```
![scale-up!](/images/scale-up.png)

Now refresh the voting-app page and you can notice each time the page is served by a new pod.

![scale-up-pod1!](/images/scale-up-pod1.png) 
![scale-up-pod2!](/images/scale-up-pod2.png)

## Clean up resources and delete namespace:
To stop the application and delete all resources run the below command which deletes the namespace we have created earlier.

```
kubectl delete namespace demo-voting-app
```
